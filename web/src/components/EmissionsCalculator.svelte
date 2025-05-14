<script>
  let calculatorMode = 'specific'; // 'specific' or 'distance'
  let from = '';
  let to = '';
  let distance = 300;
  let passengers = 1;
  let flightClass = 'economy';
  let results = null;
  let loading = false;
  let error = null;

  // Emissions factors (lbs CO2 per passenger-mile)
  const EMISSIONS_FACTORS = {
    FLIGHT_SHORT: 0.44,
    FLIGHT_DOMESTIC: 0.496,
    FLIGHT_INTERNATIONAL: 0.939,
    TRAIN: 0.147,
    BUS: 0.367,
    CAR: 0.513
  };

  // Class multipliers
  const CLASS_MULTIPLIERS = {
    economy: 1,
    premium: 1.6,
    business: 2.9,
    first: 4.0
  };

  // Common city coordinates (lat, lon)
  const CITIES = {
    'PVD': { lat: 41.7242, lon: -71.4282, name: 'Providence' },
    'NYC': { lat: 40.7128, lon: -74.0060, name: 'New York' },
    'BOS': { lat: 42.3601, lon: -71.0589, name: 'Boston' },
    'ORD': { lat: 41.8781, lon: -87.6298, name: 'Chicago' },
    'LON': { lat: 51.5074, lon: -0.1278, name: 'London' },
    'LAX': { lat: 34.0522, lon: -118.2437, name: 'Los Angeles' },
    'SFO': { lat: 37.7749, lon: -122.4194, name: 'San Francisco' },
    'DCA': { lat: 38.8951, lon: -77.0364, name: 'Washington DC' }
  };

  function calculateDistance(lat1, lon1, lat2, lon2) {
    const R = 3959; // Earth's radius in miles
    const dLat = (lat2 - lat1) * Math.PI / 180;
    const dLon = (lon2 - lon1) * Math.PI / 180;
    const a = 
      Math.sin(dLat/2) * Math.sin(dLat/2) +
      Math.cos(lat1 * Math.PI / 180) * Math.cos(lat2 * Math.PI / 180) * 
      Math.sin(dLon/2) * Math.sin(dLon/2);
    const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
    return Math.round(R * c);
  }

  function getEmissionsFactor(distance) {
    if (distance <= 500) return EMISSIONS_FACTORS.FLIGHT_SHORT;
    if (distance <= 3000) return EMISSIONS_FACTORS.FLIGHT_DOMESTIC;
    return EMISSIONS_FACTORS.FLIGHT_INTERNATIONAL;
  }

  function calculateEmissions() {
    loading = true;
    error = null;

    try {
      let calculatedDistance;
      
      if (calculatorMode === 'specific') {
        if (!CITIES[from] || !CITIES[to]) {
          error = 'Please select both origin and destination cities';
          loading = false;
          return;
        }
        calculatedDistance = calculateDistance(
          CITIES[from].lat, CITIES[from].lon,
          CITIES[to].lat, CITIES[to].lon
        );
        distance = calculatedDistance * 2; // Round trip
      }

      const emissionsFactor = getEmissionsFactor(distance/2); // One-way distance for classification
      const flightEmissions = distance * emissionsFactor * CLASS_MULTIPLIERS[flightClass];
      const trainEmissions = distance * EMISSIONS_FACTORS.TRAIN;
      const busEmissions = distance * EMISSIONS_FACTORS.BUS;
      const carEmissions = (distance * EMISSIONS_FACTORS.CAR) / passengers;

      results = {
        distance: distance,
        flight: Math.round(flightEmissions),
        train: Math.round(trainEmissions),
        bus: Math.round(busEmissions),
        car: Math.round(carEmissions),
        from: calculatorMode === 'specific' ? CITIES[from].name : null,
        to: calculatorMode === 'specific' ? CITIES[to].name : null,
        flightType: distance <= 1000 ? 'Short-haul' : (distance <= 6000 ? 'Domestic' : 'International')
      };
    } catch (err) {
      error = 'Error calculating emissions. Please try again.';
    } finally {
      loading = false;
    }
  }
</script>

<div class="calculator-container">
  <h2>Travel Emissions Calculator</h2>
  
  <div class="calculator-modes">
    <button 
      class:active={calculatorMode === 'specific'}
      on:click={() => calculatorMode = 'specific'}
    >
      City to City
    </button>
    <button 
      class:active={calculatorMode === 'distance'}
      on:click={() => calculatorMode = 'distance'}
    >
      Custom Distance
    </button>
  </div>

  {#if calculatorMode === 'specific'}
    <div class="form-group">
      <label for="from">From:</label>
      <select id="from" bind:value={from} class="input">
        <option value="">Select origin city</option>
        {#each Object.entries(CITIES) as [code, city]}
          <option value={code}>{city.name} ({code})</option>
        {/each}
      </select>
    </div>

    <div class="form-group">
      <label for="to">To:</label>
      <select id="to" bind:value={to} class="input">
        <option value="">Select destination city</option>
        {#each Object.entries(CITIES) as [code, city]}
          <option value={code}>{city.name} ({code})</option>
        {/each}
      </select>
    </div>
  {:else}
    <div class="form-group">
      <label for="distance">Round Trip Distance (miles)</label>
      <input 
        type="number" 
        id="distance"
        bind:value={distance}
        min="1"
        class="input"
      />
      <div class="common-distances">
        <p>Common Routes:</p>
        <button on:click={() => distance = 300}>PVD ↔ NYC (300 mi)</button>
        <button on:click={() => distance = 1600}>PVD ↔ Chicago (1,600 mi)</button>
        <button on:click={() => distance = 5000}>PVD ↔ London (5,000 mi)</button>
      </div>
    </div>
  {/if}

  <div class="form-group">
    <label for="flightClass">Flight Class:</label>
    <select id="flightClass" bind:value={flightClass} class="input">
      <option value="economy">Economy</option>
      <option value="premium">Premium Economy</option>
      <option value="business">Business</option>
      <option value="first">First</option>
    </select>
  </div>

  <div class="form-group">
    <label for="passengers">Number of Passengers (for car travel)</label>
    <input 
      type="number" 
      id="passengers"
      bind:value={passengers}
      min="1"
      max="8"
      class="input"
    />
  </div>

  {#if error}
    <div class="error">{error}</div>
  {/if}

  <button class="calculate-button" on:click={calculateEmissions} disabled={loading}>
    {loading ? 'Calculating...' : 'Calculate Emissions'}
  </button>

  {#if results}
    <div class="results">
      <h3>Estimated CO₂ Emissions</h3>
      {#if results.from && results.to}
        <p class="route-info">
          Route: {results.from} to {results.to} ({results.distance} miles round trip)
        </p>
      {/if}
      
      <div class="emissions-comparison">
        <div class="transport-option">
          <h4>Flight ({flightClass})</h4>
          <p class="emissions">{results.flight} lbs CO₂</p>
          <p class="note">{results.flightType} flight</p>
        </div>

        {#if results.distance <= 1000}
          <div class="transport-option">
            <h4>Train</h4>
            <p class="emissions">{results.train} lbs CO₂</p>
            <p class="savings">
              {Math.round((results.flight - results.train) / results.flight * 100)}% less than flying
            </p>
          </div>

          <div class="transport-option">
            <h4>Bus</h4>
            <p class="emissions">{results.bus} lbs CO₂</p>
            <p class="savings">
              {Math.round((results.flight - results.bus) / results.flight * 100)}% less than flying
            </p>
          </div>

          <div class="transport-option">
            <h4>Car ({passengers} {passengers === 1 ? 'person' : 'people'})</h4>
            <p class="emissions">{results.car} lbs CO₂ per person</p>
            <p class="savings">
              {results.car < results.flight 
                ? `${Math.round((results.flight - results.car) / results.flight * 100)}% less than flying`
                : `${Math.round((results.car - results.flight) / results.flight * 100)}% more than flying`}
            </p>
          </div>
        {/if}
      </div>

      <div class="recommendations">
        <h4>Recommendations</h4>
        {#if results.distance <= 500}
          <p>For this short-haul trip:</p>
          <ul>
            <li>Train travel offers significant emissions savings</li>
            <li>Bus travel is also a lower-emission option</li>
            {#if passengers > 1}
              <li>Carpooling with {passengers} people makes car travel a viable option</li>
            {:else}
              <li>Consider carpooling to reduce per-person emissions</li>
            {/if}
          </ul>
        {:else}
          <p>For this {results.flightType.toLowerCase()} trip:</p>
          <ul>
            <li>If flying is necessary, consider:
              <ul>
                <li>Booking economy class (saves up to {Math.round((1 - CLASS_MULTIPLIERS.economy/CLASS_MULTIPLIERS[flightClass]) * 100)}% emissions)</li>
                <li>Choosing a direct flight</li>
                <li>Using a sustainable airline</li>
                <li>Offsetting your emissions</li>
              </ul>
            </li>
          </ul>
        {/if}
      </div>
    </div>
  {/if}
</div>

<style>
  .calculator-container {
    max-width: 800px;
    margin: 0 auto;
    padding: 2rem;
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }

  .calculator-modes {
    display: flex;
    gap: 1rem;
    margin-bottom: 2rem;
    justify-content: center;
  }

  .calculator-modes button {
    padding: 0.75rem 1.5rem;
    background-color: var(--secondary);
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-weight: bold;
    transition: all 0.2s;
  }

  .calculator-modes button.active {
    background-color: var(--primary);
    color: white;
  }

  .route-info {
    text-align: center;
    font-size: 1.1rem;
    margin-bottom: 1.5rem;
    color: var(--dark);
  }

  .error {
    color: red;
    text-align: center;
    margin-bottom: 1rem;
    padding: 0.5rem;
    background-color: #ffebee;
    border-radius: 4px;
  }

  h2 {
    color: var(--dark);
    text-align: center;
    margin-bottom: 2rem;
  }

  .form-group {
    margin-bottom: 1.5rem;
  }

  label {
    display: block;
    margin-bottom: 0.5rem;
    color: var(--dark);
    font-weight: bold;
  }

  .input {
    width: 100%;
    padding: 0.75rem;
    border: 2px solid var(--dark);
    border-radius: 4px;
    font-size: 1rem;
  }

  .common-distances {
    margin-top: 1rem;
  }

  .common-distances p {
    margin-bottom: 0.5rem;
    font-size: 0.9rem;
    color: #666;
  }

  .common-distances button {
    margin-right: 0.5rem;
    margin-bottom: 0.5rem;
    padding: 0.5rem 1rem;
    background-color: var(--secondary);
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 0.9rem;
  }

  .common-distances button:hover {
    background-color: var(--primary);
    color: white;
  }

  .calculate-button {
    width: 100%;
    padding: 1rem;
    background-color: var(--primary);
    color: white;
    border: none;
    border-radius: 4px;
    font-size: 1.1rem;
    font-weight: bold;
    cursor: pointer;
    margin-bottom: 2rem;
  }

  .calculate-button:hover:not(:disabled) {
    background-color: var(--dark);
  }

  .calculate-button:disabled {
    background-color: #ccc;
    cursor: not-allowed;
  }

  .results {
    background-color: var(--secondary);
    padding: 2rem;
    border-radius: 8px;
  }

  .emissions-comparison {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 1.5rem;
    margin: 2rem 0;
  }

  .transport-option {
    background-color: white;
    padding: 1.5rem;
    border-radius: 8px;
    text-align: center;
  }

  .transport-option h4 {
    color: var(--dark);
    margin: 0 0 1rem 0;
  }

  .emissions {
    font-size: 1.5rem;
    font-weight: bold;
    color: var(--dark);
    margin: 0.5rem 0;
  }

  .savings {
    color: var(--primary);
    font-weight: bold;
  }

  .note {
    font-size: 0.9rem;
    color: #666;
  }

  @media (max-width: 600px) {
    .calculator-container {
      padding: 1rem;
    }

    .calculator-modes {
      flex-direction: column;
    }

    .emissions-comparison {
      grid-template-columns: 1fr;
    }
  }
</style> 