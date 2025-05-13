<script>
  import { onMount } from 'svelte';
  import { marked } from 'marked';

  let activeTab = 'about';
  let markdownContent = `# Bears On The Ground

Welcome to our initiative to track and reduce our carbon footprint. This tool helps you:

- Calculate your carbon footprint
- Track your environmental impact
- Make informed decisions about travel and daily activities

More detailed documentation coming soon.`;

  let isEditing = false;

  function setActiveTab(tab) {
    activeTab = tab;
  }

  function toggleEdit() {
    isEditing = !isEditing;
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
        <div class="edit-controls">
          <button class="edit-button" on:click={toggleEdit}>
            {isEditing ? 'Preview' : 'Edit Content'}
          </button>
        </div>
        
        {#if isEditing}
          <textarea 
            bind:value={markdownContent}
            class="markdown-editor"
            placeholder="Enter your markdown content here..."
          ></textarea>
        {:else}
          {@html marked(markdownContent)}
        {/if}
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
    {/if}
  </div>
</main>

<style>
  main {
    min-height: 100vh;
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
</style>
