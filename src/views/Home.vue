<template>
  <div class="home">
    <div v-for="professor in professors">
      <h1 v-on:click="showProfessor(professor)">{{ professor.name }}</h1>
      <div v-if="professor === currentProfessor">
        <h3>Tile: {{ professor.title }}</h3>
        <h3>School: {{ professor.school }}</h3>
        <h3>Department: {{ professor.department }}</h3>
        <div v-for='review in reviews' v-if='review.professor_id === currentProfessor.id'>
          <h4>Review: {{ review.text }}</h4>
          <h4>Rating: {{ review.rating }}</h4>
        </div>
      </div>
    </div>
  </div>
</template>


<style>
</style>

<script>
import axios from 'axios'

export default {
  data: function() {
    return {
      professors: [],
      currentProfessor: {},
      reviews: [],
      test: ""
    };
  },

  created: function() {
    axios.get('/professors').then(response => {
      this.professors = response.data;
    })
    axios.get('/reviews').then(response => {
      this.reviews = response.data;
    })
  },
  methods: {
    showProfessor: function(inputProfessor) {
      if(this.currentProfessor !== inputProfessor) {
        this.currentProfessor = inputProfessor}
        else {
          this.currentProfessor = {};
        }
    }
  }
}
</script>
