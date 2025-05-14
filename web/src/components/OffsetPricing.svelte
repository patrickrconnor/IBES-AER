<script>
  export let emissions = 0; // in lbs CO2

  const OFFSET_PROVIDERS = {
    CLIMATE_IMPACT: {
      name: 'Climate Impact Partners',
      website: 'https://store.climateimpact.com',
      getPriceRange: (emissions) => {
        if (emissions <= 132) return { min: 0.50, max: 1.50 };
        if (emissions <= 794) return { min: 2.30, max: 9.00 };
        return { min: 13.20, max: 53.00 };
      }
    },
    CLIMATE_TRADE: {
      name: 'Climate Trade',
      website: 'https://market.climatetrade.com',
      getPriceRange: (emissions) => {
        if (emissions <= 132) return { min: 0.50, max: 24.00 };
        if (emissions <= 794) return { min: 2.50, max: 140.00 };
        return { min: 15.00, max: 850.00 };
      }
    },
    WREN: {
      name: 'Wren',
      website: 'https://wren.co',
      getPriceRange: (emissions) => {
        if (emissions <= 132) return { min: 1.70, max: 4.90 };
        if (emissions <= 794) return { min: 9.90, max: 16.00 };
        return { min: 58.70, max: 92.00 };
      }
    }
  };

  $: flightCategory = emissions <= 132 ? 'Short-Haul' : 
                      emissions <= 794 ? 'Domestic' : 
                      'International';
</script>

<div class="offset-pricing">
  <h4>Offset Your Flight</h4>
  <p class="flight-category">Flight Category: <strong>{flightCategory}</strong></p>
  
  <div class="providers">
    {#each Object.entries(OFFSET_PROVIDERS) as [key, provider]}
      {@const priceRange = provider.getPriceRange(emissions)}
      <div class="provider-card">
        <h5>{provider.name}</h5>
        <div class="price-range">
          <span class="price">${priceRange.min.toFixed(2)} - ${priceRange.max.toFixed(2)}</span>
        </div>
        <a href={provider.website} target="_blank" rel="noopener noreferrer" class="offset-link">
          Offset with {provider.name}
        </a>
      </div>
    {/each}
  </div>

  <div class="info-box">
    <p>
      <strong>Why Offset?</strong> Carbon offsetting allows you to compensate for your flight's emissions 
      by investing in projects that reduce or remove greenhouse gases from the atmosphere.
    </p>
    <p>
      All providers listed meet strong verification standards (Gold Standard, VCS, ICROA) 
      and offer various project types including reforestation, renewable energy, and more.
    </p>
  </div>
</div>

<style>
  .offset-pricing {
    margin-top: 2rem;
    padding: 2rem;
    background-color: var(--secondary);
    border-radius: 8px;
  }

  h4 {
    color: var(--dark);
    margin: 0 0 1rem 0;
    text-align: center;
    font-size: 1.5rem;
  }

  .flight-category {
    text-align: center;
    margin-bottom: 1.5rem;
    color: var(--dark);
  }

  .providers {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 1.5rem;
    margin-bottom: 2rem;
  }

  .provider-card {
    background-color: white;
    padding: 1.5rem;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    text-align: center;
  }

  .provider-card h5 {
    color: var(--dark);
    margin: 0 0 1rem 0;
    font-size: 1.2rem;
  }

  .price-range {
    margin: 1rem 0;
  }

  .price {
    font-size: 1.5rem;
    font-weight: bold;
    color: var(--dark);
  }

  .offset-link {
    display: inline-block;
    padding: 0.75rem 1.5rem;
    background-color: var(--primary);
    color: white;
    text-decoration: none;
    border-radius: 4px;
    font-weight: bold;
    transition: background-color 0.2s;
    margin-top: 1rem;
  }

  .offset-link:hover {
    background-color: var(--dark);
  }

  .info-box {
    margin-top: 2rem;
    padding: 1.5rem;
    background-color: white;
    border-radius: 8px;
    color: var(--dark);
  }

  .info-box p {
    margin: 0 0 1rem 0;
  }

  .info-box p:last-child {
    margin-bottom: 0;
  }
</style> 