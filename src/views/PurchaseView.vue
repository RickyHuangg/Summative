<script setup>
import axios from "axios";
import { ref } from "vue";
import { useRouter } from "vue-router";
import Modal from "../components/Modal.vue";

const router = useRouter();
const genre = ref(28);
const search = ref("");
const movies = ref(null);
const page = ref(1);
const currentURL = ref("");
const totalPages = ref(0);
const showModal = ref(false);
const selectedRecordId = ref(0);

const toggleModal = (id) => {
  showModal.value = !showModal.value;
  selectedRecordId.value = id;
};

const getTMDBData = async (url, options, page) => {
  movies.value = (
    await axios.get(url, {
      params: {
        api_key: "f986b47ac3895e05d9614e10bd9e88c0",
        region: "US",
        language: "en",
        include_adult: false,
        page,
        ...options,
      },
    })
  ).data;
  totalPages.value = movies.value.total_pages;
  currentURL.value = url;
};
</script>

<template>
  <div class="page">
    <div class="title">Select Movies</div>
    <div>
      <div class="controls">
        <div class="search">
          <input
            type="search"
            placeholder="Enter search items"
            v-model="search"
          />
          <button
            @click="
              getTMDBData('https://api.themoviedb.org/3/search/movie', {
                query: search,
              })
            "
          >
            Search
          </button>
        </div>
        <div>
          <div class="dropdown">
            <select v-model="genre">
              <option value="28">Action</option>
              <option value="10751">Family</option>
              <option value="878">Science Fiction</option>
              <option value="12">Adventure</option>
              <option value="14">Fantasy</option>
              <option value="10770">TV Movie</option>
              <option value="16">Animation</option>
              <option value="36">History</option>
              <option value="53">Thriller</option>
              <option value="35">Comedy</option>
              <option value="27">Horror</option>
              <option value="10752">War</option>
              <option value="80">Crime</option>
              <option value="10402">Music</option>
              <option value="37">Western</option>
              <option value="99">Documentary</option>
              <option value="9648">Mystery</option>
              <option value="18">Drama</option>
              <option value="10749">Romance</option>
            </select>
            <button
              @click="
                getTMDBData('https://api.themoviedb.org/3/discover/movie', {
                  with_genres: genre,
                })
              "
            >
              Get
            </button>
          </div>
          <div class="cart">
            <button @click="router.push('/cart')">Cart</button>
          </div>
        </div>
      </div>
      <div class="pagination">
        <button
          @click="
            getTMDBData(
              currentURL,
              {
                query: search,
                with_genres: genre,
              },
              page === 1 ? 1 : page--
            )
          "
        >
          Prev
        </button>
        <p>{{ `Page ${page} of ${totalPages}` }}</p>
        <button
          @click="
            getTMDBData(
              currentURL,
              {
                query: search,
                with_genres: genre,
              },
              page >= totalPages ? totalPages : page++
            )
          "
        >
          Next
        </button>
      </div>
      <div v-if="movies" class="tiles">
        <div v-for="movie in movies.results" :key="movie.id" class="tile">
          <img
            class="movies"
            :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`"
            @click="toggleModal(movie.id)"
          />
        </div>
      </div>
    </div>
  </div>
  <Modal v-if="showModal" :id="selectedRecordId" @toggleModal="toggleModal()" />
</template>

<style scoped>
.cart button {
  width: 80px;
  height: 50px;
  border-radius: 20px;
}
.cart {
  position: relative;
  left: 570px;
  bottom: 35px;
}
.page {
  height: 100%;
  background: rgb(2, 0, 36);
  background: linear-gradient(
    rgba(2, 0, 36, 1) 0%,
    rgba(121, 9, 75, 1) 35%,
    rgba(80, 78, 136, 1) 59%,
    rgba(0, 212, 255, 1) 100%
  );
}
.title {
  color: white;
  position: relative;
  text-align: center;
  font-size: 30px;
  font-weight: 800;
}

.search {
  top: 10px;
  position: relative;
  right: 740px;
}

.tiles {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  text-align: center;
}

img {
  width: 200px;
}

.pagination {
  left: 730px;
  position: relative;

  color: white;
  display: flex;
  gap: 1rem;
}
.pagination button {
  width: 70px;
  height: 45px;
}

.controls {
  display: flex;
  flex-direction: row-reverse;
  justify-content: space-between;
}

.movies {
  border: solid white;
  margin-block: 1rem;
}

.dropdown {
  position: relative;
  left: 1050px;
}

.dropdown button {
}
</style>
