<script>
  let distance = 300; // Default to 300 miles (PVD to NYC example)
  let passengers = 1;
  let results = null;

  // Emissions factors (lbs CO2 per passenger-mile)
  const EMISSIONS_FACTORS = {
    FLIGHT_SHORT: 0.44, // 132 lbs / 300 miles
    FLIGHT_DOMESTIC: 0.496, // 794 lbs / 1600 miles
    TRAIN: 0.147, // 44 lbs / 300 miles
    BUS: 0.367, // 110 lbs / 300 miles
    CAR: 0.513 // 154 lbs / 300 miles
  };

  function calculateEmissions() {
    const flightEmissions = distance <= 500 
      ? distance * EMISSIONS_FACTORS.FLIGHT_SHORT
      : distance * EMISSIONS_FACTORS.FLIGHT_DOMESTIC;
    
    const trainEmissions = distance * EMISSIONS_FACTORS.TRAIN;
    const busEmissions = distance * EMISSIONS_FACTORS.BUS;
    const carEmissions = (distance * EMISSIONS_FACTORS.CAR) / passengers;

    results = {
      flight: Math.round(flightEmissions),
      train: Math.round(trainEmissions),
      bus: Math.round(busEmissions),
      car: Math.round(carEmissions)
    };
  }
</script>

<div class="calculator-container">
  <h2>Travel Emissions Calculator</h2>
  
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

  <button class="calculate-button" on:click={calculateEmissions}>
    Calculate Emissions
  </button>

  {#if results}
    <div class="results">
      <h3>Estimated CO₂ Emissions</h3>
      
      <div class="emissions-comparison">
        <div class="transport-option">
          <h4>Flight</h4>
          <p class="emissions">{results.flight} lbs CO₂</p>
          <p class="note">{distance <= 500 ? 'Short-haul' : 'Domestic'} flight</p>
        </div>

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
      </div>

      <div class="recommendations">
        <h4>Recommendations</h4>
        {#if distance <= 500}
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
          <p>For this longer trip:</p>
          <ul>
            <li>If flying is necessary, consider:
              <ul>
                <li>Booking economy class</li>
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

  .calculate-button:hover {
    background-color: var(--dark);
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

  .recommendations {
    margin-top: 2rem;
    padding-top: 2rem;
    border-top: 1px solid #eee;
  }

  .recommendations ul {
    list-style: disc;
    padding-left: 1.5rem;
    margin-top: 1rem;
  }

  .recommendations li {
    margin-bottom: 0.5rem;
  }

  @media (max-width: 600px) {
    .calculator-container {
      padding: 1rem;
    }

    .emissions-comparison {
      grid-template-columns: 1fr;
    }
  }
</style> 