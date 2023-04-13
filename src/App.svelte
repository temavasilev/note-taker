<!-- Import the writable store and NotesList component -->
<script lang="ts">
  import { writable } from 'svelte/store';
  import NotesList from './NotesList.svelte';
  import { v4 as uuid } from 'uuid';

  // Variable to hold the new note content
  let newNoteContent: string = '';
  let newNoteTags: string = '';
  let newNoteCategory: string = '';

  // Create a writable store to hold the list of notes
  const notes = writable([]);

  // Function to add a new note to the notes store
  function addNote() {
  if (newNoteContent) {
    const tagsArray = newNoteTags
      ? newNoteTags.split(',').map((tag) => tag.trim())
      : [];

    $notes = [
      ...$notes,
      {
        id: uuid(),
        content: newNoteContent,
        tags: tagsArray, // use parsed tags array
        category: newNoteCategory, // include category
      },
    ];
    newNoteContent = '';
    newNoteTags = '';
    newNoteCategory = '';
  }
}
</script>

<!-- Main app layout -->
<main>
  <h1>Note-Taking App</h1>
  <div class="app-container">
    <NotesList notes={$notes} />
    <div class="note-editor">
      <textarea bind:value={newNoteContent} placeholder="Enter note content"></textarea>
      <input
        type="text"
        placeholder="Enter comma-separated tags"
        bind:value={newNoteTags}
      />
      <input
        type="text"
        placeholder="Enter a category"
        bind:value={newNoteCategory}
      />
      <button on:click={addNote}>Add Note</button>
    </div>
    </div>
</main>

<style>
  body {
    margin: 0;
    font-family: Arial, sans-serif;
  }

  main {
    display: flex;
    height: 100vh;
  }

  h1 {
    margin: 1rem;
  }

  .app-container {
    display: flex;
    flex-direction: row;
    flex-grow: 1;
    height: calc(100vh - 3rem); /* Subtract header height */
    width: 100%; /* Add full width */
  }

  .notes-list {
    width: 30%;
    border-right: 1px solid #dee2e6;
    overflow-y: auto;
    height: 100%;
    padding: 1rem;
    box-sizing: border-box; /* Add box-sizing to prevent overflow */
  }

  .note-editor {
    width: 70%;
    display: flex;
    flex-direction: column;
    align-items: stretch;
    height: 100%;
    padding: 1rem;
    box-sizing: border-box; /* Add box-sizing to prevent overflow */
  }

  .note-editor textarea {
    flex-grow: 1;
    resize: none;
    border: 1px solid #dee2e6;
    padding: 1rem;
    font-size: 1rem;
    margin-bottom: 1rem;
  }

  button {
    padding: 10px 20px;
    font-size: 1rem;
    background-color: #007bff;
    color: white;
    border: none;
    cursor: pointer;
    margin-bottom: 1rem;
  }

  button:hover {
    background-color: #0056b3;
  }

  .note {
    padding: 10px;
    background-color: #f8f9fa;
    border: 1px solid #dee2e6;
    margin-bottom: 1rem;
    position: relative;
  }

  .note button {
    position: absolute;
    top: 10px;
    right: 10px;
    background-color: #dc3545;
  }

  .note button:hover {
    background-color: #bd2130;
  }
</style>
