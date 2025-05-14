<script>
  import { onMount } from 'svelte';
</script>

<div class="verify-container">
  <h2>Verify Your Calculations</h2>
  <div class="calculator-embed">
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
  <div class="back-link">
    <a href="#/calculator" class="back-button">← Back to Calculator</a>
  </div>
</div>

<style>
  .verify-container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 2rem;
  }

  h2 {
    color: var(--dark);
    margin-bottom: 2rem;
    text-align: center;
  }

  .calculator-embed {
    background-color: var(--white);
    padding: 1rem;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }

  .back-link {
    margin-top: 2rem;
    text-align: center;
  }

  .back-button {
    display: inline-block;
    padding: 0.75rem 1.5rem;
    background-color: var(--dark);
    color: var(--white);
    text-decoration: none;
    border-radius: 4px;
    font-weight: bold;
    transition: background-color 0.2s;
  }

  .back-button:hover {
    background-color: var(--primary);
  }
</style> 