<!-- Import the Note component and onMount lifecycle function -->
<script lang="ts">
    import { onMount } from 'svelte';
    import Note from './Note.svelte';
  
    // Exported prop for the list of notes
    export let notes: { id: string; content: string }[];
  
    // Initialize notes on component mount
    onMount(() => {
      if (!notes) {
        notes = [];
      }
    });
  </script>
  
  <!-- Display the list of notes using the Note component -->  
  <div class="notes-list">
    <div class="notes-container">
      {#each notes as note (note.id)}
        <Note {...note} on:removeNote={(event) => (notes = notes.filter((n) => n.id !== event.detail.id))} />
      {/each}
    </div>
  </div>

  <style>
    .notes-list {
      width: 100%;
    }
  
    .notes-container {
      max-height: 100%;
      overflow-y: auto;
    }
  </style>
  