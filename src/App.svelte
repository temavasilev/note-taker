<script lang="ts">
  import { writable, derived } from 'svelte/store';
  import NotesList from './NotesList.svelte';
  import { v4 as uuid } from 'uuid';

  type NoteType = {
    id: string;
    title: string;
    content: string;
    tags: string[];
    category: string;
    notebook: string;
  };

  let filterQuery: string = '';
  let sortBy: string = 'date-desc';

  let newNoteTitle: string = '';
  let newNoteContent: string = '';
  let newNoteTags: string = '';
  let newNoteCategory: string = '';

  let newNotebookName: string = '';
  let selectedNotebook: string = '';

  const notes = writable([]);
  const notebooks = writable([]);

  function addNote() {
    if (newNoteTitle && newNoteContent && selectedNotebook) {
      const tagsArray = newNoteTags
        ? newNoteTags.split(',').map((tag) => tag.trim())
        : [];

      $notes = [
        ...$notes,
        {
          id: uuid(),
          title: newNoteTitle,
          content: newNoteContent,
          tags: tagsArray,
          category: newNoteCategory,
          notebook: selectedNotebook,
        },
      ];
      newNoteTitle = '';
      newNoteContent = '';
      newNoteTags = '';
      newNoteCategory = '';
    }
  }

  function removeNoteById(id: string) {
    $notes = $notes.filter((note) => note.id !== id);
  }

  function addNotebook() {
    if (newNotebookName) {
      $notebooks = [...$notebooks, newNotebookName];
      selectedNotebook = newNotebookName;
      newNotebookName = '';
    }
  }

  const sortedFilteredNotes = derived(notes, ($notes: NoteType[]) => {
    let filteredNotes: NoteType[] = $notes.filter(
      (note: NoteType) => note.notebook === selectedNotebook
    );

    if (filterQuery) {
      const lowerCaseFilter = filterQuery.toLowerCase();
      filteredNotes = filteredNotes.filter(
        (note: NoteType) =>
          note.content.toLowerCase().includes(lowerCaseFilter) ||
          note.tags.some((tag: string) =>
            tag.toLowerCase().includes(lowerCaseFilter)
          ) ||
          note.category.toLowerCase().includes(lowerCaseFilter)
      );
    }

    switch (sortBy) {
      case 'date-asc':
        return filteredNotes.sort((a: NoteType, b: NoteType) =>
          a.id.localeCompare(b.id)
        );
      case 'date-desc':
        return filteredNotes.sort((a: NoteType, b: NoteType) =>
          b.id.localeCompare(a.id)
        );
      case 'category':
        return filteredNotes.sort((a: NoteType, b: NoteType) =>
          a.category.localeCompare(b.category)
        );
      default:
        return filteredNotes;
    }
  });
</script>

<!-- Main app layout -->
<main>
  <h1>Note-Taking App</h1>
  <div class="app-container">
    <NotesList 
      bind:notes={$sortedFilteredNotes}
      {removeNoteById} />
    <div class="note-editor">
      <input
        type="text"
        placeholder="Enter note title"
        bind:value={newNoteTitle}
      />
      <textarea
        bind:value={newNoteContent}
        placeholder="Enter note content"
      ></textarea>
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
        <div class="notebook-controls">
          <select bind:value={selectedNotebook}>
            <option value="" disabled>Select a notebook</option>
            {#each $notebooks as notebook}
              <option value={notebook}>{notebook}</option>
            {/each}
          </select>
          <input
            type="text"
            placeholder="New notebook name"
            bind:value={newNotebookName}
          />
          <button on:click={addNotebook}>Add Notebook</button>
        </div>
      </div>
    </div>
  </main>

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
  resize: none;
  width: 70%;
  flex: 1 0 auto;
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
