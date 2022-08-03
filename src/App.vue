<script setup lang="ts">
import { ref } from 'vue'
import axios from 'axios'

const API_URL = "https://www.omdbapi.com/";
const API_KEY = "70c5b7d2";

const movieTitle = ref('')
let movies: Array<{
  Poster: string;
  Title: string;
  Type: string;
  Year: string;
  imdbID: string
}>= ref('')

let totalResults = ref('')
let page = ref(1)

const getMovieByTitle = () => {
  axios.get(`${API_URL}?apikey=${API_KEY}&s=${movieTitle.value}&page=${page.value}`).then((response: Object) => {
    if (response.data.Response == "False") {
      throw new Error(response.data.Error)
    }

    movies.value = response.data.Search
    totalResults.value = response.data.totalResults
  })
}

const loadMore = () => {
  page.value+=1
  getMovieByTitle()
}

const reset = () => {
  page.value = 1
  getMovieByTitle()
}

</script>

<template>
  <main>
    <form @sumbit.prevent="getMovieByTitle" class="movie-search-form" title="Type movie title and click Enter">
      <label class="movie-search-form__label" for="movie-title">Search movies</label>
      <input class="movie-search-form__input" type="text" v-model="movieTitle" @keydown.enter.prevent="getMovieByTitle" id="movie-title" name="movie-title">
      <div class="movie-search-form__total-results" v-if="totalResults">Total results: {{totalResults}}</div>
      <div v-if="totalResults">Page: {{page}}</div>
    </form>
    <div class="actions">
      <button v-if="(totalResults > 10) && (10 * page < totalResults)" @click="loadMore">Next page</button>
      <button v-if="page !== 1" @click="reset">Reset</button>
    </div>
    
    <div class="movie-list">
      <div v-for="movie in movies" :key="movie.imdbID" class="movie-list__item">
        <div class="image-container">
          <div class="thumbnail-inner">
              <div class="thumbnail" :style="`background-image: url('${movie.Poster}')`"></div>
          </div>
          <div class="content">
              <div class="inner">
                  <p>{{movie.Year}}</p>
                  <h2>{{movie.Title}}</h2>
              </div>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<style lang="scss" scoped>
.movie-search-form {
  display: flex;
  flex-direction: column;
  align-items: center;
  &__label {
    font-size: 1rem;
    font-weight: 700;
    letter-spacing: 0.1rem;
    padding-bottom: 0.5rem;
    text-transform: uppercase;
    color: #fae919;
  }
  &__input {
    font-size: 2rem;
    margin-bottom: 0.5rem;
  }
  &__total-results {
    font-size: 0.8rem;
  }
}
.actions {
  display: flex;
  justify-content: center;
  padding: 0.5rem;
}
.movie-list {
  display: grid;
  grid-template-columns: 1fr;
  grid-gap: 1rem;
  margin-top: 4rem;
}
.image-container {
  position: relative;
  transition: all .3s cubic-bezier(.645,.045,.355,1);
  min-height: 500px;
  .thumbnail-inner {
      transition: transform .28s ease;
      z-index: 9;
      position: absolute;
      top: 0;
      left: 0;
      height: 100%;
      width: 100%;
      background-size: cover;
      background-position: 50%;
      overflow: hidden;
      border-radius: 5px;
      cursor: pointer;
      &:after {
          content: " ";
          display: block;
          background-image: linear-gradient(transparent 30%,#080808);
          position: absolute;
          top: 0;
          left: 0;
          height: 100%;
          width: 100%;
          z-index: 9;
      }
      &:before {
          background-color: #fae919;
          background-image: linear-gradient(#fae919 10%,#000);
          position: absolute;
          content: "";
          top: 0;
          left: 0;
          width: 100%;
          height: 100%;
          z-index: 5;
          opacity: 0;
      }
  }
  .thumbnail {
      position: absolute;
      top: 0;
      left: 0;
      height: 100%;
      width: 100%;
      background-size: cover;
      background-position: 50%;
      border-radius: 5px;
      background-color: #0a0a0a;
      transform: scale(1.13) translateZ(0);
      backface-visibility: hidden;
      transition: transform .28s ease;
      z-index: 4;
  }
  &:hover {
    z-index: 999;
      .thumbnail-inner {
          transform: scale(1.08) translateZ(0);
          &:before {
              opacity: .65;
          }
          &:after {
              background-image: none;
          }
      }
      .thumbnail {
          transform: scale(1) translateZ(0);
      }
  }
  .content {
      position: absolute;
      bottom: 40px;
      left: 40px;
      right: 40px;
      max-width: 100%!important;
      z-index: 10;
      padding: 0;
      .inner {
          p {
              color: #eee;
              font-size: 14px;
          }
          h2 {
              font-size: 24px;
              line-height: 36px;
              color: #fff;
          }
          .button {
              margin-top: 35px;
              transition: .7s;
              .btn {
                  padding: 0 23px;
                  height: 40px;
                  display: inline-block;
                  line-height: 34px;
                  border: 2px solid #f9004d;
                  border-radius: 4px;
                  font-size: 14px;
                  position: relative;
                  z-index: 2;
                  letter-spacing: .2px;
                  text-transform: uppercase;
                  color: #fff;
                  border-color: hsla(0,0%,100%,.3);
              }
          }
      }
  }
  img {
    width: 100%;
  }
}

@media (min-width: 420px) {
  .movie-list {
    grid-template-columns: repeat(2, 1fr);
  }
}
@media (min-width: 992px) {
  .movie-list {
    grid-template-columns: repeat(3, 1fr);
  }
}
@media (min-width: 1100px) {
  .movie-list {
    grid-template-columns: repeat(4, 1fr);
  }
}
</style>
