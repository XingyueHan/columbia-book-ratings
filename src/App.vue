<template>
  <div id="app">
    <BookReviews />
  </div>
</template>

<script>
import BookReviews from './components/BookReviews.vue';

export default {
  name: 'App',
  components: {
    BookReviews
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
}
</style>
