<!-- Import createEventDispatcher to handle custom events -->
<script lang="ts">
    import { createEventDispatcher } from 'svelte';
    import MarkdownIt from 'markdown-it';
  
    export let id: string;
    export let title: string;
    export let content: string;
    export let tags: string[];
    export let category: string;
  
    const dispatch = createEventDispatcher();
  
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
    <h3 class="note-title">{title}</h3>
    <div class="note-content">{@html parseMarkdown(content)}</div>
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
 .note {
  padding: 10px;
  background-color: #ebdbb2;
  border: 1px solid #d5c4a1;
  margin-bottom: 1rem;
  position: relative;
}

.note-title {
  margin: 0;
  margin-bottom: 0.5rem;
}

.note button {
  position: absolute;
  top: 10px;
  right: 10px;
  background-color: #d65d0e;
  color: #3c3836;
}

.note button:hover {
  background-color: #b14a00;
}

.note-content {
  margin-top: 0.5rem;
}

.note-tags {
  margin-top: 0.5rem;
}

.tag {
  background-color: #d79921;
  color: #3c3836;
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