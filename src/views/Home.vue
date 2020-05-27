<template>
  <div class="home">
    <div v-for="professor in professors">
      <h1 v-model="professor.name" v-on:click="showProfessor(professor)">{{ professor.name }}</h1>
      <div v-if="professor === currentProfessor">
        <h3>Title: <input type="text" v-model="professor.title"></h3>
        <h3>School: <input type="text" v-model="professor.school"></h3>
        <h3>Department: <input type="text" v-model="professor.department"></h3>
        <br>
        <h3>Review: <input type="text" v-model="newReviewText"></h3>
        <h3>Rate 1 to 100: <input type="text" v-model="newRating"></h3>
        <button v-on:click="createReview(professor)">Add Review</button>
        <br><br>
        <div>
          <button v-on:click="showCreateProfessor = !showCreateProfessor">Create Professor</button>
          <div v-if="showCreateProfessor">
          <h4>Create Professor Form</h4>
          Name: <input type="text" v-model="professor.name"><br>
          Title: <input type="text" v-model="professor.title"><br>
          School: <input type="text" v-model="professor.school"><br>
          Department: <input type="text" v-model="movie.department"><br>
          <button v-on:click="createProfessor()">Create Professor</button>
        </div>
        </div>
          <button v-on:click="updateProfessor(professor)">Update Professor</button>
        </div>
        <div>
          <button v-on:click="destroyProfessor(professor)">Delete Professor</button>
        </div>

        <div class="review" v-for='review in reviews' v-if='review.professor_id === currentProfessor.id'>
          <h4>Review: {{ review.text }}</h4>
          <h4>Rating: {{ review.rating }}</h4>

          <button v-on:click="review.showEditReviewForm = !review.showEditReviewForm">Edit Review</button>
          <div v-if="review.showEditReviewForm">
            <h4>Edit Review <input type="text" v-model="review.text"></h4>
            <h4>Edit Rating <input type="text" v-model="review.rating"></h4>

            <button v-on:click="updateReview(review)"> Update Review</button>
            <button v-on:click="destroyReview(review)">Delete Review</button>
          </div>
        </div>
        


      </div>
    </div>
  </div>
</template>


<style>
</style>

<script>
import axios from "axios"

export default {
  data: function() {
    return {
      professors: [],
      currentProfessor: {},
      reviews: [],
      newReviewText: "",
      newRating: ""
    };
  },

  created: function() {
    axios.get('/professors').then(response => {
      response.data.forEach(professor => {
        professor.showEditProfessorForm = false;
      });
      console.log(response.data);
      this.professors = response.data;
      showCreateProfessor = false;
    });
    axios.get('/reviews').then(response => {
      response.data.forEach(review => {
        review.showEditReviewForm = false;
      });
      console.log(response.data);
      this.reviews = response.data;
    });
  },
  methods: {
    createProfessor: function() {
      var clientParams = {
        name: this.name,
        title: this.title,
        school: this.school,
        department: this.department
      };

      axios
        .post("/professors/")
        .then(response => {
          console.log("Successful create!", response.data);
          this.reviews.push(response.data);
        })
        .catch(error => {
          console.log(error.response.data);
        });
      },
    showProfessor: function(inputProfessor) {
      if(this.currentProfessor !== inputProfessor) {
        this.currentProfessor = inputProfessor}
        else {
          this.currentProfessor = {};
        }
    },
    updateProfessor: function(inputProfessor) {
      var clientParams = {
        name: inputProfessor.name,
        title: inputProfessor.title,
        school: inputProfessor.school,
        department: inputProfessor.department 
      };

      axios
        .put("/professors/" + inputProfessor.id, clientParams)
        .then(response => {
          console.log("Successfully updated", response.data);
        }).catch(error => {
          console.log(error.response.data)
        })
    },
    destroyProfessor: function(inputProfessor) {
      axios
        .delete("/professors/" + inputProfessor.id)
        .then(response => {
          console.log("Successfully deleted", response.data);
          var index = this.professors.indexOf(inputProfessor);
          this.professors.splice(index, 1);
        });
    },
    createReview: function(professor) {
      var params = {
        professor_id: professor.id,
        text: this.newReviewText,
        rating: this.newRating
      };
      axios
        .post("/reviews/", params)
        .then(response => {
          console.log("Successful create!", response.data);
          this.reviews.push(response.data);
        })
        .catch(error => {
          console.log(error.response.data);
        });
    },
    updateReview: function(review) {
      var params = {
        text: review.text,
        rating: review.rating,
        professor_id: review.professor_id
      };
      axios
        .put(`/reviews/${review.id}`, params)
        .then(response => {
          console.log("Successful update!", response.data);
        })
        .catch(error => {
          console.log(error.response.data.errors);
        });

    },
    destroyReview: function(inputReview) {
      axios
        .delete("/reviews/" + inputReview.id)
        .then(response => {
          console.log("Successfully deleted", response.data);
          var index = this.reviews.indexOf(inputReview);
          this.reviews.splice(index, 1);
        });
    }
  }
}
</script>
