# Part 4

[[toc]]

## 4.1 Components

### 4.1.1 Re-structuring to use components

We have a decent layout, but we might want to reuse the movie cards somewhere else.

Let create a component by creating a new Vue file under the `components` directory called `MovieCard.vue`.

Add the following the `MovieCard.vue`:

#### MovieCard.vue
~~~~

<template></template>

<style></style>

<script>
export default {
  props: {
    movie: Object
  },
  data: () => ({   
      editRating: 0
  }),
  created() {
      this.editRating = this.$props.movie.rating
  }
};
</script>

~~~~

Notice that I've added `props` to the new component. The `movie` prop will eventually be passed in by the parent controller.

We'll remove the v-card from the template of `App.vue` and place in the template of our new component.

#### MovieCard.vue
~~~~ vue

<template>
  <v-card class="fill-height">
    <v-img :src="movie.img"/>
    <v-card-title>
      <h2 class="subtitle-1">{{movie.title}}</h2>
    </v-card-title>
    <v-card-text>
      <v-rating small v-model="movie.rating"></v-rating>
      {{`${movie.editRating}/5`}}
    </v-card-text>
  </v-card>
</template>

<style></style>

<script>
export default {
  props: {
    movie: Object
  },
  data: () => ({   
      editRating: 0
  }),
  created() {
      this.editRating = this.$props.movie.rating
  }
};
</script>

~~~~


Back in our App.vue file, we should import `MovieCard.vue` and add it to the components:

~~~~ vue

<script>
import * as axios from "axios";
import MovieCard from './components/MovieCard';

export default {
  name: "App",
  components: {
    MovieCard
  },
  data: () => ({
    movies: []
  }),
  methods: {
    getAllMovies: async function() {
      const url = "https://movie-api-test-page.herokuapp.com/api/movies";
      var res = [];
      await axios
        .get(url)
        .then(function(data) {
          res = data.data;
        })
        .catch(function(err) {
          // eslint-disable-next-line
          console.log(err);
        });
      return res;
    }
  },
  async created() {
    this.movies = await this.getAllMovies();
  },
  computed: {
    favoriteMovies: function() {
      return this.movies.filter(x => {
        return x.rating > 4;
      });
    }
  }
};
</script>

~~~~

We'll also need to add `<MovieCard/>` where our old v-card used to be in the template:

~~~~

<template>
  <v-app class="app">
    <v-content>
      <v-layout justify-center row wrap fill-height>
        <v-flex xs12 md4 pa-4>
          <h2>My Top Rated Movies</h2>
          <ul>
            <li v-for="(m, index) in favoriteMovies" :key="index">{{m.title}}</li>
          </ul>
        </v-flex>
        <v-flex xs12 md8 pa-4 fill-height>
          <v-layout justify-left row wrap class="fill-height">
            <v-flex xs12>
              <h1>My Movies</h1>
            </v-flex>
            <v-flex xs6 sm4 md4 lg3 xl3 pa-2 v-for="(m, index) in movies" :key="index">
              <MovieCard></MovieCard>
            </v-flex>
          </v-layout>
        </v-flex>
      </v-layout>
    </v-content>
  </v-app>
</template>

~~~~
