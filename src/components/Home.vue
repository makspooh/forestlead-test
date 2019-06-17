<template>
  <div>
    <v-toolbar app>
      <v-toolbar-title class="headline text-uppercase">
        <span>the movie database </span>
        <span class="font-weight-light">list of movies</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-text-field
        v-model="search"
        :search-input.sync="search"
        append-icon="search"
        label="Search for a movies..."
      ></v-text-field>
    </v-toolbar>
    <v-toolbar
      v-if="!this.findedMovies"
      dark
    >
        <v-toolbar-title>Most popular movies</v-toolbar-title>
    </v-toolbar>
    <v-data-table
      v-if="!this.findedMovies"
      :headers="headers"
      :items="movies"
      class="elevation-1"
    >
      <template v-slot:items="props">
        <td>
          <v-img
            :src="getRelatedPoster(props.item)"
            width="50px"
            >
          </v-img>
        </td>
        <td>{{ props.item.title }}</td>
        <td class="text-xs-left">{{ new Date(props.item.release_date).getFullYear() }}</td>
        <td class="text-xs-left">{{ props.item.vote_average }}</td>
        <td class="text-xs-left">
          <router-link
            :to="'/movie/' + props.item.id"
            style="text-decoration: none"
          >
              <v-btn color="warning">show more</v-btn>
            </router-link>
        </td>
      </template>
    </v-data-table>
    <v-toolbar v-if="this.findedMovies" dark>
        <v-toolbar-title>Finded movies</v-toolbar-title>
    </v-toolbar>
    <v-data-table
      v-if="this.findedMovies"
      :headers="headers"
      :items="findedMovies"
      class="elevation-1"
    >
      <template v-slot:items="props">
        <td>
          <v-img
            :src="getRelatedPoster(props.item)"
            width="50px"
            >
          </v-img>
        </td>
        <td>{{ props.item.title }}</td>
        <td class="text-xs-left">{{ new Date(props.item.release_date).getFullYear() }}</td>
        <td class="text-xs-left">{{ props.item.vote_average }}</td>
        <td class="text-xs-left">
          <router-link
            :to="'/movie/' + props.item.id"
            style="text-decoration: none"
          >
              <v-btn color="warning">show more</v-btn>
            </router-link>
        </td>
      </template>
    </v-data-table>
  </div>
</template>

<script>
  export default {
    data()  {
      return {
        search: null,
        movies: [],
        countries: [],
        findedMovies: null,
        headers: [
          { text: 'Poster', value: 'poster', sortable: false },
          { text: 'Title', value: 'title' },
          { text: 'Release year', value: 'year',  sortable: false},
          { text: 'Rating', value: 'rating',  sortable: false},
          { text: 'More details', value: 'details', sortable: false}
        ],
      }
    },
    methods: {
      getRelatedPoster(item) {
          return `http://image.tmdb.org/t/p/w342/${ item.poster_path }`
      },
      getMovies() {
        fetch('https://api.themoviedb.org/3/movie/popular?api_key=8e7e66412d4fe189dbe2a28f0cd9a016&language=en-US&')
        .then(response => response.json())
        .then(data => {
          this.movies = data.results
        })
      },
      getCountries(countriesList) {
        let countries;
        
        countriesList.forEach(country => {
          if (countries === undefined) {
            countries = country.iso_3166_1;
          } else countries += ', ' + country.iso_3166_1;
        })
        return countries;
      }
    },
    created() {
      this.getMovies();
    },
    watch: {
      search() {
        fetch(`https://api.themoviedb.org/3/search/movie?api_key=8e7e66412d4fe189dbe2a28f0cd9a016&language=en-US&query=${ this.search }&page=1&include_adult=false`)
          .then(response => response.json())
          .then(data => this.findedMovies = data.results)
      }
    }
  }
</script>