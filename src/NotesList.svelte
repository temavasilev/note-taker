<script lang="ts">
    import Note from './Note.svelte';
  
    export let notes: { id: string; title: string; content: string; tags: string[]; category: string; notebook: string }[];

    export let removeNoteById: (id: string) => void;
  </script>
  
  <!-- Display the list of notes using the Note component -->  
  <div class="notes-list">
    <div class="notes-container">
      {#each notes as note (note.id)}
        <Note 
        {...note}
        on:removeNote={() => removeNoteById(note.id)}
        />
      {/each}
    </div>
  </div>
  
  <!-- Add the existing styles here -->
  
  <style>
    .notes-list {
    width: 100%;
    }

    .notes-container {
    max-height: 100%;
    overflow-y: auto;
    }

  </style>
  