<template>
  <div class="home">
    <h1>Places</h1>
    <div>
      <p>Name:<input type="text" v-model="newPlaceParams.name" />
      </p>
      <p>Address:<input type="text" v-model="newPlaceParams.address" />
      </p>
    </div>
    <button v-on:click="createPlace()">Create</button>
    <div v-for="place in places" :key="place.id">
      <h2>Name: {{ place.name }}</h2>
      <p>Address: {{ place.address }}</p>
      <button v-on:click="showPlace(place)">More Info</button>
    </div>
    <dialog id="place-details">
      <form method="dialog">
        <h1>Place Info</h1>
        <p>Name:<input type="text" v-model="currentPlace.name" />
        </p>
        <p>Address:<input type="text" v-model="currentPlace.address" />
        </p>
        <button v-on:click="updatePlace(currentPlace)">Update</button>
        <button v-on:click="destroyPlace(currentPlace)">Delete!</button>
        <button>Close</button>
      </form>
    </dialog>
    <ul>
      <li v-for="error in errors" :key="error.id">{{ error }}</li>
    </ul>
  </div>
</template>
<style></style>
<script>
import axios from "axios";
export default {
  data: function () {
    return {
      message: "Welcome to Vue.js!",
      places: {},
      newPlaceParams: {},
      currentPlace: {},
      errors: [],
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("http://localhost:3000/places").then((response) => {
        this.places = response.data;
        console.log("All places:", this.places);
      });
    },
    createPlace: function () {
      console.log("Adding a Place!");
      axios
        .post("http://localhost:3000/places", this.newPlaceParams)
        .then((response) => {
          console.log("Yay!", response.data);
          this.places.push(response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function (place) {
      console.log(place);
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function (place) {
      var editPlaceParams = place;
      axios.patch("http://localhost:3000/places/" + place.id, editPlaceParams).then((response) => {
        console.log("success", response.data);
      });
    },
    destroyPlace: function (place) {
      axios.delete("http://localhost:3000/places/" + place.id).then((response) => {
        console.log("destroyed", response.data);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    },
  },
};
