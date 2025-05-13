<script>
  import { onMount } from 'svelte';
  import { marked } from 'marked';

  let activeTab = 'about';
  let markdownContent = `Bears on the Ground:   
**Aviation Emissions Reduction Framework for Academic and Research Expeditions (AERFARE)**
![Screenshot 2025-05-13 at 10.32.25 AM](https://hackmd.io/_uploads/SJ5-O0lble.png)


## **1: Introduction to AERFARE**

This guide is designed to support the IBES faculty's decision-making around academic travel by providing clear, evidence-based strategies to reduce aviation-related carbon emissions. Whether you're heading to a conference, invited to speak abroad, or considering local collaborations, this resource helps you weigh your options— from **virtual attendance and train routes to carbon offsetting and sustainable flight choices**.  
We recognize that travel is a vital part of your research and professional engagement. This framework doesn't aim to restrict your mobility; rather, it empowers you with tools to make informed, climate-conscious choices that align with IBES's commitment to environmental leadership.  
Every decision, no matter how small, can help reduce our collective footprint. *Let's lead by example.*

**How AERFARE Works:**  
Before you book a work-related flight, go through the flowchart on the cover page of this packet. Our decision tree will direct you to resources that can be used to assess the necessity of your trip, shift to a lower-emissions travel alternative, or offset your flight.  
Our team has spent the semester researching sustainable travel practices and calculating the emissions of flight routes common to IBES faculty. We hope this guide offers you a better understanding of your aviation emissions and encourages you to become a partner in reducing IBES's carbon footprint.  
As our project progresses, we aim to collect emissions reduction pledges from faculty and reduce a minimum of **10,000 lbs of CO2** associated with the department's aviation.

## 

## **2: Virtual Attendance and "Opting Out"**

In recent years, virtual participation in conferences, meetings, and guest lectures has become a widely accepted and often preferred alternative to in-person travel. Platforms like Zoom have not only made remote participation technically seamless but have also expanded access to academic engagement across geographic and financial barriers

### **Why Choose Virtual?**

Opting for virtual attendance helps avoid emissions entirely— unlike offsetting, which mitigates impact after the fact. Consider the following carbon savings from just one avoided round trip:

| Flight Category | Example Round Trip | Emissions Per Round Trip (including radiative forcing) | EPA Range (Secondary) |
| :---- | :---- | :---- | :---- |
| **Short-Haul** | Providence ⇄ New York (300 mi) | 132 lbs CO₂ | ~130–220 lbs CO₂ |
| **Domestic** | Providence ⇄ Chicago (1,600 mi) | 794 lbs CO₂ | ~1,100–1,400 lbs CO₂ |
| **International** | Providence ⇄ London (5,000 mi) | 4,695 lbs CO₂ | ~3,500–4,700 lbs CO₂ |

To put that in perspective using EPA emissions estimates: **just one international trip emits roughly as much CO₂** **as providing electricity to an average American home for 6 months**.

[... rest of the markdown content ...]`;  // Note: I'm truncating this in the example but you should include the full content

  let isEditing = false;

  function setActiveTab(tab) {
    activeTab = tab;
  }

  function toggleEdit() {
    isEditing = !isEditing;
  }

  // Configure marked options for better table support
  marked.use({
    gfm: true,
    breaks: true,
    headerIds: true
  });
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

  /* Enhanced markdown styling */
  :global(.markdown-content) {
    font-size: 16px;
    line-height: 1.6;
  }

  :global(.markdown-content h1) {
    color: var(--dark);
    font-size: 2.5em;
    margin-bottom: 1em;
  }

  :global(.markdown-content h2) {
    color: var(--dark);
    font-size: 2em;
    margin-top: 1.5em;
    margin-bottom: 0.5em;
  }

  :global(.markdown-content h3) {
    color: var(--dark);
    font-size: 1.5em;
    margin-top: 1.2em;
  }

  :global(.markdown-content p) {
    margin-bottom: 1em;
  }

  :global(.markdown-content table) {
    width: 100%;
    border-collapse: collapse;
    margin: 1em 0;
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
    margin: 1em 0;
    border-radius: 4px;
  }

  :global(.markdown-content blockquote) {
    border-left: 4px solid var(--primary);
    padding-left: 1em;
    margin: 1em 0;
    color: #666;
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
</style>
