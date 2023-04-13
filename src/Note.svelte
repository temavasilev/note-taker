<!-- Import createEventDispatcher to handle custom events -->
<script lang="ts">
    import { createEventDispatcher } from 'svelte';
  
    // Exported props for this component
    export let id: string;
    export let content: string;
  
    // Create a custom event dispatcher
    const dispatch = createEventDispatcher();
  
    // Function to dispatch a custom 'removeNote' event with the note ID
    function removeNote() {
      dispatch('removeNote', { id });
    }
  </script>
  
  <!-- Display the note content and a remove button -->
  <div class="note">
    <p>{content}</p>
    <button on:click={removeNote}>Remove</button>
  </div>
  