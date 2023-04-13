<!-- Import createEventDispatcher to handle custom events -->
<script lang="ts">
    import { createEventDispatcher } from 'svelte';
    import MarkdownIt from 'markdown-it';
  
    // Exported props for this component
    export let id: string;
    export let content: string;
  
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
    <div class="note-content">{@html parseMarkdown(content)}</div>
    <button on:click={removeNote}>X</button>
  </div>
  