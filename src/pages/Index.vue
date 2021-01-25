<template>
  <q-page class="flex column " :class="bgClass">
    <div class="col q-pt-lg q-px-md">
      <q-input
        v-model="search"
        placeholder="Search"
        dark
        borderless
        @keyup.enter="getWeatherBySearch"
      >
        <template v-slot:before>
          <q-icon name="my_location" @click="getLocation" />
        </template>

        <template v-slot:append>
          <q-btn
            round
            dense
            flat
            icon="search"
            @click="getWeatherBySearch"
            :disable="!search"
          />
        </template>
      </q-input>
    </div>
    <template v-if="weatherData">
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">
          <span class="text-h6"> {{ weatherData.location.name }}</span
          >,
          <br />
          {{ weatherData.location.region }} <br />
          <span class="text-h4"> {{ weatherData.location.country }}</span>
        </div>
        <div class="text-h6 text-weight-light">
          {{ weatherData.current.weather_descriptions[0] }}
        </div>
        <div class="text-h1 text-weight-thin q-my-lg relative-position">
          <span>{{ weatherData.current.temperature }}</span>

          <span class="text-h4 relative-position degree">&deg;C </span>
          <!-- <span class="text-h4">Celcius</span> -->
        </div>
      </div>
      <div class="col text-center">
        <img :src="weatherData.current.weather_icons[0]" alt="" />
      </div>
    </template>
    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin">
          Weather <br />
          Data
        </div>
        <q-btn class="col" flat @click="getLocation">
          <q-icon name="my_location" left size="3em"></q-icon>
          <div>Find Location</div>
        </q-btn>
      </div>
    </template>
    <div class="col skyline"></div>
  </q-page>
</template>

<script>
export default {
  name: "PageIndex",
  data() {
    return {
      search: "",
      weatherData: null,
      lat: null,
      lon: null,
      // apiUrl: "http://api.weatherstack.com/current?access_key=",
      apiUrl: "http://api.weatherstack.com/current?access_key=",
      // apiKey: "225fd91ce76d4074e4a00a3d7a66bd5a",
      apiKey: "225fd91ce76d4074e4a00a3d7a66bd5a"
    };
  },
  methods: {
    getLocation() {
      this.$q.loading.show(),
        // console.log("Hello world");
        navigator.geolocation.getCurrentPosition(position => {
          // console.log(position);
          this.lat = position.coords.latitude;
          this.lon = position.coords.longitude;

          this.getWeatherByCoords();
          // this.$q.loading.hide();
        });
    },
    getWeatherByCoords() {
      this.$q.loading.show(),
        // console.log("getting weather");
        this.$axios(
          `${this.apiUrl}${this.apiKey}&query=${this.lat},${this.lon}`
        ).then(response => {
          this.weatherData = response.data;
          this.$q.loading.hide();
        });
    },
    getWeatherBySearch() {
      this.$q.loading.show(),
        // console.log("getting weather");
        this.$axios(`${this.apiUrl}${this.apiKey}&query=${this.search}`).then(
          response => {
            this.weatherData = response.data;
            this.$q.loading.hide();
          }
        );
    }
  },
  computed: {
    bgClass() {
      if (this.weatherData) {
        if (this.weatherData.current.is_day === "yes") {
          return "bg-day";
        } else {
          return "bg-night";
        }
      }
    }
  }
};
</script>

<style lang="scss">
.q-page {
  background: #136a8a; /* fallback for old browsers */
  background: -webkit-linear-gradient(
    to bottom,
    #136a8a,
    #267871
  ); /* Chrome 10-25, Safari 5.1-6 */
  background: linear-gradient(to bottom, #136a8a, #267871);

  &.bg-day {
    background: linear-gradient(to bottom, #00b4db, #0083b0);
  }

  &.bg-night {
    background: linear-gradient(to bottom, #232526, #414345);
  }
}
.degree {
  top: -43px;
}
.skyline {
  flex: 0 0 100px;
  background-image: url("./../../public/city_skyline.png");
  background-size: contain;
  background-position: center bottom;
}
</style>
