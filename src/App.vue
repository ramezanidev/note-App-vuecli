<template>
  <div class="row" id="app">
    <div class="col-3 col-md-2 col-lg-1 mt-2 border-right d-flex flex-column align-items-center">
      <p class="lead">Docket</p>
      <button
        class="d-flex align-items-center justify-content-center"
        id="add-note"
        @click="addNewNote"
      >
        <img alt src="icons/plus.png" style="max-width: 90%" />
      </button>
    </div>
    <div class="col-7 col-md-8 col-lg-10 mt-2 ms-2">
      <div>
        <form @submit.prevent="searchNote">
          <div class="my-0">
            <label class="col-form-label" for="search-note"></label>
            <input
              class="border-0 form-control shadow-none"
              id="search-note"
              placeholder="Search..."
              type="text"
              @keyup="searchNote"
              v-model="searchNoteValue"
            />
          </div>
        </form>
        <h1 class="mt-3">Notes</h1>
      </div>
      <div class="row text-center justify-content-center note-container">
        <Note
          v-for="note in notes"
          :key="note.id"
          :noteObj="note"
          :newNote="note.text ? true : false"
          @edit-note="editNote"
          @del-note="delNote"
        />
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import Note from "./components/note.vue";

const notes = ref([]);
const searchNotes = ref('');
const searchNoteValue = ref('');

const localNotes = JSON.parse(localStorage.getItem("note") ?? null) || []
// removed empty notes
notes.value = localNotes.filter(e => { if (!(!e.text && !e.title)) { return e } })


const addNewNote = () => {
  notes.value.unshift({
    title: "",
    text: "",
    time: getDate().time,
    date: getDate().Date,
    id: Math.random() * 100000,
  });
  _AddlocalStorage();
}

const editNote = (e) => {
  notes.value.map(note => {
    if (note.id === e.id) { note = e; _AddlocalStorage() }
  })
}

const delNote = (e) => {
notes.value = notes.value.filter(note => {
    if (note.id !== e.id) { return note }
  })
  _AddlocalStorage();
}

const _AddlocalStorage = () => {
  localStorage.setItem("note", JSON.stringify(notes.value));
}

const searchNote = () => {
  if (searchNoteValue.value) {
    searchNotes.value = searchNotes.value || notes.value
    notes.value = searchNotes.value.filter(e => {
      if (e.title.includes(searchNoteValue.value)) {
        return e
      }
    })
  } else {
    notes.value = searchNotes.value || notes.value
    searchNotes.value = ''
  }
}

const getDate = () => {
  const now = new Date();
  const options = {
    month: 'short',
    day: 'numeric',
    year: 'numeric',
  };
  const hour = String(now.getHours()).padStart(2, '0');
  const minutes = String(now.getMinutes()).padStart(2, '0');
  const time = `${hour}:${minutes}`;
  const formattedDate = new Intl.DateTimeFormat('en-US', options).format(now);
  return {
    Date: formattedDate,
    time: time
  }
}
</script>
