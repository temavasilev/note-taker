<script lang="ts">
  import { writable, derived } from 'svelte/store';
  import NotesList from './NotesList.svelte';
  import { v4 as uuid } from 'uuid';
  import Note from './Note.svelte';

  type NoteType = {
    id: string;
    content: string;
    tags: string[];
    category: string;
  };

  let filterQuery: string = '';
  let sortBy: string = 'date-desc';

  let newNoteContent: string = '';
  let newNoteTags: string = '';
  let newNoteCategory: string = '';

// Load notes from local storage or initialize an empty array if not found
const storedNotes = localStorage.getItem('notes');
const notes = writable(storedNotes ? JSON.parse(storedNotes) : []);


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
        tags: tagsArray,
        category: newNoteCategory,
      },
    ];

    // Save notes to local storage
    localStorage.setItem('notes', JSON.stringify($notes));

    newNoteContent = '';
    newNoteTags = '';
    newNoteCategory = '';
  }
}

  function removeNoteById(id: string) {
    $notes = $notes.filter((n: NoteType) => n.id !== id);
    localStorage.setItem('notes', JSON.stringify($notes));
  }

  const sortedFilteredNotes = derived(notes, ($notes: NoteType[]) => {
    let filteredNotes: NoteType[] = $notes;

    if (filterQuery) {
      const lowerCaseFilter = filterQuery.toLowerCase();
      filteredNotes = $notes.filter(
        (note: NoteType) =>
          note.content.toLowerCase().includes(lowerCaseFilter) ||
          note.tags.some((tag: string) => tag.toLowerCase().includes(lowerCaseFilter)) ||
          note.category.toLowerCase().includes(lowerCaseFilter)
      );
    }

    switch (sortBy) {
      case 'date-asc':
        return filteredNotes.sort((a: NoteType, b: NoteType) => a.id.localeCompare(b.id));
      case 'date-desc':
        return filteredNotes.sort((a: NoteType, b: NoteType) => b.id.localeCompare(a.id));
      case 'category':
        return filteredNotes.sort((a: NoteType, b: NoteType) => a.category.localeCompare(b.category));
      default:
        return filteredNotes;
    }
  });
</script>

<!-- Main app layout -->
<main>
  <div class="app-container">
    <NotesList
    bind:notes={$sortedFilteredNotes}
    {removeNoteById}
  />
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
    <div class="controls">
      <input
        type="text"
        placeholder="Filter notes"
        bind:value={filterQuery}
      />
      <select bind:value={sortBy}>
        <option value="date-desc">Date (Newest)</option>
        <option value="date-asc">Date (Oldest)</option>
        <option value="category">Category</option>
      </select>
    </div>
  </div>
</main>

<!-- Add the existing styles here -->

<style>
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background-color: #fbf1c7;
  color: #3c3836;
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
  flex: 1 0 auto;
  height: calc(100vh - 3rem); /* Subtract header height */
  width: 100%; /* Add full width */
}

.controls {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-between;
  width: 100%;
  margin-bottom: 1rem;
}

.notes-list {
  width: 30%;
  border-right: 1px solid #d5c4a1;
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
  flex: 1 0 auto;
  resize: none;
  border: 1px solid #d5c4a1;
  padding: 1rem;
  font-size: 1rem;
  margin-bottom: 1rem;
}

button {
  padding: 10px 20px;
  font-size: 1rem;
  background-color: #d79921;
  color: #3c3836;
  border: none;
  cursor: pointer;
  margin-bottom: 1rem;
}

button:hover {
  background-color: #b57614;
}

</style>
