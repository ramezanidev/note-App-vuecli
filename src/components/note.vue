<template>
  <div
    ref="el"
    class="card col-md-6 mx-2 my-2 note transition"
    :class="{ showNoteMode: showNoteMode && newNoteBl }"
  >
    <div class="card-body">
      <div class="d-flex justify-content-center">
        <button @click="delNote" class="btn m-0 p-0 shadow-none delete-note">
          <img alt class="m-0 p-0" src="icons/close.png" style="max-width: 45%" />
        </button>
        <input
          :readonly="showNoteMode && newNoteBl"
          class="card-title mt-0 text-center note-title"
          maxlength="17"
          type="text"
          v-model="noteObject.title"
        />
      </div>
      <label for="content"></label>
      <textarea
        class="note-text"
        cols="30"
        :readonly="showNoteMode && newNoteBl"
        dir="auto"
        id="content"
        rows="8"
        v-model="noteObject.text"
      ></textarea>
      <div class="d-flex justify-content-between align-items-center mb-4 mt-2">
        <div class="d-flex flex-column justify-content-center">
          <p class="m-0 note-date">{{ noteObject.date }}</p>
          <small class="note-time">{{ noteObject.time }}</small>
        </div>
        <button
          class="btn shadow-none m-0 p-0 btn-note d-flex justify-content-center align-items-center"
        >
          <img
            @click="editNote"
            alt
            class="d-none edit-btn"
            src="icons/pen.png"
            style="max-width: 60%"
          />
          <img @click="saveNote" src="icons/check.png" class="check-btn mw-100" alt />
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { defineProps, ref, defineEmits } from 'vue'

const props = defineProps({
  noteObj: Object,
  newNote: Boolean,
})

const emit = defineEmits(["edit-note", "del-note"])

const el = ref(null)

const noteObject = ref(props.noteObj)
const newNoteBl = ref(props.newNote)
const showNoteMode = ref(true)

const editNote = () => {
  showNoteMode.value = newNoteBl.value = false
}

const delNote = () => {
  el.value.style.opacity = '0';
  setTimeout(() => {
    emit('del-note', noteObject.value)
  }, 300);
}

const saveNote = () => {
  showNoteMode.value = newNoteBl.value = true
  emit('edit-note', noteObject.value)
}
</script>
