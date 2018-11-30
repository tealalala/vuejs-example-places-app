<template>
  <div class="home">

    <div id='map'></div>

    <!-- <h1>{{ message }}</h1>

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
    </ul> -->


  </div>
</template>

<style>
  body { margin:0; padding:0; }
  #map { position:absolute; top:0; bottom:0; width:100%}
  #marker {
      background-image: url('https://www.mapbox.com/mapbox-gl-js/assets/washington-monument.jpg');
      background-size: cover;
      width: 50px;
      height: 50px;
      border-radius: 50%;
      cursor: pointer;
  }

  .mapboxgl-popup {
      max-width: 200px;
  }
</style>

<script>
import axios from 'axios'

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
      this.places = response.data;
    }.bind(this));
  },
  mounted: function() {
    mapboxgl.accessToken = 'pk.eyJ1IjoidGVhbGFsYWxhIiwiYSI6ImNqcDNidG5tdDBnOWgza28xaTMwM2N3aHcifQ.U0K9bEF5ve-2gHZqg_ogwQ';

    // Basic Map
    // var map = new mapboxgl.Map({
    //   container: 'map', // container id
    //   style: 'mapbox://styles/mapbox/streets-v9', // stylesheet location
    //   center: [-74.50, 40], // starting position [lng, lat]
    //   zoom: 9 // starting zoom
    // });

    // Marker
    // var monument = [-77.0353, 38.8895];
    // var map = new mapboxgl.Map({
    //     container: 'map',
    //     style: 'mapbox://styles/mapbox/light-v9',
    //     center: monument,
    //     zoom: 12
    // });
    //
    // // create the popup
    // var popup = new mapboxgl.Popup({ offset: 25 })
    //     .setText('Construction on the Washington Monument began in 1848.');
    //
    // // create DOM element for the marker
    // var el = document.createElement('div');
    // el.id = 'marker';
    //
    // // create the marker
    // new mapboxgl.Marker(el)
    //     .setLngLat(monument)
    //     .setPopup(popup) // sets a popup on this marker
    //     .addTo(map);

    var map = new mapboxgl.Map({
        style: 'mapbox://styles/mapbox/light-v9',
        center: [-74.0066, 40.7135],
        zoom: 15.5,
        pitch: 45,
        bearing: -17.6,
        container: 'map'
    });

    map.on('load', function() {
        // Insert the layer beneath any symbol layer.
        var layers = map.getStyle().layers;

        var labelLayerId;
        for (var i = 0; i < layers.length; i++) {
            if (layers[i].type === 'symbol' && layers[i].layout['text-field']) {
                labelLayerId = layers[i].id;
                break;
            }
        }

        map.addLayer({
            'id': '3d-buildings',
            'source': 'composite',
            'source-layer': 'building',
            'filter': ['==', 'extrude', 'true'],
            'type': 'fill-extrusion',
            'minzoom': 15,
            'paint': {
                'fill-extrusion-color': '#aaa',

                // use an 'interpolate' expression to add a smooth transition effect to the
                // buildings as the user zooms in
                'fill-extrusion-height': [
                    "interpolate", ["linear"], ["zoom"],
                    15, 0,
                    15.05, ["get", "height"]
                ],
                'fill-extrusion-base': [
                    "interpolate", ["linear"], ["zoom"],
                    15, 0,
                    15.05, ["get", "min_height"]
                ],
                'fill-extrusion-opacity': .6
            }
        }, labelLayerId);
    });

  },
  methods: {
    addPlace: function() {
      var params = {
        name: this.newPlace.name,
        address: this.newPlace.address
      };
      axios.post('http://localhost:3000/api/places', params).then(function(response) {
        console.log("add place");
        this.places.push(response.data);
      }.bind(this));
    }
  },
  computed: {}
};
</script>
