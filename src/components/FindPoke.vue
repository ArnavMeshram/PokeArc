<template lang="html">
  <div id="FindPoke">
    <md-progress-bar id="search-progress-bar" md-mode="indeterminate"></md-progress-bar>
    <md-toolbar class="md-primary">
      <div class="md-toolbar-row">
        <md-field class="search">
         <label>Enter Pokemon name or id</label>
         <md-input  v-on:keyup.enter="searchPokemon()" v-model="query"></md-input>
        </md-field>
        <md-button id="search-btn" class="md-icon-button md-fab" v-on:click="searchPokemon()"><md-icon>search</md-icon><md-tooltip md-direction="bottom">Search</md-tooltip></md-button>
      </div>
    </md-toolbar>
    <md-content class="md-scrollbar">
      <div class="not-found" v-if="!pokemon">
        <md-empty-state>
          <h2 class="md-display-1">You haven't searched yet or no Pokemon matched your query. Try searching for "6" or "charizard".</h2>
        </md-empty-state>
      </div>
      <md-card v-if="pokemon" class="md-elevation-24">
        <md-card-media>
          <img style="height:180px;width:180px;" v-bind:src="pokemon.sprites.front_default" alt="People">
        </md-card-media>

        <md-card-header>
          <div class="md-title">{{ pokemon.name }}</div>
          <div class="md-subhead">Moves : {{ pokemon.moves.length }}</div>
        </md-card-header>

        <md-card-expand>
          <md-card-actions md-alignment="space-between">
            <div>
                <md-button class="md-icon-button">
                    <md-icon>favorite<md-tooltip md-direction="bottom">Add to favorite</md-tooltip></md-icon>
                </md-button>
            </div>

            <md-card-expand-trigger>
              <md-button class="md-icon-button">
                <md-icon>keyboard_arrow_down<md-tooltip md-direction="bottom">Stats</md-tooltip></md-icon>
              </md-button>
            </md-card-expand-trigger>
          </md-card-actions>

          <md-card-expand-content>
            <md-card-content>
              <span class="md-body-2">
                Weight: {{ pokemon.weight }}
                <br/>
                Base experience: {{ pokemon.base_experience }}
                <br/>
                <ul  v-for="stat in pokemon.stats">
                  <li class="capitalize">
                    {{ stat.stat.name }} : {{ stat.base_stat }}
                  </li>
                </ul>
              </span>
            </md-card-content>
          </md-card-expand-content>
        </md-card-expand>
      </md-card>
    </md-content>
  </div>
</template>

<script>
export default {
  name: 'FindPoke',
  data () {
    return {
      pokemon: null,
      query: ''
    }
  },
  methods: {
    getPokemon: function (url) {
      this.$http.get(url)
      .then(function (data) {
        this.pokemon = data.body
      }).then(function () {
        if (this.pokemon !== null) {
          this.hideProgressBar()
          this.clickStop()
        }
      })
    },
    searchPokemon: function () {
      document.getElementById('search-btn').classList.add('md-dense')
      this.pokemon = null
      this.showProgressBar()
      if (this.query === '') {
        this.query = '1'
      }
      var url = 'https://pokeapi.co/api/v2/pokemon/' + this.query
      this.getPokemon(url)
    },
    hideProgressBar: function () {
      document.getElementById('search-progress-bar').style.visibility = 'hidden'
    },
    showProgressBar: function () {
      document.getElementById('search-progress-bar').style.visibility = 'visible'
    },
    clickStop: function () {
      document.getElementById('search-btn').classList.remove('md-dense')
    }
  },
  mounted () {
    this.hideProgressBar()
  }
}
</script>

<style scoped>
  .search {
    max-width: 500px;
  }
  .card-expansion {
      height: 480px;
  }
  .md-card {
      width: 300px;
      margin: 4px;
      display: inline-block;
      vertical-align: top;
  }
  .md-title, .capitalize {
    text-transform: capitalize;
  }
  .md-content {
      max-width: 100%;
      height: 690px;
      max-height: 690px;
      overflow: auto;
  }
  .not-found {
    position: absolute;
    width: 100%;
    margin: 50px auto;
  }
  ul {
    list-style-type: none;
    padding: 0;
  }
  li {
    display: inline-block;
    margin: 0 10px;
  }
  .md-body-2, li {
    text-shadow: 2px 2px 5px black;
  }
</style>