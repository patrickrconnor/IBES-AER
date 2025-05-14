<script>
  export let from = '';
  export let to = '';
  export let distance = 0;

  // Station codes for major cities with travel times and price ranges
  const STATIONS = {
    'PVD': {
      amtrak: 'PVD',
      greyhound: 'Providence, RI',
      peterpan: 'Providence, RI',
      name: 'Providence',
      coordinates: { lat: 41.7242, lon: -71.4282 }
    },
    'NYC': {
      amtrak: 'NYP',
      greyhound: 'New York, NY',
      peterpan: 'New York, NY',
      name: 'New York',
      coordinates: { lat: 40.7128, lon: -74.0060 }
    },
    'BOS': {
      amtrak: 'BOS',
      greyhound: 'Boston, MA',
      peterpan: 'Boston, MA',
      name: 'Boston',
      coordinates: { lat: 42.3601, lon: -71.0589 }
    },
    'DCA': {
      amtrak: 'WAS',
      greyhound: 'Washington, DC',
      peterpan: 'Washington, DC',
      name: 'Washington DC',
      coordinates: { lat: 38.8951, lon: -77.0364 }
    }
  };

  // Travel time estimates (in minutes) and price ranges by distance
  const TRAVEL_ESTIMATES = {
    AMTRAK: {
      speedMPH: 65,
      pricePerMile: { min: 0.30, max: 0.60 }
    },
    GREYHOUND: {
      speedMPH: 55,
      pricePerMile: { min: 0.15, max: 0.35 }
    },
    PETER_PAN: {
      speedMPH: 55,
      pricePerMile: { min: 0.15, max: 0.35 }
    },
    FLIGHT: {
      speedMPH: 500,
      pricePerMile: { min: 0.15, max: 0.45 }
    }
  };

  function calculateTravelTime(distance, speedMPH) {
    const timeHours = distance / speedMPH;
    const hours = Math.floor(timeHours);
    const minutes = Math.round((timeHours - hours) * 60);
    return { hours, minutes };
  }

  function formatTravelTime(time) {
    if (time.hours === 0) {
      return `${time.minutes} min`;
    }
    return `${time.hours}h ${time.minutes}m`;
  }

  function calculatePriceRange(distance, pricePerMile) {
    const min = Math.round(distance * pricePerMile.min);
    const max = Math.round(distance * pricePerMile.max);
    return `$${min}-${max}`;
  }

  // Check if route is serviced by different providers
  function getAvailableServices(fromCode, toCode) {
    const services = [];
    
    if (STATIONS[fromCode] && STATIONS[toCode]) {
      // Calculate flight time for comparison
      const flightTime = calculateTravelTime(distance/2, TRAVEL_ESTIMATES.FLIGHT.speedMPH);
      const flightPrice = calculatePriceRange(distance/2, TRAVEL_ESTIMATES.FLIGHT.pricePerMile);
      
      // Add 2 hours to flight time for security, boarding, etc.
      const totalFlightTime = {
        hours: flightTime.hours + 2,
        minutes: flightTime.minutes
      };

      // Amtrak serves most Northeast Corridor routes
      if (['PVD', 'NYC', 'BOS', 'DCA'].includes(fromCode) && 
          ['PVD', 'NYC', 'BOS', 'DCA'].includes(toCode)) {
        const trainTime = calculateTravelTime(distance/2, TRAVEL_ESTIMATES.AMTRAK.speedMPH);
        services.push({
          name: 'Amtrak',
          url: `https://www.amtrak.com/home.html`,
          fromStation: STATIONS[fromCode].amtrak,
          toStation: STATIONS[toCode].amtrak,
          icon: '🚂',
          travelTime: formatTravelTime(trainTime),
          priceRange: calculatePriceRange(distance/2, TRAVEL_ESTIMATES.AMTRAK.pricePerMile),
          timeDiff: formatTimeDifference(trainTime, totalFlightTime)
        });
      }
      
      // Greyhound serves most major cities
      if (STATIONS[fromCode].greyhound && STATIONS[toCode].greyhound) {
        const busTime = calculateTravelTime(distance/2, TRAVEL_ESTIMATES.GREYHOUND.speedMPH);
        services.push({
          name: 'Greyhound',
          url: 'https://www.greyhound.com',
          fromStation: STATIONS[fromCode].greyhound,
          toStation: STATIONS[toCode].greyhound,
          icon: '🚌',
          travelTime: formatTravelTime(busTime),
          priceRange: calculatePriceRange(distance/2, TRAVEL_ESTIMATES.GREYHOUND.pricePerMile),
          timeDiff: formatTimeDifference(busTime, totalFlightTime)
        });
      }
      
      // Peter Pan serves Northeast
      if (['PVD', 'NYC', 'BOS'].includes(fromCode) && 
          ['PVD', 'NYC', 'BOS'].includes(toCode)) {
        const busTime = calculateTravelTime(distance/2, TRAVEL_ESTIMATES.PETER_PAN.speedMPH);
        services.push({
          name: 'Peter Pan',
          url: 'https://peterpanbus.com',
          fromStation: STATIONS[fromCode].peterpan,
          toStation: STATIONS[toCode].peterpan,
          icon: '🚐',
          travelTime: formatTravelTime(busTime),
          priceRange: calculatePriceRange(distance/2, TRAVEL_ESTIMATES.PETER_PAN.pricePerMile),
          timeDiff: formatTimeDifference(busTime, totalFlightTime)
        });
      }

      // Add flight comparison
      services.push({
        name: 'Flight',
        url: '',
        fromStation: fromCode,
        toStation: toCode,
        icon: '✈️',
        travelTime: formatTravelTime(totalFlightTime),
        priceRange: flightPrice,
        isReference: true
      });
    }
    
    return services;
  }

  function formatTimeDifference(groundTime, flightTime) {
    const groundMinutes = groundTime.hours * 60 + groundTime.minutes;
    const flightMinutes = flightTime.hours * 60 + flightTime.minutes;
    const diff = groundMinutes - flightMinutes;
    
    if (diff <= 0) return 'Faster than flying!';
    const hours = Math.floor(diff / 60);
    const minutes = diff % 60;
    return `+${hours}h ${minutes}m vs. flying`;
  }

  $: services = getAvailableServices(from, to);
</script>

{#if distance <= 1000 && services.length > 0}
  <div class="alternatives-container">
    <h4>Travel Options Comparison</h4>
    <div class="services-grid">
      {#each services.filter(s => !s.isReference) as service}
        <div class="service-card">
          <div class="service-header">
            <span class="service-icon">{service.icon}</span>
            <h5>{service.name}</h5>
          </div>
          <div class="service-details">
            <p>From: {service.fromStation}</p>
            <p>To: {service.toStation}</p>
            <div class="travel-info">
              <p class="time">⏱️ {service.travelTime}</p>
              <p class="time-diff">{service.timeDiff}</p>
              <p class="price">💰 Est. {service.priceRange}</p>
            </div>
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
      <p>💡 Why Choose Ground Transportation?</p>
      <ul>
        <li>🌱 Lower carbon emissions</li>
        <li>🏙️ City center to city center travel</li>
        <li>⏰ No airport security lines or early arrival needed</li>
        <li>💻 Productive travel time with Wi-Fi and workspace</li>
        <li>💰 Often more cost-effective</li>
        <li>🎟️ Flexible booking and cancellation policies</li>
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
    font-size: 1.5rem;
  }

  .services-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
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
    padding-bottom: 0.5rem;
    border-bottom: 1px solid rgba(0, 0, 0, 0.1);
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

  .travel-info {
    margin-top: 1rem;
    padding: 0.5rem;
    background-color: rgba(255, 255, 255, 0.5);
    border-radius: 4px;
  }

  .time {
    font-weight: bold;
    color: var(--dark);
  }

  .time-diff {
    font-size: 0.9rem;
    color: #666;
  }

  .price {
    margin-top: 0.5rem;
    font-weight: bold;
    color: var(--primary);
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
    padding: 1.5rem;
    background-color: var(--secondary);
    border-radius: 8px;
  }

  .info-box p {
    margin: 0 0 1rem 0;
    font-weight: bold;
    font-size: 1.1rem;
  }

  .info-box ul {
    margin: 0;
    padding-left: 1.5rem;
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 0.5rem;
  }

  .info-box li {
    margin: 0.5rem 0;
  }

  @media (max-width: 600px) {
    .services-grid {
      grid-template-columns: 1fr;
    }

    .info-box ul {
      grid-template-columns: 1fr;
    }
  }
</style> 