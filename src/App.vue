<template>
<div id="container">
      <img src="./assets/logowgc2.png" id="site-logo">
      <div class="slogan">
          Nos experts vous dénichent les meilleures offres !
      </div>
      <div id="filter-section" v-if="filters_section">
          <ul>
            <li v-for="category in displayableCategories()" :key="category">
                <OptionChoice v-on:send-selected-choices="updateFilters" :category="category" :data="options"/>
            </li>
          </ul>
          <ul>
            <li v-for="value in continuous" :key="value">
              <ContinuousInput v-on:send-selected-choices="updateFilters" :value="value" :data="options"/>
            </li>
          </ul>
         <button @click="searchCars">Go get cars !</button>
        </div>
      <br>
  </div>
        <!-- <div v-if="isLoading" class="loader-container">
            <div class="loader"></div>
            <p>We're searching the best similar offers...</p>
        </div> -->
        <!-- <div v-if="filters_section" class="search-bar-container" >
            <input type="text" placeholder="Rentrez ici l'url de l'offre pour la comparer..." v-model="filters.url" @keyup.enter="performSearch">
            <button @click="performSearch">Trouver des offres similaires</button>
        </div> -->
  <div v-if="isLoading" class="loader-container">
    <ul class="cars-list">
        <li v-for="item in maxCars" :key="item"  class="car-card">
            <div class="loader"></div>
        </li>    
    </ul>
  </div>
  <div v-else>
    <ul class="cars-list">
      <li v-for="car in cars" :key="car._id" class="car-card">
        <CarCard :car="car" :fetch="fetchSimilarCars"/>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios'
import CarCard from './components/CarCard.vue'
import OptionChoice from './components/OptionChoice.vue'
import ContinuousInput from './components/ContinuousInput.vue'

export default {
  name: 'App',
  components: { CarCard, OptionChoice, ContinuousInput },
  data() {
    return {
      cars: [],
      categories: ["brand", "model", "type", "location", "transmission", "fuel"],
      continuous: ["kilometers", "price", "displacement", "power"],
      current_filters: {},
      current_formatted_filters: '',
      filters_section: true,
      isLoading: false,
      maxCars: 10,
      options: {}
    }
  },
  methods: {
      formatNumber(value) {
          return new Intl.NumberFormat('fr-FR').format(value);
      },
      capitalizeWords(str) {
          if (str === null) {
              return ''
          }
          if (str === undefined) {
              return ''
          }
          return str.replace(/\b(\w)/g, s => s.toUpperCase());
      },
      displayModels(category) {
          return ((category != 'model') | (this.current_filters['brand'] != undefined))
      },
      displayableCategories() {
          return this.categories.filter(category => this.displayModels(category))
      },
      fetchCarsBasedOnBrand() {
          this.categories.forEach(element => {
            let baseUrl = 'http://127.0.0.1:5000/filters/' + element + '?' + this.current_formatted_filters
            axios.get(baseUrl)
                .then(response => {
                    this.options[element] = response.data;
                })
                .catch(error => {
                    console.error('Error fetching cars:', error);
                });
          });
      },
      fetchSimilarCars(carId) {
          axios.get(`http://127.0.0.1:5000/top_cars/${carId}`)
              .then(response => {
                  this.cars = response.data;
              })
              .catch(error => {
                  console.error('Error fetching similar cars:', error);
              });
      },
      searchCars() {
          this.isLoading = true;
          this.cars = []
          let baseUrl = 'http://127.0.0.1:5000/top_cars?n=' + this.maxCars;
          if (this.current_formatted_filters) {
              baseUrl += '&' + this.current_formatted_filters;
          }
          axios.get(baseUrl)
          .then(response => {
              this.cars = response.data;
          })
          .catch(error => {
              console.error('Error fetching the cars:', error);
          })
            .finally(() => {
                this.isLoading = false;
            });
      },
      updateFilters(selected_filters){
        const category_ = selected_filters[0]
        const filters = selected_filters[1]
        const params = [];
        this.current_filters[category_] = filters
        for (let category in this.current_filters) {
            for (let filter in this.current_filters[category]) {
                params.push(`${category}=${encodeURIComponent(this.current_filters[category][filter])}`);
            }
        }
        if (params.length) {
            this.current_formatted_filters = params.join('&');
        }
      }
  },
  watch: {
      'current_formatted_filters'() {
          this.fetchCarsBasedOnBrand();
      },
  }, mounted() {
      axios.get('http://127.0.0.1:5000/top_cars?n=10')
          .then(response => {
              this.cars = response.data;
          })
          .catch(error => {
              console.error('Error fetching the cars:', error);
          })
      const filterTypes = ['brand', 'fuel', 'transmission', 'type', 'location'];
      filterTypes.forEach(element => {
          axios.get('http://127.0.0.1:5000/filters/' + element)
          .then(response => {
              this.options[element] = response.data;
          })
          .catch(error => {
              console.error('Error fetching the ' + element + ': ', error);
          })
      })
  },
}
</script>

<style>
.search-bar-container {
    text-align: center; /* Center the search bar */
    margin-bottom: 20px; /* Space below the search bar */
}
.search-bar-container input[type="text"] {
    width: 100%; /* Increase the width of the search bar */
    max-width: 1200px; /* Maximum width */
    padding: 12px 15px; /* Bigger padding for a larger field */
    font-size: 1.2em; /* Larger font size */
    border: 2px solid #ccc;
    border-radius: 5px;
    margin-right: 10px;
}

.search-bar-container button {
    padding: 12px 18px;
    font-size: 1.2em;
    border: none;
    border-radius: 5px;
    background-color: #000;
    color: white;
    cursor: pointer;
}

.search-bar-container button:hover {
    background-color: #bbb;
}

#site-logo {
    top: 10px;
    left: 10px;
    width: 200px; 
    height: auto;
    border-radius: 50%;

}
body {
    font-family: 'Poppins', sans-serif;
}

.car-card {
    position: relative;
    border: 1px solid #ddd;
    border-radius: 8px;
    overflow: hidden;
    margin-bottom: 15px;
    box-sizing: border-box;
    transition: box-shadow 0.3s ease, transform 0.3s ease;
    padding-bottom: 40px;
}

.car-card:hover {
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  transform: translateY(-2px);
}


.cars-list {
    list-style-type: none;
    padding: 0;
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(400px, 1fr));
    grid-gap: 10px;
}

#container {
    justify-content: center;
    align-items: center;
    display: flex;
    align-items: center;
    justify-content: flex-start; 
    gap: 20px;
}


#filter-section {
    display: flex;
    justify-content: center;
    gap: 20px;
}

#filter-section ul {
    list-style-type: none; /* Remove default list styling */
    padding: 0;
    margin: 0;
    display: flex; /* Make each list a flex container */
    flex-direction: column; /* Stack list items vertically */
    gap: 10px; /* Space between each filter */
}


#filter-section select:focus, #filter-section input:focus {
    outline: none;
    border-color: #007bff; /* Highlight color when focused */
    box-shadow: 0 0 0 2px rgba(0, 123, 255, 0.25); /* Soft glow for focus */
}


button {
    padding: 8px 15px;
    background-color: #000000;
    color: white;
    border: none;
    border-radius: 4px;
    font-size: 1em;
    cursor: pointer;
    transition: background-color 0.3s;
    font-family: 'Lato', sans-serif;
}

.slogan {
    text-align: center; /* Center the slogan text */
    font-size: 3em; /* Adjust the font size as needed */
    margin-top: 20px; /* Add some space at the top */
    margin-bottom: 20px; /* Add some space at the bottom */
    color: #333; /* Set the text color */
    font-weight: bold; /* Make the slogan text bold */
    font-family: 'Oswald', sans-serif;
}


.car-card button {
    position: absolute;
    top: 5px; /* 5px from the top of the card */
    right: 5px; /* 5px from the left of the card */
    width: 100px;
    height: 20px;
    padding: 0;
    font-size: 1.2em; 
    text-align: center;
    line-height: 20px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
}

.loader-container {
    text-align: center;
    padding: 20px;
}

.loader {
    border: 4px solid #f3f3f3;
    border-top: 4px solid #3498db;
    border-radius: 50%;
    width: 40px;
    height: 40px;
    animation: spin 2s linear infinite;
}

@keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
}
</style>
