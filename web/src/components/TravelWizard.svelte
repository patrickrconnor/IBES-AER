<script>
  import { onMount } from 'svelte';

  let currentStep = 1;
  let answers = {
    isConferenceOrMeeting: null,
    canBeVirtual: null,
    isShortHaul: null,
    hasGroundTransport: null,
    willOffset: null
  };

  let recommendation = '';
  let emailTemplate = '';

  function updateRecommendation() {
    if (answers.canBeVirtual) {
      recommendation = 'Virtual Attendance Recommended';
      emailTemplate = `Subject: Request for Virtual Participation

Dear [Organizer's Name],

I am honored to have been invited to [present/attend] [Conference/Event Name]. I'm currently working with Brown University's Institute at Brown for Environment and Society (IBES) to reduce academic aviation emissions, as the department is trying to minimize unnecessary flights where virtual participation is possible. As part of this initiative, I've pledged to minimize flight-related carbon emissions wherever feasible.

In support of this commitment, I'm reaching out to ask whether remote participation would be possible for [presentation/session]. I would be glad to engage fully via [Zoom/Teams/etc.] and ensure that my contribution maintains the same level of quality and collaboration as in-person attendance.

Thank you for considering this request, and for any flexibility you can offer.

Warm regards,
[Your Name]
[Your Title/Position]
Institute at Brown for Environment and Society`;
    } else if (answers.isShortHaul && answers.hasGroundTransport) {
      recommendation = 'Ground Transportation Recommended';
    } else {
      recommendation = 'Flight May Be Necessary - Consider These Options:';
    }
  }

  function nextStep() {
    currentStep++;
    updateRecommendation();
  }

  function prevStep() {
    currentStep--;
  }

  function resetWizard() {
    currentStep = 1;
    answers = {
      isConferenceOrMeeting: null,
      canBeVirtual: null,
      isShortHaul: null,
      hasGroundTransport: null,
      willOffset: null
    };
    recommendation = '';
    emailTemplate = '';
  }
</script>

<div class="wizard-container">
  <h2>Travel Decision Wizard</h2>
  
  {#if currentStep === 1}
    <div class="step-container">
      <h3>Step 1: Event Type</h3>
      <p>Is this for a conference, meeting, or presentation?</p>
      <div class="button-group">
        <button 
          class:selected={answers.isConferenceOrMeeting === true}
          on:click={() => { answers.isConferenceOrMeeting = true; nextStep(); }}
        >
          Yes
        </button>
        <button 
          class:selected={answers.isConferenceOrMeeting === false}
          on:click={() => { answers.isConferenceOrMeeting = false; nextStep(); }}
        >
          No
        </button>
      </div>
    </div>
  {:else if currentStep === 2}
    <div class="step-container">
      <h3>Step 2: Virtual Possibility</h3>
      <p>Can this event be attended virtually without significant impact on its purpose?</p>
      <div class="button-group">
        <button 
          class:selected={answers.canBeVirtual === true}
          on:click={() => { answers.canBeVirtual = true; nextStep(); }}
        >
          Yes
        </button>
        <button 
          class:selected={answers.canBeVirtual === false}
          on:click={() => { answers.canBeVirtual = false; nextStep(); }}
        >
          No
        </button>
      </div>
    </div>
  {:else if currentStep === 3}
    <div class="step-container">
      <h3>Step 3: Travel Distance</h3>
      <p>Is this a short-haul trip (under 500 miles)?</p>
      <div class="button-group">
        <button 
          class:selected={answers.isShortHaul === true}
          on:click={() => { answers.isShortHaul = true; nextStep(); }}
        >
          Yes
        </button>
        <button 
          class:selected={answers.isShortHaul === false}
          on:click={() => { answers.isShortHaul = false; nextStep(); }}
        >
          No
        </button>
      </div>
    </div>
  {:else if currentStep === 4 && answers.isShortHaul}
    <div class="step-container">
      <h3>Step 4: Ground Transportation</h3>
      <p>Are there viable ground transportation options (train, bus, carpool)?</p>
      <div class="button-group">
        <button 
          class:selected={answers.hasGroundTransport === true}
          on:click={() => { answers.hasGroundTransport = true; nextStep(); }}
        >
          Yes
        </button>
        <button 
          class:selected={answers.hasGroundTransport === false}
          on:click={() => { answers.hasGroundTransport = false; nextStep(); }}
        >
          No
        </button>
      </div>
    </div>
  {:else}
    <div class="recommendation-container">
      <h3>Recommendation</h3>
      <div class="recommendation">
        <h4>{recommendation}</h4>
        
        {#if answers.canBeVirtual}
          <div class="virtual-resources">
            <h4>Email Template for Virtual Request</h4>
            <textarea readonly value={emailTemplate}></textarea>
            <button on:click={() => navigator.clipboard.writeText(emailTemplate)}>
              Copy Email Template
            </button>
          </div>
        {:else if answers.isShortHaul && answers.hasGroundTransport}
          <div class="transport-options">
            <h4>Ground Transportation Options:</h4>
            <ul>
              <li>
                <a href="https://www.amtrak.com" target="_blank" rel="noopener noreferrer">
                  Amtrak Train Service
                </a>
              </li>
              <li>
                <a href="https://peterpanbus.com" target="_blank" rel="noopener noreferrer">
                  Peter Pan Bus Lines
                </a>
              </li>
              <li>
                <a href="https://www.greyhound.com" target="_blank" rel="noopener noreferrer">
                  Greyhound Bus Service
                </a>
              </li>
            </ul>
          </div>
        {:else}
          <div class="flight-recommendations">
            <h4>Tips for Lower-Emission Flying:</h4>
            <ul>
              <li>Book economy class (up to 3x less emissions than business class)</li>
              <li>Choose non-stop flights when possible</li>
              <li>Consider using these sustainable airlines:
                <ul>
                  <li>Alaska Airlines</li>
                  <li>United Airlines</li>
                  <li>American Airlines</li>
                  <li>Delta Airlines</li>
                </ul>
              </li>
              <li>Offset your flight emissions using a verified provider</li>
            </ul>
          </div>
        {/if}
      </div>
      
      <div class="button-group">
        <button on:click={resetWizard}>Start Over</button>
        {#if !answers.canBeVirtual}
          <button on:click={() => window.location.href = '#calculator'}>
            Calculate Emissions
          </button>
        {/if}
      </div>
    </div>
  {/if}

  {#if currentStep > 1 && currentStep < 5}
    <div class="navigation">
      <button on:click={prevStep}>Back</button>
    </div>
  {/if}
</div>

<style>
  .wizard-container {
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

  .step-container {
    text-align: center;
    margin-bottom: 2rem;
  }

  .button-group {
    display: flex;
    gap: 1rem;
    justify-content: center;
    margin-top: 1.5rem;
  }

  button {
    padding: 0.75rem 1.5rem;
    border: 2px solid var(--dark);
    border-radius: 4px;
    background-color: white;
    color: var(--dark);
    font-weight: bold;
    cursor: pointer;
    transition: all 0.2s;
  }

  button:hover {
    background-color: var(--dark);
    color: white;
  }

  button.selected {
    background-color: var(--primary);
    border-color: var(--primary);
    color: white;
  }

  .recommendation-container {
    text-align: left;
  }

  .recommendation {
    background-color: var(--secondary);
    padding: 2rem;
    border-radius: 8px;
    margin: 1.5rem 0;
  }

  .virtual-resources textarea {
    width: 100%;
    height: 300px;
    margin: 1rem 0;
    padding: 1rem;
    border: 1px solid #ccc;
    border-radius: 4px;
    font-family: monospace;
    resize: vertical;
  }

  .transport-options ul,
  .flight-recommendations ul {
    list-style: none;
    padding-left: 0;
  }

  .transport-options li,
  .flight-recommendations li {
    margin-bottom: 0.5rem;
  }

  .transport-options a {
    color: var(--dark);
    text-decoration: underline;
  }

  .transport-options a:hover {
    color: var(--primary);
  }

  .navigation {
    margin-top: 2rem;
    text-align: center;
  }

  @media (max-width: 600px) {
    .wizard-container {
      padding: 1rem;
    }

    .button-group {
      flex-direction: column;
    }

    button {
      width: 100%;
    }
  }
</style> 