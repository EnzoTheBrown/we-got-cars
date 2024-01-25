<template>
    <div>
        <a :href="car.href" class="car-link" target="_blank">
            <div class="car-header">
                <h2>{{ capitalizeWords(car.brand) }} - {{ capitalizeWords(car.model) }}</h2>
                <span class="car-model">{{ car.motorization }}</span>
            </div>
            <div class="car-body">
                <img :src="car.image" alt="Car Image" class="car-image">                    
                <div class="car-info">
                    <div class="car-numeric">
                        <span>{{ formatNumber(car.kilometers) }} <small>km</small></span>
                        <span>-</span>
                        <span>{{ car.year }}</span>
                    </div>
                    <div class="car-gear">
                        <span>{{ capitalizeWords(car.fuel) }}</span>
                        <span> </span>
                        <span>{{ capitalizeWords(car.transmission) }}</span>
                    </div>
                    <div v-if="car.power & car.displacement" class="car-gear">
                        <span>{{ car.power }} <small>Ch</small></span>
                        <span>-</span>
                        <span>{{ car.displacement }} <small>L</small></span>
                    </div>
                    <div v-if="car.location != ''" class="car-location">
                        <img class="france-logo" :src="formatcardFR()">
                        <span class="car-location-info">{{ car.named_location }}</span>
                    </div>
                    <div v-else class="car-location">
                        <img class="france-logo" src="../assets/delivery.png">
                        <span class="car-location-info">livraison</span>
                    </div>
                </div>
            </div>
        </a>
        <br>
        <img :src="formatImage(car)" class="source-logo">
        <span class="price">{{ formatNumber(car.price) }}<small> €</small></span>
        
        <div class="car-predict">
            <span>Estimation de l'expert: {{ formatNumber(Math.round(((car.price_predict + car.price) / 2 ))) }} € 
                <br>
            <a style='color: tomato'>{{ formatNumber(Math.round(((car.price_predict + car.price) / 2 - car.price)/100) * 100 )}} € en dessous du prix du marché </a>
        </span>
        <button class="similar-button"  @click="fetch(car._id)">ofrres similaires</button>
        </div>
    </div>
</template>

<script>
export default {
    props: ['car', 'fetch'],
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
      formatImage(car){
          if (car.source === undefined) {
              return ''
          }
          return require(`@/assets/${car.source}.png`);
      },
      formatcardFR(){
          return require(`@/assets/francewebp.webp`);
      },
    }
}
</script>

<style>


.car-location {
    position: absolute; /* Or 'fixed' if you want it to stay in place on scroll */
    top: 0;
    right: 0;
    display: flex;
    align-items: center; /* Aligns items vertically in the center */
    gap: 10px; /* Space between logo and text */
    padding: 10px; /* Padding around the container */
}

.france-logo {
    width: 40px; /* Adjust size as needed */
    height: 40px; /* Adjust size as needed */
    height: auto; /* Maintain aspect ratio */
    
}

.car-location-info {
    font-size: 0.7em;
    color: #333;
    font-family: 'Poppins', sans-serif;
}

.car-link {
    text-decoration: none;
    color: inherit;
}

.car-model {
    display: block;
    color: #666;
    font-size: 0.7em;
    white-space: nowrap; 
    overflow: hidden;
    text-overflow: ellipsis; 
    font-family: 'Poppins', sans-serif;
}

.car-body {
    padding: 10px;
    text-align: center;
}

.car-info span, .car-motorization span {
    display: block;
    margin-bottom: 5px;
    color: #333;
}
.car-header {
    background-color: #f7f7f7;
    padding: 10px;
    padding-top: 30px;
    text-align: center;
    font-family: 'Poppins', sans-serif;
}

.car-numeric {
    font-size: 0.7em;
}

.car-gear {
    font-size: 0.65em;
}

.car-image {
  width: 150px;
  height: 100px;
  border-radius: 8px;
  top: 10px;
  right: 10px;
  object-fit: cover;
  border: 2px solid #bbb;
}

.car-info span, .car-motorization span {
    display: inline-block;
    margin-right: 10px;
    color: #333;
}

.car-info small, .car-motorization small {
    font-size: smaller;
    color: #666;
}


.car-predict {
    position: absolute;
    bottom: 0;
    width: 100%;
    background-color: #f0f0f0;
    padding: 4px;
    box-sizing: border-box;
    border-top: 1px solid #ddd;
    font-size: 0.7em;
    font-weight: bold;
    padding-top: 6px;
    text-align: center;
}

.price-source {
    text-align: center;
}

.source-logo {
    max-width: 60px; /* Maximum width */
    max-height: 60px; /* Maximum height */
    position: absolute;
    bottom: 40px; 
    left: 10px; 
    font-size: 1em; 
    color: #333;
}


.price {
    position: absolute;
    bottom: 40px; 
    right: 10px; 
    font-size: 1em; 
    color: #333;
}


.similar-button {
    color: white; /* Text color */
    border: none;
    border-radius: 4px;
    padding: 10px 20px;
    cursor: pointer;
    transition: background-color 0.3s; /* Smooth transition for background color */
}

.similar-button:hover {
    background-color: #0056b3; /* Background color on hover */
}
</style>
