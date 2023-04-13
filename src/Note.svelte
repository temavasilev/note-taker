<!-- Import createEventDispatcher to handle custom events -->
<script lang="ts">
    import { createEventDispatcher } from 'svelte';
    import MarkdownIt from 'markdown-it';
  
    // Exported props for this component
    export let id: string;
    export let content: string;
    export let tags: string[];
    export let category: string;
  
    // Create a custom event dispatcher
    const dispatch = createEventDispatcher();
  
    // Function to dispatch a custom 'removeNote' event with the note ID
    function removeNote() {
      dispatch('removeNote', { id });
    }

    const md = new MarkdownIt();

    function parseMarkdown(content: string) {
      return md.render(content);
    }

  </script>
  
  <!-- Display the note content and a remove button -->
  <div class="note">
    <div class="note-content">{@html parseMarkdown(content)}></div>
    <div class="note-tags">
      {#each tags as tag}
        <span class="tag">{tag}</span>
      {/each}
    </div>
    {#if category}
      <div class="note-category">Category: {category}</div>
    {/if}
    <button on:click={removeNote}>X</button>
  </div>
  
  <style>
    /* ... */
    .note-tags {
      margin-top: 0.5rem;
    }
  
    .tag {
      background-color: #007bff;
      color: white;
      padding: 2px 6px;
      border-radius: 3px;
      font-size: 0.8rem;
      margin-right: 4px;
    }

    .note-category {
      margin-top: 0.5rem;
      font-size: 0.8rem;
    }
</style>