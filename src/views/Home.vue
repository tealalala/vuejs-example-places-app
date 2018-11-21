<template>
  <div class="home">
    <h1>{{ message }}</h1>

    <h2>Number of Places: {{ places.length }}</h2>

    <hr>

    <h3>Create New Place</h3>
    <p>Name: <input type="text" v-model="newPlace.name"></p>
    <p>Address: <input type="text" v-model="newPlace.address"></p>
    <button v-on:click="addPlace()">Submit a new place</button>

    <hr>

    <ul style="list-style-type: none">
      <li v-for="place in places">
        <p>id: {{ place.id }}</p>
        <p>name: {{ place.name }}</p>
        <p>address: {{ place.address }}</p>
        <hr>
      </li>
    </ul>

  </div>
</template>

<style>
</style>

<script>
var axios = require('axios');

export default {
  data: function() {
    return {
      message: "Places Index",
      places: [],
      newPlace: {name: "", address: ""}
    };
  },
  created: function() {
    axios.get('http://localhost:3000/api/places').then(function(response) {
      console.log(response.data);
      this.places = response.data
    }.bind(this))
  },
  methods: {
    addPlace: function() {
      var params = {
        name: this.newPlace.name,
        address: this.newPlace.address
      }
      axios.post('http://localhost:3000/api/places', params).then(function(response) {
        console.log("add place");
        this.places.push(response.data);
      }.bind(this))
    }
  },
  computed: {}
};
</script>
