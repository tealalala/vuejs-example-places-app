<template>
  <div class="home">
    <h1>{{ message }}</h1>

    <h2>Number of Places: {{ places.length }}</h2>

    <ul style="list-style-type: none">
      <li v-for="place in places">
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
      places: {name: "", address: ""}
    };
  },
  created: function() {
    axios.get('http://localhost:3000/api/places').then(function(response) {
      console.log(response.data);
      this.places = response.data
    }.bind(this))
  },
  methods: {},
  computed: {}
};
</script>
