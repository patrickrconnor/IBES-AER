<script>
  import { onMount } from 'svelte';
  import { marked } from 'marked';

  let activeTab = 'about';
  let markdownContent = '';

  onMount(async () => {
    const response = await fetch('../README.md');
    markdownContent = await response.text();
  });

  function setActiveTab(tab) {
    activeTab = tab;
  }
</script>

<main>
  <header class="header">
    <h1>Bears On The Ground</h1>
  </header>

  <div class="container">
    <div class="tab-container">
      <button 
        class="tab" 
        class:active={activeTab === 'about'} 
        on:click={() => setActiveTab('about')}
      >
        About
      </button>
      <button 
        class="tab" 
        class:active={activeTab === 'calculator'} 
        on:click={() => setActiveTab('calculator')}
      >
        Carbon Calculator
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
          src="https://calculator.carbonfootprint.com/calculator.aspx"
        ></iframe>
      </div>
    {/if}
  </div>
</main>

<style>
  main {
    min-height: 100vh;
  }
</style>
