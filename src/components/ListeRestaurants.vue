<template>
  <div class="hello">
    <app-details-restaurant
      v-bind:restauSelected="restauSelected"
      v-show="showModal"
      @close="showModal = false"
    ></app-details-restaurant>
    <div class="row">
      <div class="col-8">
        <form v-on:submit="methodForm(event);">
          <div class="form-group">
            <label>Nom :</label>
            <input required class="form-control" name="nom" id="nom" type="text" v-model="name">
          </div>

          <div class="form-group">
            <label>Cuisine :</label>
            <input
              required
              class="form-control"
              name="cuisine"
              id="cuisine"
              type="text"
              v-model="cuisine"
            >
            <input type="hidden" name="ids" v-model="ids" id="ids">
          </div>

          <div class="form-group">
            <button class="form-control btn btn-success">Ajouter</button>
          </div>
        </form>
        <h1>nombre de restaurants : {{ nbreResto }}</h1>
      </div>
      <div class="col-4"></div>
    </div>

    <div class="row">
      <div class="col-8">
        <div class="form-group">
          <label for="pagesize">
            <h6>
              Nombre de restaurants par page :
              <span class="badge badge-pill badge-success">
                {{
                pagesize
                }}
              </span>
            </h6>
          </label>
          <input
            v-model="pagesize"
            v-on:change="getRestaurantsFromServer()"
            type="range"
            class="custom-range form-control"
            min="5"
            max="100"
            step="5"
            value="10"
            id="pagesize"
          >
        </div>
        <div class="form-group">
          <label for="name">
            <h6>Trier par nom de restaurant :</h6>
          </label>
          <input v-model="name" v-on:keypress="reload()" type="text" class="form-control" id="name">
        </div>
      </div>
      <div class="col-4"></div>
    </div>
    <table>
      <thead>
        <tr>
          <th>Nom</th>
          <th>Cuisine</th>
          <th colspan="2">Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(r,index) in restaurants" v-bind:key="index">
          <td
            title="Afficher les détails"
            style="cursor: pointer"
            @click="afficherDetailsRestau(r)"
          >
            <strong>{{ r.name }}</strong>
          </td>
          <td
            title="Afficher les détails"
            style="cursor: pointer"
            @click="afficherDetailsRestau(r)"
          >
            <a>{{ r.cuisine }}</a>
          </td>

          <td>
            <button
              class="btn btn-success"
              title="Modifier"
              v-on:click="getRestaurantById(r._id)"
              href
            >
              <font-awesome-icon icon="edit"/>
            </button>
          </td>
          <td>
            <button
              class="btn btn-danger"
              title="Supprimer"
              v-on:click="supprimerRestaurant(r._id)"
              href
            >
              <font-awesome-icon icon="trash"/>
            </button>
          </td>
        </tr>
      </tbody>
      <tfoot>
        <tr>
          <th>Nom</th>
          <th>Cuisine</th>
          <th colspan="2">Action</th>
        </tr>
      </tfoot>
    </table><br>
    <button title="Première page" class="btn btn-danger" v-on:click="firstPage()">
      <font-awesome-icon icon="fast-backward"/>
    </button>
    <button
      title="Précédent"
      class="btn btn-primary"
      v-bind:disabled="precedentDisable()"
      v-on:click="precedent()"
    >
      <font-awesome-icon icon="backward"/>
    </button>
    <button
      title="Suivant"
      class="btn btn-primary"
      v-bind:disabled="suivantDisable(nbreResto)"
      v-on:click="suivant(nbreResto)"
    >
      <font-awesome-icon icon="forward"/>
    </button>
    <button title="Dernière page" class="btn btn-danger" v-on:click="lastPage()">
      <font-awesome-icon icon="fast-forward"/>
    </button>
  </div>
</template>

<script>
import RestauDetail from "./app-details-restaurant.vue";

export default {
  name: "app-liste-restaurants",

  data() {
    return {
      restaurants: [],
      nom: "",
      cuisine: "",

      ids: "",
      nbreResto: 0,

      page: 1,
      pagesize: 10,
      name: "",

      showModal: false,
      restauSelected: null
    };
  },
  components: {
    "app-details-restaurant": RestauDetail
  },
  mounted() {
    this.getRestaurantsFromServer();
  },
  methods: {
    afficherDetailsRestau: function(restau) {
      this.restauSelected = restau;
      this.showModal = true;
    },
    getRestaurantsFromServer: function() {
      let url =
        "http://localhost:8080/api/restaurants?page=" +
        this.page +
        "&pagesize=" +
        this.pagesize +
        "&name=" +
        this.name;

      fetch(url)
        .then(reponseJSON => {
          reponseJSON.json().then(reponseJSON => {
            this.restaurants = reponseJSON.data;
            this.nbreResto = reponseJSON.count;
          });
        })
        .catch(function(err) {
          console.log(err);
        });
    },

    methodForm: function(event) {
      let id = document.querySelector("#ids").value;
      if (id) this.modifierRestaurant(event);
      else this.ajouterRestaurant(event);
    },

    getRestaurantById: function(id) {
      let url = "http://localhost:8080/api/restaurants/" + id;

      fetch(url)
        .then(responseJSON => {
          responseJSON.json().then(res => {
            // Maintenant res est un vrai objet JavaScript
            //console.log(res);
            this.name = res.restaurant.name;
            this.cuisine = res.restaurant.cuisine;
            this.ids = res.restaurant._id;
          });
        })
        .catch(function(err) {
          console.log(err);
        });
    },

    supprimerRestaurant: function(id) {
      let url = "http://localhost:8080/api/restaurants/" + id;

      fetch(url, {
        method: "DELETE"
      })
        .then(function(responseJSON) {
          responseJSON.json().then(function(res) {
            // Maintenant res est un vrai objet JavaScript
            //afficheReponseDELETE(res);
          });
        })
        .catch(function(err) {
          console.log(err);
        });
    },

    modifierRestaurant: function(event) {
      // Pour éviter que la page ne se ré-affiche
      event.preventDefault();

      let form = event.target;
      let donneesFormulaire = new FormData(form);
      let id = document.querySelector("#ids").value;

      let url = "http://localhost:8080/api/restaurants/" + id;

      fetch(url, {
        method: "PUT",
        body: donneesFormulaire
      })
        .then(function(responseJSON) {
          responseJSON.json().then(function(res) {
            // Maintenant res est un vrai objet JavaScript
            //afficheReponsePUT(res);
            console.log(res);
            this.name = "";
            this.cuisine = "";
            this.ids = "";
            this.getRestaurantsFromServer();
          });
        })
        .catch(function(err) {
          console.log(err);
        });
    },

    ajouterRestaurant: function(event) {
      event.preventDefault();

      let form = event.target;

      let donneesFormulaire = new FormData(form);

      let url = "http://localhost:8080/api/restaurants";

      fetch(url, {
        method: "POST",
        body: donneesFormulaire
      })
        .then(responseJSON => {
          responseJSON.json().then(res => {
            // Maintenant res est un vrai objet JavaScript
            console.log(res);
            this.name = "";
            this.cuisine = "";
            this.getRestaurantsFromServer();
          });
        })
        .catch(function(err) {
          console.log(err);
        });
    },

    firstPage: function() {
      this.page = 1;
      this.getRestaurantsFromServer();
    },

    lastPage: function() {
      this.page =
        this.nbreResto % this.pagesize > 0
          ? parseInt(this.nbreResto / this.pagesize, 10) + 1
          : parseInt(this.nbreResto / this.pagesize, 10);
      this.getRestaurantsFromServer();
    },

    precedent: function() {
      if (this.page > 1) {
        this.page--;
        this.getRestaurantsFromServer();
      }
    },

    precedentDisable: function() {
      if (this.page > 1) return false;
      else return true;
    },

    suivant: function(count) {
      if (count - this.page * this.pagesize > 0) {
        this.page++;
        this.getRestaurantsFromServer();
      }
    },

    suivantDisable: function(count) {
      if (count - this.page * this.pagesize > 0) return false;
      else return true;
    },

    reload: function() {
      this.getRestaurantsFromServer();
      //return _.debounce(this.getRestaurantsFromServer(), 1500);
    },

    getColor(index) {
      return index % 2 == 0 ? "color: yellow" : "color: green";
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
table,
tr,
td,
th {
  border: 1px solid blue;
  padding: 5px;
  border-collapse: collapse;
}

table {
  width: 100%;
}

.borderRouge {
  border: 2px dashed red;
}

body {
  padding-top: 100 px;
}
</style>
