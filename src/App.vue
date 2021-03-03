<template>
  <div class="row" id="app">
    <div
      class="col-3 col-md-2 col-lg-1 mt-2 border-right d-flex flex-column align-items-center"
    >
      <p class="lead">Docket</p>
      <button
        class="d-flex align-items-center justify-content-center"
        id="add-note"
        @click="addNewNote"
      >
        <img alt="" src="icons/plus.png" style="max-width: 90%" />
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
        <noteApp
          v-for="note in Notes"
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


<script>
import noteApp from "./components/noteApp.vue";

export default {
  name: "App",
  components: {
    noteApp,
  },
  data() {
    return {
      Notes: (JSON.parse(localStorage.getItem("note") ?? null) || []).filter(e=>{if(!(!e.text&&!e.title)){return e}}),
      searchNotes:'',
      searchNoteValue:''
    };
  },
  methods: {
    addNewNote: function () {
      this.Notes.unshift({
        title: "",
        text: "",
        time: this.getDate().time,
        date: this.getDate().Date,
        id: Math.random()*100000,
      });
      this._AddlocalStorage();
    },
    editNote: function(e){
      this.Notes.map(note=>{
        if(note.id === e.id){note=e;this._AddlocalStorage()}
      })
    },
    delNote: function (e) {
      this.Notes = this.Notes.filter(note=>{
        if(note.id !== e.id){return note}
      })
      this._AddlocalStorage();
    },
    _AddlocalStorage: function () {
      localStorage.setItem("note", JSON.stringify(this.Notes));
    },
    searchNote: function () {
      if(this.searchNoteValue){
        this.searchNotes = this.searchNotes || this.Notes
        this.Notes = this.searchNotes.filter(e=>{
          if(e.title.includes(this.searchNoteValue)){
            return e
          }
        })
      }else{
          this.Notes = this.searchNotes || this.Notes
          this.searchNotes=''
      }
    },
    getDate: function(){
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
          Date:formattedDate,
          time:time
        }
    }
  },
};
</script>



