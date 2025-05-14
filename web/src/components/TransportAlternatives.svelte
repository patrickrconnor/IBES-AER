<script>
  export let from = '';
  export let to = '';
  export let distance = 0;

  // Station codes for major cities
  const STATIONS = {
    'PVD': {
      amtrak: 'PVD',
      greyhound: 'Providence, RI',
      peterpan: 'Providence, RI',
      name: 'Providence'
    },
    'NYC': {
      amtrak: 'NYP',
      greyhound: 'New York, NY',
      peterpan: 'New York, NY',
      name: 'New York'
    },
    'BOS': {
      amtrak: 'BOS',
      greyhound: 'Boston, MA',
      peterpan: 'Boston, MA',
      name: 'Boston'
    },
    'DCA': {
      amtrak: 'WAS',
      greyhound: 'Washington, DC',
      peterpan: 'Washington, DC',
      name: 'Washington DC'
    }
  };

  // Check if route is serviced by different providers
  function getAvailableServices(fromCode, toCode) {
    const services = [];
    
    if (STATIONS[fromCode] && STATIONS[toCode]) {
      // Amtrak serves most Northeast Corridor routes
      if (['PVD', 'NYC', 'BOS', 'DCA'].includes(fromCode) && 
          ['PVD', 'NYC', 'BOS', 'DCA'].includes(toCode)) {
        services.push({
          name: 'Amtrak',
          url: `https://www.amtrak.com/home.html`,
          fromStation: STATIONS[fromCode].amtrak,
          toStation: STATIONS[toCode].amtrak,
          icon: '🚂'
        });
      }
      
      // Greyhound serves most major cities
      if (STATIONS[fromCode].greyhound && STATIONS[toCode].greyhound) {
        services.push({
          name: 'Greyhound',
          url: 'https://www.greyhound.com',
          fromStation: STATIONS[fromCode].greyhound,
          toStation: STATIONS[toCode].greyhound,
          icon: '🚌'
        });
      }
      
      // Peter Pan serves Northeast
      if (['PVD', 'NYC', 'BOS'].includes(fromCode) && 
          ['PVD', 'NYC', 'BOS'].includes(toCode)) {
        services.push({
          name: 'Peter Pan',
          url: 'https://peterpanbus.com',
          fromStation: STATIONS[fromCode].peterpan,
          toStation: STATIONS[toCode].peterpan,
          icon: '🚐'
        });
      }
    }
    
    return services;
  }

  $: services = getAvailableServices(from, to);
</script>

{#if distance <= 1000 && services.length > 0}
  <div class="alternatives-container">
    <h4>Available Ground Transportation Options</h4>
    <div class="services-grid">
      {#each services as service}
        <div class="service-card">
          <div class="service-header">
            <span class="service-icon">{service.icon}</span>
            <h5>{service.name}</h5>
          </div>
          <div class="service-details">
            <p>From: {service.fromStation}</p>
            <p>To: {service.toStation}</p>
          </div>
          <a 
            href={service.url} 
            target="_blank" 
            rel="noopener noreferrer" 
            class="book-button"
          >
            Check Availability
          </a>
        </div>
      {/each}
    </div>
    <div class="info-box">
      <p>💡 Tip: Ground transportation options often offer:</p>
      <ul>
        <li>Lower carbon emissions</li>
        <li>City center to city center travel</li>
        <li>No airport security lines</li>
        <li>Comfortable seating with Wi-Fi</li>
        <li>Flexible booking options</li>
      </ul>
    </div>
  </div>
{/if}

<style>
  .alternatives-container {
    margin-top: 2rem;
    padding: 1.5rem;
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }

  h4 {
    color: var(--dark);
    margin-bottom: 1.5rem;
    text-align: center;
  }

  .services-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 1.5rem;
    margin-bottom: 1.5rem;
  }

  .service-card {
    padding: 1.5rem;
    background-color: var(--secondary);
    border-radius: 8px;
    display: flex;
    flex-direction: column;
    gap: 1rem;
  }

  .service-header {
    display: flex;
    align-items: center;
    gap: 1rem;
  }

  .service-icon {
    font-size: 1.5rem;
  }

  h5 {
    margin: 0;
    color: var(--dark);
    font-size: 1.2rem;
  }

  .service-details {
    color: var(--dark);
  }

  .service-details p {
    margin: 0.5rem 0;
  }

  .book-button {
    display: inline-block;
    padding: 0.75rem 1rem;
    background-color: var(--primary);
    color: white;
    text-decoration: none;
    border-radius: 4px;
    text-align: center;
    font-weight: bold;
    transition: background-color 0.2s;
  }

  .book-button:hover {
    background-color: var(--dark);
  }

  .info-box {
    margin-top: 1.5rem;
    padding: 1rem;
    background-color: var(--secondary);
    border-radius: 8px;
  }

  .info-box p {
    margin: 0 0 0.5rem 0;
    font-weight: bold;
  }

  .info-box ul {
    margin: 0;
    padding-left: 1.5rem;
  }

  .info-box li {
    margin: 0.5rem 0;
  }

  @media (max-width: 600px) {
    .services-grid {
      grid-template-columns: 1fr;
    }
  }
</style> 