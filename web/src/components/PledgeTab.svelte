<script>
  import { onMount } from 'svelte';

  // Pledge types
  const PLEDGE_TYPES = {
    FLIGHTS: 'flights',
    EMISSIONS: 'emissions'
  };

  // Pledge categories
  const CATEGORIES = {
    SHORT_HAUL: { name: 'Short-haul', avgEmissions: 132 },
    DOMESTIC: { name: 'Domestic', avgEmissions: 794 },
    INTERNATIONAL: { name: 'International', avgEmissions: 4695 }
  };

  let pledgeType = PLEDGE_TYPES.FLIGHTS;
  let category = 'SHORT_HAUL';
  let amount = 1;
  let pledges = [];
  let totalEmissionsReduced = 0;
  let totalFlightsReduced = 0;

  onMount(() => {
    // Load existing pledges from localStorage
    const savedPledges = localStorage.getItem('pledges');
    if (savedPledges) {
      pledges = JSON.parse(savedPledges);
      calculateTotals();
    }
  });

  function calculateTotals() {
    totalEmissionsReduced = pledges.reduce((total, pledge) => {
      return total + (pledge.emissionsReduction || 0);
    }, 0);
    
    totalFlightsReduced = pledges.reduce((total, pledge) => {
      return total + (pledge.flightsReduction || 0);
    }, 0);
  }

  function addPledge() {
    const newPledge = {
      type: pledgeType,
      category,
      date: new Date().toISOString(),
      flightsReduction: pledgeType === PLEDGE_TYPES.FLIGHTS ? amount : 0,
      emissionsReduction: pledgeType === PLEDGE_TYPES.EMISSIONS ? amount : 
        (pledgeType === PLEDGE_TYPES.FLIGHTS ? amount * CATEGORIES[category].avgEmissions : 0)
    };

    pledges = [...pledges, newPledge];
    localStorage.setItem('pledges', JSON.stringify(pledges));
    calculateTotals();

    // Reset form
    amount = 1;
  }
</script>

<div class="pledge-container">
  <div class="pledge-info">
    <h2>Pledge Tiers</h2>
    <p class="intro-text">Based on feedback from an IBES faculty survey, our team developed a range of pledge tiers to reflect the varying levels of commitment to emissions reduction expressed by respondents.</p>
    
    <div class="strategy-section">
      <h3>Strategy 1: Reducing Flights</h3>
      <table>
        <thead>
          <tr>
            <th>Tier</th>
            <th>Pledge</th>
            <th>Avg Emissions Reduced</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td><strong>Bronze</strong></td>
            <td>Reduce 1 short-haul round-trip per year</td>
            <td>132 lbs CO₂</td>
          </tr>
          <tr>
            <td><strong>Silver</strong></td>
            <td>Reduce 1 domestic round-trip per year</td>
            <td>794 lbs CO₂</td>
          </tr>
          <tr>
            <td><strong>Gold</strong></td>
            <td>Reduce 1 international round-trip per year</td>
            <td>4,695 lbs CO₂</td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="strategy-section">
      <h3>Strategy 2: Alternative Transport</h3>
      <table>
        <thead>
          <tr>
            <th>Tier</th>
            <th>Pledge</th>
            <th>Avg Emissions Reduced</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td><strong>Bronze</strong></td>
            <td>Replace 1 short-haul round-trip with a bus</td>
            <td>22 lbs CO₂</td>
          </tr>
          <tr>
            <td><strong>Silver</strong></td>
            <td>Replace 1 short-haul round-trip with a carpool</td>
            <td>55 - 101 lbs CO₂ *</td>
          </tr>
          <tr>
            <td><strong>Gold</strong></td>
            <td>Replace 1 short-haul round-trip with a train</td>
            <td>88 lbs CO₂</td>
          </tr>
        </tbody>
      </table>
      <p class="note">* Depending on # people in carpool</p>
    </div>

    <div class="strategy-section">
      <h3>Strategy 3: Offsetting</h3>
      <table>
        <thead>
          <tr>
            <th>Tier</th>
            <th>Pledge</th>
            <th>Avg Emissions Reduced</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td><strong>Bronze</strong></td>
            <td>Offset 1 short-haul round-trip per year</td>
            <td>132 lbs CO₂</td>
          </tr>
          <tr>
            <td><strong>Silver</strong></td>
            <td>Offset 1 domestic round-trip per year</td>
            <td>794 lbs CO₂</td>
          </tr>
          <tr>
            <td><strong>Gold</strong></td>
            <td>Offset 1 international round-trip per year</td>
            <td>4,695 lbs CO₂</td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="impact-section">
      <p>Faculty are encouraged to scale their pledges however they like to match their travel habits. From small, single-trip changes to broader commitments across travel behavior, every change makes an impact on your carbon footprint.</p>
      <div class="impact-example">
        <h4>Example Impact Scenarios:</h4>
        <ul>
          <li>If just 15 IBES faculty members pledge to either reduce or offset <strong>1 domestic round-trip per year</strong>, we can cut out almost <strong>12,000 lbs of emissions</strong>— equivalent to taking <strong>one gas-powered car off the road each year.</strong></li>
          <li>For a more ambitious example, consider all 25 IBES faculty members pledging to reduce <strong>1 domestic round-trip per year,</strong> as well as 5 faculty members pledging to reduce <strong>1 international round-trip a year</strong>. That comes out to <strong>43,000 lbs CO₂</strong> reduced annually: equivalent to taking <strong>5 gas-powered cars off the road per year.</strong></li>
        </ul>
      </div>
      <p class="google-form-link">For faculty who did not get a chance to fill out our google form, or for those who wish to make an official pledge, please fill out this brief <a href="https://docs.google.com/forms/d/e/1FAIpQLSdZ07znDJjt_vg3LtKUrF-gi6aQkt0yAi67m7hvtBVa_Gen-g/viewform?usp=sharing" target="_blank" rel="noopener noreferrer">Google Form</a>.</p>
    </div>
  </div>

  <div class="pledge-form">
    <h2>Make a Pledge</h2>
    <div class="form-group">
      <label>Pledge Type</label>
      <div class="radio-group">
        <label>
          <input 
            type="radio" 
            bind:group={pledgeType} 
            value={PLEDGE_TYPES.FLIGHTS}
          >
          Reduce Flights
        </label>
        <label>
          <input 
            type="radio" 
            bind:group={pledgeType} 
            value={PLEDGE_TYPES.EMISSIONS}
          >
          Reduce CO₂ Emissions (lbs)
        </label>
      </div>
    </div>

    {#if pledgeType === PLEDGE_TYPES.FLIGHTS}
      <div class="form-group">
        <label for="category">Flight Category</label>
        <select id="category" bind:value={category} class="select">
          {#each Object.entries(CATEGORIES) as [key, value]}
            <option value={key}>{value.name} ({value.avgEmissions} lbs CO₂ avg.)</option>
          {/each}
        </select>
      </div>
    {/if}

    <div class="form-group">
      <label for="amount">
        {pledgeType === PLEDGE_TYPES.FLIGHTS ? 'Number of Flights' : 'Amount of CO₂ (lbs)'}
      </label>
      <input 
        id="amount"
        type="number"
        min="1"
        bind:value={amount}
        class="input"
      />
    </div>

    <button class="pledge-button" on:click={addPledge}>
      Make Pledge
    </button>
  </div>

  <div class="pledge-summary">
    <h3>Your Pledge Impact</h3>
    <div class="summary-stats">
      <div class="stat">
        <span class="stat-value">{totalFlightsReduced}</span>
        <span class="stat-label">Flights Reduced</span>
      </div>
      <div class="stat">
        <span class="stat-value">{totalEmissionsReduced.toLocaleString()}</span>
        <span class="stat-label">lbs CO₂ Reduced</span>
      </div>
    </div>

    {#if pledges.length > 0}
      <div class="pledge-history">
        <h4>Pledge History</h4>
        <ul>
          {#each pledges as pledge}
            <li>
              {new Date(pledge.date).toLocaleDateString()}: 
              {#if pledge.type === PLEDGE_TYPES.FLIGHTS}
                Pledged to reduce {pledge.flightsReduction} {CATEGORIES[pledge.category].name} flights
                ({pledge.emissionsReduction} lbs CO₂)
              {:else}
                Pledged to reduce {pledge.emissionsReduction} lbs CO₂
              {/if}
            </li>
          {/each}
        </ul>
      </div>
    {/if}
  </div>
</div>

<style>
  .pledge-container {
    max-width: 800px;
    margin: 0 auto;
    padding: 2rem;
  }

  h2 {
    color: var(--dark);
    margin-bottom: 2rem;
    text-align: center;
  }

  .pledge-form {
    background-color: var(--secondary);
    padding: 2rem;
    border-radius: 8px;
    margin-bottom: 2rem;
  }

  .form-group {
    margin-bottom: 1.5rem;
  }

  .radio-group {
    display: flex;
    gap: 2rem;
    margin-top: 0.5rem;
  }

  .radio-group label {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    cursor: pointer;
  }

  label {
    display: block;
    margin-bottom: 0.5rem;
    color: var(--dark);
    font-weight: bold;
  }

  .input, .select {
    width: 100%;
    padding: 0.75rem;
    border: 2px solid var(--dark);
    border-radius: 4px;
    font-size: 1rem;
    background-color: white;
    color: var(--dark);
  }

  .pledge-button {
    width: 100%;
    padding: 1rem;
    background-color: var(--primary);
    color: white;
    border: none;
    border-radius: 4px;
    font-size: 1.1rem;
    font-weight: bold;
    cursor: pointer;
    transition: background-color 0.2s;
  }

  .pledge-button:hover {
    background-color: var(--dark);
  }

  .pledge-summary {
    background-color: white;
    padding: 2rem;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }

  .summary-stats {
    display: flex;
    justify-content: space-around;
    margin: 2rem 0;
  }

  .stat {
    text-align: center;
  }

  .stat-value {
    display: block;
    font-size: 2rem;
    font-weight: bold;
    color: var(--dark);
  }

  .stat-label {
    color: #666;
  }

  .pledge-history {
    margin-top: 2rem;
    padding-top: 2rem;
    border-top: 1px solid #eee;
  }

  .pledge-history h4 {
    color: var(--dark);
    margin-bottom: 1rem;
  }

  .pledge-history ul {
    list-style: none;
    padding: 0;
  }

  .pledge-history li {
    padding: 0.75rem;
    border-bottom: 1px solid #eee;
    color: var(--dark);
  }

  .pledge-history li:last-child {
    border-bottom: none;
  }

  .pledge-info {
    margin-bottom: 3rem;
  }

  .intro-text {
    margin-bottom: 2rem;
    font-size: 1.1rem;
    line-height: 1.6;
  }

  .strategy-section {
    margin-bottom: 2.5rem;
  }

  .strategy-section h3 {
    color: var(--dark);
    margin-bottom: 1rem;
  }

  table {
    width: 100%;
    border-collapse: collapse;
    margin-bottom: 1rem;
    background-color: white;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
    border-radius: 8px;
    overflow: hidden;
  }

  th, td {
    padding: 1rem;
    text-align: left;
    border-bottom: 1px solid #eee;
  }

  th {
    background-color: var(--dark);
    color: white;
    font-weight: bold;
  }

  tr:last-child td {
    border-bottom: none;
  }

  tr:hover {
    background-color: #f8f8f8;
  }

  .note {
    font-size: 0.9rem;
    color: #666;
    margin-top: 0.5rem;
    font-style: italic;
  }

  .impact-section {
    background-color: var(--secondary);
    padding: 2rem;
    border-radius: 8px;
    margin-top: 2rem;
  }

  .impact-example {
    margin: 1.5rem 0;
  }

  .impact-example h4 {
    color: var(--dark);
    margin-bottom: 1rem;
  }

  .impact-example ul {
    list-style: none;
    padding: 0;
  }

  .impact-example li {
    margin-bottom: 1rem;
    line-height: 1.6;
  }

  .impact-example li:last-child {
    margin-bottom: 0;
  }

  .google-form-link {
    margin-top: 1.5rem;
    text-align: center;
  }

  .google-form-link a {
    color: var(--dark);
    text-decoration: underline;
  }

  .google-form-link a:hover {
    color: var(--primary);
  }
</style> 