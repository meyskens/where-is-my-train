<template>
  <md-card class="md-layout-item md-size-100">
    <md-card-header>
      <div class="md-title">Trains now running</div>
    </md-card-header>
    <md-card-content>
      {{backoff}}
      <ul>
        <li v-for="trip in trips" v-bind:key="trip.id">
          {{trip.trip_short_name}}
          <ul v-for="(composition, index) in composition[trip.trip_id]" v-bind:key="index">
            <li>{{composition.confirmedBy}}: <span v-for="(unit, index) in composition.materialUnits" v-bind:key="index">{{unit.typeDescriptionNl}} {{unit.materialNumber}} - </span></li>
          </ul>
        </li>
      </ul>
    </md-card-content>
  </md-card>
</template>

<script>
import io from 'socket.io-client';

export default {
  name: 'home',
  data: function(){
    return {
      socket: null,
      trips: [],
      gotData: false,
      composition: {}, // key is the train number
      backoff: 0,
    }
  }, 
  methods: {
    getComposition: function (id) {
      if (this.composition[id]) {
        return
      }
      setTimeout(() => {
        this.$http.get(`https://trainmapjs.azureedge.net/data/composition/${id}`).then(response => {
          this.composition[id] = response.body
          console.log(this.composition)
          this.backoff -= 500
        }, err => {
          console.log(err)
          this.backoff -= 500
        });
      }, this.backoff)
      this.backoff += 500
    },
    onUpdate: function(data) {
      let trips = []

      let c = 0
      for (let id in data.trips) {
        if (data.trips.hasOwnProperty(id)) {
          c++
          trips.push(data.trips[id])
          if (c < 2000) {
            this.getComposition(id)
          } 
        }
      }

      this.trips = trips
      this.gotData = true
    },
  },
  created: function() {
    this.socket = io("https://trainmap.belgiantrain.be/");
    this.socket.on("connect", function() {
      console.log("connected to SNCB")
    });
    this.socket.on("event", this.onUpdate.bind(this));
    this.socket.on("disconnect", function() {
      console.log("disconnected from SNCB")
    });
  }
}
</script>
