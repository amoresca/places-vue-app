<template>
  <div class="home">
    <h1>Places to Visit in Philadelphia</h1>
    <div>
      <label for="">Name: </label><input type="text" v-model="newName" />
    </div>
    <div>
      <label for="">Address: </label><input type="text" v-model="newAddress" />
    </div>
    <button v-on:click="createPlace()">Add Place</button>
    <hr />
    <div v-for="place in places" :key="place.id">
      <h2>{{ place.name }}</h2>
      <p>{{ place.address }}</p>
      <button v-on:click="showPlace(place)">More Info</button>
      <hr />
    </div>
    <dialog>
      <form method="dialog">
        <h2>{{ currentPlace.name }}</h2>
        <p>{{ currentPlace.address }}</p>
        <hr />
        <div>
          <label for="">Name: </label
          ><input type="text" v-model="currentPlace.name" />
        </div>
        <div>
          <label for="">Address: </label
          ><input type="text" v-model="currentPlace.address" />
        </div>
        <button v-on:click="updatePlace()">Update</button>
        <hr />
        <button class="delete" v-on:click="destroyPlace()">Delete</button>
        <button class="close">X</button>
      </form>
    </dialog>
  </div>
</template>
<style scoped>
label {
  width: 75px;
  text-align: left;
  display: inline-block;
}
.close {
  position: absolute;
  top: 0;
  right: 0;
}
.delete {
  background-color: #ab180d;
  color: white;
}
</style>
<script>
import axios from "axios";

export default {
  data: function() {
    return {
      places: [],
      newName: "",
      newAddress: "",
      currentPlace: {},
    };
  },
  created: function() {
    axios.get("/api/places").then((response) => {
      this.places = response.data;
    });
  },
  methods: {
    createPlace: function() {
      var params = {
        name: this.newName,
        address: this.newAddress,
      };
      axios
        .post("/api/places", params)
        .then((response) => {
          // console.log(response.data);
          this.places.unshift(response.data);
          this.newName = "";
          this.newAddress = "";
        })
        .catch((error) => {
          console.log(error.response.data.errors);
        });
    },
    showPlace: function(place) {
      this.currentPlace = place;
      document.querySelector("dialog").showModal();
    },
    updatePlace: function() {
      var params = {
        name: this.currentPlace.name,
        address: this.currentPlace.address,
      };
      axios
        .patch(`/api/places/${this.currentPlace.id}`, params)
        .then((response) => {
          console.log(response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
        });
    },
    destroyPlace: function() {
      axios.delete(`/api/places/${this.currentPlace.id}`).then((response) => {
        console.log(response.data);
        var index = this.places.indexOf(this.currentPlace);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>
