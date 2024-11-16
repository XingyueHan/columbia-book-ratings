<template>
  <div class="container">
    <div class="logo-container">
      <img src="/columbia-logo.png" alt="Columbia Logo" class="columbia-logo" />
    </div>
    <h1 class="main-title">Columbia Book Ratings</h1>
    <h2 class="subtitle">Discover and Explore Books Rated by Our Community</h2>

    <div class="filter-container">
      <input
        type="text"
        v-model="searchTitle"
        class="search-box"
        placeholder="Enter book title"
      />
      <button @click="searchByTitle" class="search-button">Search by Title</button>
    </div>

    <div class="filter-container">
      <div class="dropdown-container">
        <label for="rating" class="dropdown-label">Filter by Rating:</label>
        <select id="rating" v-model="selectedRating" @change="searchByRating" class="dropdown">
          <option v-for="rating in ratings" :key="rating" :value="rating">{{ rating }}</option>
        </select>
      </div>
      <div class="dropdown-container">
        <label for="genre" class="dropdown-label">Filter by Genre:</label>
        <select id="genre" v-model="selectedGenre" @change="searchByGenre" class="dropdown">
          <option value="" disabled>Select Genre</option>
          <option v-for="genre in genres" :key="genre" :value="genre">{{ genre }}</option>
        </select>
      </div>
      <div class="dropdown-container">
        <label for="sort" class="dropdown-label">Sort by:</label>
        <select id="sort" v-model="selectedSort" @change="sortReviews" class="dropdown">
          <option value="title">Title (A-Z)</option>
          <option value="rating-asc">Rating (Low to High)</option>
          <option value="rating-desc">Rating (High to Low)</option>
        </select>
      </div>
    </div>

    <h3>Search Results:</h3>
    <div class="results-grid">
      <div v-for="review in reviews" :key="review.title_without_series" class="card">
        <h4 class="card-title">{{ review.title_without_series }}</h4>
        <p><strong>Rating:</strong> {{ review.average_rating }}</p>
        <p><strong>Genre:</strong> {{ review.genre || "N/A" }}</p>
      </div>
    </div>
    <p v-if="error" class="error-message">{{ error }}</p>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "BookReviews",
  data() {
    return {
      searchTitle: "",
      selectedRating: 1,
      selectedGenre: "",
      selectedSort: "title", // Default sorting
      ratings: [1, 2, 3, 4, 5],
      genres: ["Fantasy", "Mystery", "Thriller", "Romance", "Science Fiction", "Non-Fiction"],
      reviews: [],
      error: "",
    };
  },
  methods: {
    async searchByTitle() {
      this.clearResults();
      this.error = "";
      try {
        const response = await axios.get(
          `http://127.0.0.1:5000/api/reviews/by-title/${this.searchTitle}`
        );
        this.reviews = response.data ? [response.data] : [];
        if (!this.reviews.length) this.error = "No results found for this title.";
        this.sortReviews(); // Apply sorting
      } catch (error) {
        console.error("Error fetching review by title:", error);
        this.error = "An error occurred while fetching the title.";
      }
    },
    async searchByRating() {
      this.clearResults();
      this.error = "";
      try {
        const response = await axios.get(
          `http://127.0.0.1:5000/api/reviews/rating/${this.selectedRating}`
        );
        this.reviews = response.data || [];
        if (!this.reviews.length) this.error = "No results found for this rating.";
        this.sortReviews(); // Apply sorting
      } catch (error) {
        console.error("Error fetching reviews by rating:", error);
        this.error = "An error occurred while fetching the rating.";
      }
    },
    async searchByGenre() {
      this.clearResults();
      this.error = "";
      try {
        const response = await axios.get(
          `http://127.0.0.1:5000/api/reviews/by-genre/${this.selectedGenre}`
        );
        this.reviews = response.data || [];
        if (!this.reviews.length) this.error = "No results found for this genre.";
        this.sortReviews(); // Apply sorting
      } catch (error) {
        console.error("Error fetching reviews by genre:", error);
        this.error = "An error occurred while fetching the genre.";
      }
    },
    sortReviews() {
      // Sorting logic
      if (this.selectedSort === "title") {
        this.reviews.sort((a, b) =>
          a.title_without_series.localeCompare(b.title_without_series)
        );
      } else if (this.selectedSort === "rating-asc") {
        this.reviews.sort((a, b) => a.average_rating - b.average_rating);
      } else if (this.selectedSort === "rating-desc") {
        this.reviews.sort((a, b) => b.average_rating - a.average_rating);
      }
    },
    clearResults() {
      this.reviews = [];
      this.error = "";
    },
  },
};
</script>

<style scoped>
.container {
  max-width: 900px;
  margin: 0 auto;
  text-align: center;
  font-family: Arial, sans-serif;
}

.logo-container {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 20px;
}

.columbia-logo {
  max-width: 150px; /* Adjusted logo size */
  height: auto;
}

.main-title {
  color: #003366;
  font-size: 32px;
  margin-bottom: 10px;
}

.subtitle {
  color: #333;
  font-size: 20px;
  margin-bottom: 20px;
}

.filter-container {
  display: flex;
  justify-content: center;
  gap: 20px;
  margin-bottom: 15px;
}

.dropdown-container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.dropdown-label {
  font-size: 14px;
  margin-bottom: 5px;
  color: #555;
}

.dropdown {
  padding: 6px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
  background-color: #f7f7f7;
}

.search-box {
  padding: 8px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  width: 250px;
  font-size: 16px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.search-button {
  padding: 8px 16px;
  background-color: #003366;
  color: white;
  border: none;
  border-radius: 4px;
  font-size: 16px;
  cursor: pointer;
}

.search-button:hover {
  background-color: #002244;
}

.results-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 20px;
  margin-top: 20px;
}

.card {
  background-color: #f0f8ff;
  border: 1px solid #d4e2f0;
  border-radius: 8px;
  padding: 15px;
  text-align: left;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.card-title {
  color: #003366;
  font-size: 18px;
  margin-bottom: 10px;
}

.error-message {
  color: red;
  font-size: 16px;
  margin-top: 10px;
}
</style>
