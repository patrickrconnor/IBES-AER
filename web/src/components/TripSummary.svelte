<script>
  export let emissions = 0;
  export let distance = 0;
  export let mode = '';
  export let alternatives = [];

  function getEnvironmentalImpact(emissions) {
    return {
      trees: Math.round(emissions / 48), // One tree absorbs ~48 lbs CO2 per year
      carMiles: Math.round(emissions / 0.9), // Average car emits 0.9 lbs CO2 per mile
      homeEnergy: Math.round(emissions / 30) // Average home emits ~30 lbs CO2 per day
    };
  }

  $: impact = getEnvironmentalImpact(emissions);
</script>

<div class="summary-container">
  <h4>Trip Environmental Impact</h4>
  
  <div class="impact-grid">
    <div class="impact-card">
      <span class="impact-icon">🌳</span>
      <div class="impact-details">
        <p class="impact-value">{impact.trees}</p>
        <p class="impact-label">Trees needed for a year to offset this trip</p>
      </div>
    </div>

    <div class="impact-card">
      <span class="impact-icon">🚗</span>
      <div class="impact-details">
        <p class="impact-value">{impact.carMiles}</p>
        <p class="impact-label">Miles driven in an average car</p>
      </div>
    </div>

    <div class="impact-card">
      <span class="impact-icon">🏠</span>
      <div class="impact-details">
        <p class="impact-value">{impact.homeEnergy}</p>
        <p class="impact-label">Days of home energy use</p>
      </div>
    </div>
  </div>

  {#if alternatives.length > 0}
    <div class="alternatives-summary">
      <h5>Potential Savings</h5>
      <ul>
        {#each alternatives as alt}
          <li>
            <span class="alt-icon">{alt.icon}</span>
            Switch to {alt.name}: Save {Math.round(emissions - alt.emissions)} lbs CO₂
            {#if alt.timeDiff}
              <span class="time-diff">({alt.timeDiff})</span>
            {/if}
          </li>
        {/each}
      </ul>
    </div>
  {/if}

  <div class="tips-box">
    <h5>💡 Tips to Reduce Impact</h5>
    {#if mode === 'flight'}
      <ul>
        <li>Consider economy class seating (reduces emissions by up to 75%)</li>
        <li>Choose direct flights when possible</li>
        <li>Pack lighter to reduce fuel consumption</li>
        <li>Consider carbon offset programs</li>
      </ul>
    {:else if mode === 'car'}
      <ul>
        <li>Consider carpooling to share emissions</li>
        <li>Maintain proper tire pressure for better efficiency</li>
        <li>Remove excess weight from vehicle</li>
        <li>Use cruise control on highways</li>
      </ul>
    {/if}
  </div>
</div>

<style>
  .summary-container {
    margin-top: 2rem;
    padding: 2rem;
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }

  h4 {
    color: var(--dark);
    text-align: center;
    margin-bottom: 2rem;
    font-size: 1.5rem;
  }

  .impact-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 1.5rem;
    margin-bottom: 2rem;
  }

  .impact-card {
    padding: 1.5rem;
    background-color: var(--secondary);
    border-radius: 8px;
    display: flex;
    align-items: center;
    gap: 1rem;
  }

  .impact-icon {
    font-size: 2.5rem;
  }

  .impact-details {
    flex: 1;
  }

  .impact-value {
    font-size: 1.8rem;
    font-weight: bold;
    color: var(--dark);
    margin: 0;
  }

  .impact-label {
    margin: 0.5rem 0 0 0;
    color: #666;
    font-size: 0.9rem;
  }

  .alternatives-summary {
    margin-top: 2rem;
    padding: 1.5rem;
    background-color: var(--secondary);
    border-radius: 8px;
  }

  .alternatives-summary h5 {
    margin: 0 0 1rem 0;
    color: var(--dark);
    font-size: 1.2rem;
  }

  .alternatives-summary ul {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  .alternatives-summary li {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    margin: 0.75rem 0;
    padding: 0.5rem;
    background-color: rgba(255, 255, 255, 0.5);
    border-radius: 4px;
  }

  .alt-icon {
    font-size: 1.2rem;
  }

  .time-diff {
    font-size: 0.9rem;
    color: #666;
    margin-left: 0.5rem;
  }

  .tips-box {
    margin-top: 2rem;
    padding: 1.5rem;
    background-color: var(--secondary);
    border-radius: 8px;
  }

  .tips-box h5 {
    margin: 0 0 1rem 0;
    color: var(--dark);
    font-size: 1.2rem;
  }

  .tips-box ul {
    margin: 0;
    padding-left: 1.5rem;
  }

  .tips-box li {
    margin: 0.5rem 0;
    color: var(--dark);
  }

  @media (max-width: 600px) {
    .impact-grid {
      grid-template-columns: 1fr;
    }

    .impact-card {
      padding: 1rem;
    }

    .impact-icon {
      font-size: 2rem;
    }

    .impact-value {
      font-size: 1.5rem;
    }
  }
</style> 