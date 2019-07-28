# Part 5

[[toc]]

![Chart of Emitters/Props](./images/events.png)

## 5.1 Using props to pass data from a parent component to a child component

Wait a minute... we have a problem. Your app should be broken at this point, meaning, the MovieCards should not appear in the UI at all. This is because we have not yet passed our movies into our MovieCards.

How to we pass movie to our new component? We can bind `m` into our child component by adding `:movie="m"` to `<MovieCard></MovieCard>`.

If the movie object is being passed correctly, you should see the movie cards in the UI.

Next, we'll emit events to pass data from the child to the parent component.


## 5.2 Using event emitters to pass data to the parent component

So, we have our `MovieCard` component within out `App.vue` file.

Let's say, we want to watch for a change to the rating within the `MovieCard`, and let the the parent know when a change occurs.

Event emitters are the answer! We can watch the value of `editRating` and `$emit` an event along with the `editRating` value in our MovieApp component.

#### Emitting in MovieCard
~~~~ vue

<script>
export default {
  props: {
    movie: Object
  },
  data: () => ({
    editRating: 0
  }),
  created() {
    this.editRating = this.$props.movie.rating;
  },
  watch: {
    editRating: function() {
      this.$emit("change-rating", this.editRating);
    }
  }
};
</script>

~~~~

#### Listening for an event in App.vue
~~~~ vue

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
              <MovieCard @change-rating="m.rating=$event" :movie="m"></MovieCard>
            </v-flex>
          </v-layout>
        </v-flex>
      </v-layout>
    </v-content>
  </v-app>
</template>

~~~~