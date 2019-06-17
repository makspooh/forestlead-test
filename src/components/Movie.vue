<template>
  <v-container>
    <v-toolbar app>
      <v-btn
        @click="this.goBack"
        icon class="hidden-xs-only">
        <v-icon>arrow_back</v-icon>
      </v-btn>
      <v-toolbar-title class="headline text-uppercase">
        <span>the movie database </span>
        <span class="font-weight-light">list of movies</span>
      </v-toolbar-title>
    </v-toolbar>
    <v-layout row>
      <v-flex xs12>
        <v-card
          v-for="(item, index) of movie"
          :key="index"
        >
          <v-layout>
            <v-flex xs5>
              <v-img
                :src="getPoster(item)"
                contain
              >
              </v-img>
            </v-flex>
            <v-flex xs12>
              <v-card-title primary-title>
                <div>
                  <div class="headline">{{ item.title }}</div>
                  <div v-if="item.tagline" class="tagline">&laquo;{{ item.tagline }}&raquo;</div>
                  <v-expansion-panel popout>
                    <v-expansion-panel-content flat expand-icon="none">
                      <template v-slot:header>
                        <div class="headliner">Show overview</div>
                      </template>
                      <v-card>
                        <v-card-text>{{ item.overview }}</v-card-text>
                      </v-card>
                    </v-expansion-panel-content>
                  </v-expansion-panel>
                  <div>Rating: {{ item.vote_average }}</div>
                  <div>Count of votes: {{ item.vote_count }}</div>
                  <div>Release: {{ getDate(item.release_date) }}</div>
                  <div>Budget: ${{ item.budget }}</div>
                  <div>Genres: {{ getGenres(item.genres) }}</div>
                  <div>Production: {{ getCompanies(item.production_companies) }}</div>
                  <div>Countries: {{ getCountries(item.production_countries) }}</div>
                  <div>Runtime: {{ item.runtime }} min.</div>
                  <div v-if="item.homepage">Official page: <a class="link" target="blank" :href="item.homepage">{{ item.title }}</a></div>
                </div>
              </v-card-title>
            </v-flex>
          </v-layout>
        </v-card>
      </v-flex>
    </v-layout>
    <template>
      <v-layout justify-center>
        <v-flex xs12>
          <v-toolbar class="mt-5" dark>
              <v-toolbar-title>Related movies</v-toolbar-title>
          </v-toolbar>
          <v-card>
            <v-container
            fluid
            grid-list-md
            >
              <v-layout row wrap>
                <v-flex
                v-for="(movie, index) of relatedMovies[0]"
                :key="index"
                >
                  <v-card>
                    <v-img
                    :src="getRelatedPoster(movie)"
                    width="210px"
                    height="280px"
                    >
                  </v-img>
                  <v-card-actions>
                    <router-link
                      :to="'/movie/' + movie.id"
                    >
                      <v-btn outline color="warning">show more</v-btn>
                    </router-link>
                  </v-card-actions>
                  </v-card>
                </v-flex>
              </v-layout>
            </v-container>
          </v-card>
        </v-flex>
      </v-layout>
    </template>
  </v-container>
</template>

<script>
export default {
    data() {
      return {
        movie: [],
        currentMovieId: this.$router.currentRoute.params['id'],
        relatedMovies: []
      }
    },
    methods: {
      goBack() {
          this.$router.go(-1);
      },
      getMovie() {
          fetch(`https://api.themoviedb.org/3/movie/${ this.currentMovieId }?api_key=8e7e66412d4fe189dbe2a28f0cd9a016&language=en-US`)
              .then(response => response.json())
              .then(data => this.movie.push(data))
      },
      getPoster(item) {
          return `http://image.tmdb.org/t/p/w780/${ item.poster_path }`
      },
      getDate(date) {
        let newDate = new Date(date);
        let day = newDate.getDate();
        let month = newDate.toLocaleString('en-us', { month: 'long' });
        let year = newDate.getFullYear();

        return day + ' ' + month + ' ' + year
      },
      getGenres(genresList) {
        let genres;
        
        genresList.forEach(genre => {
          if (genres === undefined) {
            genres = genre.name;
          } else genres += ', ' + genre.name;
        })
        return genres;
      },
      getCompanies(companiesList) {
        let companies;
        
        companiesList.forEach(company => {
          if (companies === undefined) {
            companies = company.name;
          } else companies += ', ' + company.name;
        })
        return companies;
      },
      getCountries(countriesList) {
        let countries;
        
        countriesList.forEach(country => {
          if (countries === undefined) {
            countries = country.iso_3166_1;
          } else countries += ', ' + country.iso_3166_1;
        })
        return countries;
      },
      getRelatedMovies() {
        fetch(`https://api.themoviedb.org/3/movie/${ this.currentMovieId }/recommendations?api_key=8e7e66412d4fe189dbe2a28f0cd9a016&language=en-US&page=1`)
            .then(response => response.json())
            .then(data => this.relatedMovies.push(data.results))
      },
      getRelatedPoster(item) {
          return `http://image.tmdb.org/t/p/w342/${ item.poster_path }`
      },
    },
    created() {
        this.getMovie();
        this.getRelatedMovies();
    }
}
</script>

<style lang="scss">
  a {
    text-decoration: none;
    color: #fb8c00;
  }

  .headliner {
    color: #fb8c00;
    text-align: left;
  }

  .tagline {
    font-style: italic;
  }
</style>
