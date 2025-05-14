<script>
  import { onMount } from 'svelte';
  import { marked } from 'marked';

  let activeTab = 'about';
  let from = '';
  let to = '';
  let passengers = 1;
  let flightClass = 'economy';
  let results = null;
  let loading = false;
  let error = null;

  // Configure marked options
  marked.use({
    gfm: true,
    breaks: true
  });

  function setActiveTab(tab) {
    activeTab = tab;
  }

  async function calculateEmissions() {
    loading = true;
    error = null;
    
    try {
      // For now, we'll just simulate the API call with mock data
      // In production, this would be replaced with the actual Climatiq API call
      await new Promise(resolve => setTimeout(resolve, 1000)); // Simulate API delay
      
      results = {
        co2e: 794,
        co2e_unit: "kg",
        legs: [{
          activity_data: {
            activity_value: 1600,
            activity_unit: "passenger-km"
          },
          constituent_gases: {
            co2: 788,
            ch4: 0.002,
            n2o: 0.015
          }
        }]
      };
    } catch (err) {
      error = 'Failed to calculate emissions. Please try again.';
    } finally {
      loading = false;
    }
  }

  let markdownContent = `# Bears on the Ground:   
**Aviation Emissions Reduction Framework for Academic and Research Expeditions (AERFARE)**
![Screenshot 2025-05-13 at 10.32.25 AM](https://hackmd.io/_uploads/SJ5-O0lble.png)

## 1: Introduction to AERFARE

This guide is designed to support the IBES faculty's decision-making around academic travel by providing clear, evidence-based strategies to reduce aviation-related carbon emissions. Whether you're heading to a conference, invited to speak abroad, or considering local collaborations, this resource helps you weigh your options— from **virtual attendance and train routes to carbon offsetting and sustainable flight choices**.  

We recognize that travel is a vital part of your research and professional engagement. This framework doesn't aim to restrict your mobility; rather, it empowers you with tools to make informed, climate-conscious choices that align with IBES's commitment to environmental leadership.  

Every decision, no matter how small, can help reduce our collective footprint. *Let's lead by example.*

**How AERFARE Works:**  
Before you book a work-related flight, go through the flowchart on the cover page of this packet. Our decision tree will direct you to resources that can be used to assess the necessity of your trip, shift to a lower-emissions travel alternative, or offset your flight.  

Our team has spent the semester researching sustainable travel practices and calculating emissions of flight routes common to IBES faculty. We hope this guide offers you a better understanding of your aviation emissions and encourages you to become a partner in reducing IBES's carbon footprint.  

As our project progresses, we aim to collect emissions reduction pledges from faculty and reduce a minimum of **10,000 lbs of CO2** associated with the department's aviation.

## 2: Virtual Attendance and "Opting Out"

In recent years, virtual participation in conferences, meetings, and guest lectures has become a widely accepted and often preferred alternative to in-person travel. Platforms like Zoom have not only made remote participation technically seamless but have also expanded access to academic engagement across geographic and financial barriers

### Why Choose Virtual?

Opting for virtual attendance helps avoid emissions entirely— unlike offsetting, which mitigates impact after the fact. Consider the following carbon savings from just one avoided round trip:

| Flight Category | Example Round Trip | Emissions Per Round Trip (including radiative forcing) | EPA Range (Secondary) |
| :---- | :---- | :---- | :---- |
| **Short-Haul** | Providence ⇄ New York (300 mi) | 132 lbs CO₂ | ~130–220 lbs CO₂ |
| **Domestic** | Providence ⇄ Chicago (1,600 mi) | 794 lbs CO₂ | ~1,100–1,400 lbs CO₂ |
| **International** | Providence ⇄ London (5,000 mi) | 4,695 lbs CO₂ | ~3,500–4,700 lbs CO₂ |

To put that in perspective using EPA emissions estimates: **just one international trip emits roughly as much CO₂** **as providing electricity to an average American home for 6 months**.

### Equity and Inclusion Benefits

Encouraging virtual attendance also supports faculty who face barriers to frequent travel— whether due to caregiving responsibilities, health constraints, or lack of visa access. Studies show that virtual conferences increase participation from early-career researchers, women, and scholars from underrepresented regions.[^1] By normalizing virtual alternatives, IBES promotes an academic culture rooted in equity, accessibility, and sustainability.

### "Opt Out" When Reasonable

Faculty are encouraged to assess whether physical attendance is truly necessary. In cases where the professional benefits of a trip can be achieved virtually, we recommend formally requesting to attend remotely. Some funding agencies and conference organizers may still default to in-person expectations, but many are increasingly flexible if asked.

Here is a sample email template to request virtual participation:

**Subject:** Request for Virtual Participation in \[Conference Name\]

Dear \[Organizer's Name\],

I am honored to have been invited to \[present/attend\] \[Conference/Event Name\]. I'm currently working with Brown University's Institute at Brown for Environment and Society (IBES) to reduce academic aviation emissions, as the department is trying to minimize unnecessary flights where virtual participation is possible. As part of this initiative, I've pledged to minimize flight-related carbon emissions wherever feasible.

In support of this commitment, I'm reaching out to ask whether remote participation would be possible for \[presentation/session\]. I would be glad to engage fully via \[Zoom/Teams/etc.\] and ensure that my contribution maintains the same level of quality and collaboration as in-person attendance.

*(Optional: If a virtual alternative is not available, I may need to reconsider my participation.)*

Thank you for considering this request, and for any flexibility you can offer.

Warm regards,  
 \[Your Name\]  
 \[Your Title/Position\]  
 Institute at Brown for Environment and Society

## 3: Short-haul Flight Alternatives

Short-haul flights— typically defined as flights under 500 miles— are among the most carbon-intensive forms of travel per mile. That's because takeoff and landing, the most fuel-consuming parts of a flight, dominate these brief routes. Flying from Providence to Philadelphia or Washington, D.C. may save time in the air, but the environmental cost is disproportionately high.

Alternative ground transport is a good emissions reduction strategy for East Coast travel. However, alternatives are not necessarily lower-emissions than flights. Calculating emissions for travel by car is especially dependent on the model, fuel mileage, and number of passengers. Here is a comparison for a **300-mile round trip** between Providence (PVD) and NYC for 1 passenger[^2]:

| Transportation Method | Emissions | Emissions reduced compared to flying |
| :---- | :---- | :---- |
| **Flight** | 132 lbs CO2 | N/A |
| **Train (Amtrak)** | 44 lbs CO2 | 88 lbs CO2 |
| **Bus (Coach)** | 110 lbs CO2 | 22 lbs CO2 |
| **Average Passenger Vehicle** | 154 lbs CO2 | \-22 lbs CO2 (*See Note*) |

As you can see, across multiple short trips each year, choosing ground alternatives can easily reduce **hundreds of pounds of emissions per person**. For faculty flying multiple times a semester, these savings scale quickly.

NOTE: Passenger vehicles, on average, can often emit more CO2  per mile than a flight if driving alone. However, carpooling with even 1 other person to a work-related event can drastically reduce your emissions. Using the PVD → NYC round-trip example, **carpooling with 1 other person would reduce 55 lbs CO2 (per person) compared to flying**.

Consider booking your travel with [Amtrak](https://www.amtrak.com/home.html), [Peter Pan Bus Lines](https://peterpanbus.com/), or [Greyhound](https://www.greyhound.com/). These companies provide travel from Providence to over a hundred destinations across the East Coast, often at much lower prices than short-haul flights.

## 4: Carbon Offsetting

Even when travel is necessary, the environmental impact doesn't have to go unchecked. Carbon offsetting allows individuals or institutions to invest in projects that reduce or remove greenhouse gases from the atmosphere—compensating for emissions from flights or other activities.

### What Is Carbon Offsetting?

When you buy an offset credit, you're funding a project— such as forest conservation, clean cookstoves, or renewable energy— that avoids or removes a certain amount of CO₂. Typically, **1 offset \= 1 ton of CO₂ reduced or avoided.**

Offsets don't erase the emissions from flying, but they can neutralize their net climate impact when applied responsibly. To be effective, offsets must be verified by a reputable third party and demonstrate additionality. That is, the reductions wouldn't have occurred without the offset funding.

**Personal Offsetting Options**

Below are three trusted offset services that meet strong verification standards (Gold Standard, VCS, ICROA):

| Provider | Key Features | Website |
| :---- | :---- | :---- |
| Climate Impact Partners | Verified projects; choose from reforestation, energy access, and more | [store.climateimpact.com](http://store.climateimpact.com)  |
| Climate Trade | In-platform calculator; customizable offset projects across the globe | [market.climatetrade.com](http://market.climatetrade.com)  |
| Wren | Subscription or one-time offset plans; portfolio-based, lifestyle calculator | [wren.co](http://wren.co)  |

**Cost of Purchasing Offsets**

Below are average offset costs by flight type from each of these resources. Note that one metric tonne of carbon accounts for one carbon credit. The calculation emissions for each category represent the selected routes below calculated by CarbonFootprint.com's flight calculator using economy seating and including radiative forcing:

### Short-Haul
Providence ⇄ New York: ~300 miles round trip = 132 lbs CO₂

### Domestic
Providence ⇄ Chicago: ~1,600 miles round trip = 794 lbs CO₂

### International
Providence ⇄ London: ~5,000 miles round trip = 4695 lbs CO₂

|  | Average Cost of Offsetting 1 Round-Trip |  |  |
| :---- | ----- | :---- | :---- |
| **Flight Category** | **Climate Impact Partners** | **Climate Trade** | **Wren** |
| **Short-Haul** | $0.50 \- $1.50 | $0.50 \- $24.00 | $1.70 \- $4.90 |
| **Domestic** | $2.30 \- $9.00 | $2.50 \- $140.00 | $9.90 \- $16.00 |
| **International** | $13.20 \- $53.00 | $15.00 \- $850.00 | $58.70- $92.00 |

## 5: Unavoidable Flights and Pledges

For academic travel that is truly essential, there are still ways to minimize the climate impact of flights when they can't be avoided.

**Tips for Lower-Emission Flying**  
If you must fly, the following practices can significantly reduce the carbon footprint of your trip:

### Fly Economy Class

Business class can emit up to 3x more CO₂ emissions per passenger than economy. This is primarily because business class seats occupy more space and are heavier, reducing the total number of passengers an aircraft can carry. Consequently, the aircraft's emissions are distributed among fewer individuals, increasing the per-passenger carbon footprint.

### Choose Non-Stop Flights When Possible

Takeoff and landing are the most fuel-intensive parts of a flight. Direct flights usually emit less than multi-leg itineraries. 

For example, Boston ⇄ Paris round trip: 

Non-stop trip emits 3,284 lbs CO2

Multi-leg trip (stop in London) emits over 4,000 lbs CO2 

By booking all your trips non-stop, you can reduce 100s of lbs of CO2 from your travel footprint.

### Use Lower-Emission Airlines

Some airlines prioritize newer, fuel-efficient aircraft, sustainable aviation fuels (SAFs), and offset programs. You can check airline sustainability rankings through tools like [Atmosfair Airline Index](https://www.atmosfair.de/wp-content/uploads/atmosfair-aai2024-ranking-background-final.pdf).[^3]

Some of the notably sustainable U.S. airlines include:

1. Alaska Airlines
2. United Airlines
3. American Airlines
4. Delta Airlines

### Offset Emissions

If the trip is unavoidable, be sure to offset emissions using a trusted provider (see **Section 4**).

**Pledge Tiers**

## Based on feedback from an IBES faculty survey, our team developed a range of pledge tiers to reflect the varying levels of commitment to emissions reduction expressed by respondents. 

#### **Strategy 1: Reducing Flights**

| Tier | Pledge | Avg Emissions Reduced |
| :---- | :---- | :---- |
| **Bronze** | Reduce 1 short-haul round-trip per year | 132 lbs CO₂ |
| **Silver** | Reduce 1 domestic round-trip per year | 794 lbs CO₂ |
| **Gold** | Reduce 1 international round-trip per year | 4,695 lbs CO₂ |

#### **Strategy 2: Alternative Transport**

| Tier | Pledge | Avg Emissions Reduced |
| :---- | :---- | :---- |
| **Bronze** | Replace 1 short-haul round-trip with a bus | 22 lbs CO₂ |
| **Silver** | Replace 1 short-haul round-trip with a carpool | 55 \- 101 lbs CO₂ *\*Depending on \# people in carpool* |
| **Gold** | Replace 1 short-haul round-trip with a train  | 88 lbs CO₂ |

#### **Strategy 3: Offsetting**

| Tier | Pledge | Avg Emissions Reduced |
| :---- | :---- | :---- |
| **Bronze** | Offset 1 short-haul round-trip per year | 132 lbs CO₂ |
| **Silver** | Offset 1 domestic round-trip per year | 794 lbs CO₂ |
| **Gold** | Offset 1 international round-trip per year | 4,695 lbs CO₂ |

Faculty are encouraged to scale their pledges however they like to match their travel habits. From small, single-trip changes to broader commitments across travel behavior, every change makes an impact on your carbon footprint.  
On the low end, if just 15 IBES faculty members pledge to either reduce or offset **1 domestic round-trip per year**, we can cut out almost **12,000 lbs of emissions**— equivalent to taking **one gas-powered car off the road each year.**  
For a more ambitious example, consider all 25 IBES faculty members pledging to reduce **1 domestic round-trip per year,** as well as 5 faculty members pledging to reduce **1 international round-trip a year**. That comes out to **43,000 lbs CO2** reduced annually: equivalent to taking **5 gas-powered cars off the road per year.**  
For faculty who did not get a chance to fill out our google form, or for those who wish to make an official pledge, please fill out this brief [Google Form](https://docs.google.com/forms/d/e/1FAIpQLSdZ07znDJjt_vg3LtKUrF-gi6aQkt0yAi67m7hvtBVa_Gen-g/viewform?usp=sharing). 

***Thank you for taking the time to engage with this guide. Your thoughtful decisions and collective action are essential to helping IBES lead the way in sustainable academic travel\!***

[^1]: ​Skiles et al., 2022

[^2]: Calculated using Carbonfootprint.com

[^3]: CO₂ emissions per payload kilometer are calculated for individual flights based on aircraft and engine type, capacity, and load factors, then compared to the most efficient flight scenario as defined by the International Civil Aviation Organization (ICAO). Each city pair is scored for efficiency, with the best scenario assigned 100 points and others rated accordingly. These scores are averaged across all routes flown by each airline to produce a global efficiency score, which is used to rank airlines. The Airline Efficiency Index (AAI) uses the ICAO CO₂ method, with a ±1.5 point accuracy at a 95% confidence level.`;
</script>

<main>
  <header class="header">
    <div class="header-content">
      <img src="/airplane-logo.png" alt="Airplane Logo" class="logo" />
      <h1>Bears On The Ground</h1>
    </div>
  </header>

  <div class="container">
    <div class="tab-container">
      <button 
        class="tab" 
        class:active={activeTab === 'about'} 
        on:click={() => setActiveTab('about')}
      >
        Our Guide
      </button>
      <button 
        class="tab" 
        class:active={activeTab === 'calculator'} 
        on:click={() => setActiveTab('calculator')}
      >
        Enter Flight Details
      </button>
    </div>

    {#if activeTab === 'about'}
      <div class="content markdown-content">
        {@html marked(markdownContent)}
      </div>
    {:else if activeTab === 'calculator'}
      <div class="calculator-container">
        <iframe 
          title="Carbon Calculator"
          width="100%" 
          height="750" 
          frameborder="0" 
          marginwidth="0" 
          marginheight="0" 
          scrolling="no" 
          src="https://calculator.carbonfootprint.com/calculator.aspx?c=flight"
        ></iframe>
      </div>
    
      <div class="calculator-form">
        <div class="form-group">
          <label for="from">From (Airport Code)</label>
          <input 
            id="from"
            bind:value={from}
            placeholder="e.g. PVD"
            class="input"
          />
        </div>

        <div class="form-group">
          <label for="to">To (Airport Code)</label>
          <input 
            id="to"
            bind:value={to}
            placeholder="e.g. JFK"
            class="input"
          />
        </div>

        <div class="form-group">
          <label for="passengers">Number of Passengers</label>
          <input 
            id="passengers"
            type="number"
            min="1"
            bind:value={passengers}
            class="input"
          />
        </div>

        <div class="form-group">
          <label for="class">Flight Class</label>
          <select id="class" bind:value={flightClass} class="select">
            <option value="economy">Economy</option>
            <option value="business">Business</option>
            <option value="first">First</option>
          </select>
        </div>

        <button 
          on:click={calculateEmissions}
          disabled={loading}
          class="calculate-button"
        >
          {loading ? 'Calculating...' : 'Calculate Emissions'}
        </button>

        {#if error}
          <div class="error">{error}</div>
        {/if}

        {#if results}
          <div class="results">
            <h3>Flight Emissions</h3>
            <div class="result-item">
              <span>Total CO2e:</span>
              <span>{results.co2e} {results.co2e_unit}</span>
            </div>
            
            <div class="result-details">
              <h4>Breakdown:</h4>
              <ul>
                {#each results.legs as leg}
                  <li>
                    <div>Flight Distance: {leg.activity_data.activity_value} {leg.activity_data.activity_unit}</div>
                    <div>CO2: {leg.constituent_gases.co2} kg</div>
                    <div>CH4: {leg.constituent_gases.ch4} kg</div>
                    <div>N2O: {leg.constituent_gases.n2o} kg</div>
                  </li>
                {/each}
              </ul>
            </div>
          </div>
        {/if}

        <div class="verify-link">
          <a href="https://calculator.carbonfootprint.com/calculator.aspx?c=flight" target="_blank" rel="noopener noreferrer">
            Verify your calculations
          </a>
        </div>
      </div>
    {/if}
  </div>
</main>

<style>
  main {
    min-height: 100vh;
  }

  .header-content {
    display: flex;
    align-items: center;
    gap: 1rem;
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 2rem;
  }

  .logo {
    height: 40px;
    width: auto;
  }

  .header {
    background-color: var(--dark);
    color: var(--white);
    padding: 1rem;
  }

  .header h1 {
    color: var(--primary);
    margin: 0;
    font-size: 2.5rem;
  }

  .container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 2rem;
  }

  .tab-container {
    display: flex;
    gap: 1rem;
    margin-bottom: 2rem;
  }

  .tab {
    padding: 0.5rem 1rem;
    background-color: var(--secondary);
    border: none;
    border-radius: 4px;
    cursor: pointer;
    color: var(--dark);
    font-weight: bold;
  }

  .tab.active {
    background-color: var(--primary);
    color: var(--white);
  }

  .edit-controls {
    text-align: right;
    margin-bottom: 1rem;
  }

  .edit-button {
    background-color: var(--dark);
    color: var(--white);
    border: none;
    padding: 0.5rem 1rem;
    border-radius: 4px;
    cursor: pointer;
    font-size: 0.9rem;
  }

  .edit-button:hover {
    background-color: var(--primary);
  }

  .markdown-editor {
    width: 100%;
    min-height: 400px;
    padding: 1rem;
    font-family: monospace;
    font-size: 14px;
    line-height: 1.6;
    border: 1px solid #ccc;
    border-radius: 4px;
    resize: vertical;
    background-color: #f8f8f8;
    color: var(--dark);
  }

  :global(.markdown-content) {
    font-size: 16px;
    line-height: 1.6;
    text-align: left;
  }

  :global(.markdown-content h1) {
    color: var(--dark);
    font-size: 2.5em;
    margin-bottom: 1em;
    text-align: left;
  }

  :global(.markdown-content h2) {
    color: var(--dark);
    font-size: 2em;
    margin-top: 1.5em;
    margin-bottom: 0.5em;
    text-align: left;
  }

  :global(.markdown-content h3) {
    color: var(--dark);
    font-size: 1.5em;
    margin-top: 1.2em;
    text-align: left;
  }

  :global(.markdown-content p) {
    margin-bottom: 1em;
    text-align: left;
  }

  :global(.markdown-content table) {
    width: 100%;
    border-collapse: collapse;
    margin: 1em 0;
    text-align: left;
  }

  :global(.markdown-content th),
  :global(.markdown-content td) {
    border: 1px solid #ddd;
    padding: 8px 12px;
    text-align: left;
  }

  :global(.markdown-content th) {
    background-color: var(--dark);
    color: var(--white);
  }

  :global(.markdown-content tr:nth-child(even)) {
    background-color: #f8f8f8;
  }

  :global(.markdown-content img) {
    max-width: 100%;
    height: auto;
    margin: 1em auto;
    border-radius: 4px;
    display: block;
  }

  :global(.markdown-content blockquote) {
    border-left: 4px solid var(--primary);
    padding-left: 1em;
    margin: 1em 0;
    color: #666;
    text-align: left;
  }

  :global(.markdown-content code) {
    background-color: #f0f0f0;
    padding: 2px 4px;
    border-radius: 3px;
    font-family: monospace;
  }

  :global(.markdown-content a) {
    color: var(--dark);
    text-decoration: underline;
  }

  :global(.markdown-content a:hover) {
    color: var(--primary);
  }

  :global(.markdown-content ul),
  :global(.markdown-content ol) {
    text-align: left;
    padding-left: 2em;
    margin-bottom: 1em;
  }

  :global(.markdown-content li) {
    text-align: left;
    margin-bottom: 0.5em;
  }

  .calculator-form {
    background-color: var(--secondary);
    padding: 2rem;
    border-radius: 8px;
    max-width: 600px;
    margin: 0 auto;
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

  .input, .select {
    width: 100%;
    padding: 0.75rem;
    border: 2px solid var(--dark);
    border-radius: 4px;
    font-size: 1rem;
    background-color: white;
    color: var(--dark);
  }

  .input::placeholder {
    color: #666;
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
    transition: background-color 0.2s;
  }

  .calculate-button:hover {
    background-color: var(--dark);
  }

  .calculate-button:disabled {
    background-color: #ccc;
    cursor: not-allowed;
  }

  .error {
    color: red;
    margin-top: 1rem;
  }

  .results {
    margin-top: 2rem;
    padding: 1.5rem;
    background-color: white;
    border-radius: 4px;
  }

  .result-item {
    display: flex;
    justify-content: space-between;
    margin: 0.5rem 0;
    font-size: 1.2rem;
  }

  .result-details {
    margin-top: 1.5rem;
  }

  .result-details ul {
    list-style: none;
    padding: 0;
  }

  .result-details li {
    padding: 1rem;
    background-color: var(--secondary);
    border-radius: 4px;
    margin-bottom: 0.5rem;
  }

  .verify-link {
    margin-top: 2rem;
    text-align: center;
  }

  .verify-link a {
    display: inline-block;
    padding: 0.75rem 1.5rem;
    background-color: var(--dark);
    color: var(--white);
    text-decoration: none;
    border-radius: 4px;
    font-weight: bold;
    transition: background-color 0.2s;
  }

  .verify-link a:hover {
    background-color: var(--primary);
  }
</style>
