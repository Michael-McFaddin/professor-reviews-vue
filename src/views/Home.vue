<template>
  <div class="home">
    <div>
      <button v-on:click="showCreateProfessorForm = !showCreateProfessorForm">Add New Professor</button>
      <div v-if="showCreateProfessorForm">
        <h4>Create Professor Form</h4>
          Name: <input type="text" v-model="newProfessorName"><br>
          Title: <input type="text" v-model="newProfessorTitle"><br>
          School: <input type="text" v-model="newProfessorSchool"><br>
          Department: <input type="text" v-model="newProfessorDepartment"><br>
        <button v-on:click="createProfessor()">Create Professor</button>
      </div>
    </div>

    <div v-for="professor in professors">
      <h1 v-model="professor.name" v-on:click="showProfessor(professor)">{{ professor.name }}</h1>
      <div v-if="professor === currentProfessor">
        <h3>Title: {{professor.title}}</h3>
        <h3>School: {{professor.school}}</h3>
        <h3>Department: {{professor.department}}</h3>
        <br>

        <button v-on:click="showCreateReviewForm = !showCreateReviewForm">Add New Review</button>
        <div v-if="showCreateReviewForm">
          <h3>Review: <input type="text" v-model="newReviewText"></h3>
          <h3>Rate 1 to 100: <input type="text" v-model="newRating"></h3>
          <button v-on:click="createReview(professor)">Create Review</button>
        </div>

        <button v-on:click="professor.showEditProfessorForm = !professor.showEditProfessorForm">Update Professor</button>
        <div v-if="professor.showEditProfessorForm">
          <h3>Title: <input type="text" v-model="professor.title"></h3>
          <h3>School: <input type="text" v-model="professor.school"></h3>
          <h3>Department: <input type="text" v-model="professor.department"></h3>
          <button v-on:click="updateProfessor(professor)">Save Edits</button>
        </div>

        <button v-on:click="destroyProfessor(professor)">Delete Professor</button>


        <br><br>


        <div class="review" v-for='review in professor.reviews'>
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
      newReviewText: "",
      newRating: "",
      newProfessorName: "",
      newProfessorTitle: "",
      newProfessorSchool: "",
      newProfessorDepartment: "",
      showCreateProfessorForm: false,
      showCreateReviewForm: false
    };
  },

  created: function() {
    axios.get('/professors').then(response => {
      response.data.forEach(professor => {
        professor.showEditProfessorForm = false;
        professor.reviews.map(review => {
          review.showEditReviewForm = false;
          return review;
        })
      });
      console.log(response.data);
      this.professors = response.data;
    });
    // axios.get('/reviews').then(response => {
    //   response.data.forEach(review => {
    //     review.showEditReviewForm = false;
    //   });
    //   console.log(response.data);
    //   this.reviews = response.data;
    // });
  },
  methods: {
    createProfessor: function() {
      var clientParams = {
        name: this.newProfessorName,
        title: this.newProfessorTitle,
        school: this.newProfessorSchool,
        department: this.newProfessorDepartment
      };

      axios
        .post("/professors/", clientParams)
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
