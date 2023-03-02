<template lang="">
  <div>
    <div
      class="flex flex-col items-center text-white py-12 bg-[url('C:\Users\ÄGARE\vue-project\src\assets\img\michael-diane-weidner-h-rP5KSC2W0-unsplash.jpg')] bg-cover"
    >
      <div class="flex flex-col items-center text-white py-12">
        <h1 class="text-4xl mb-2">{{ route.params.place_name }}</h1>
        {{ date }}
        <p>Timezone: {{ weather.timezone }}</p>

        <p class="text-8xl mb-8">{{ Math.round(currentWeather.temp) }}&deg;</p>
        <p>Feels like: {{ currentWeather.feels_like }}&deg;</p>
        <p class="capitalize">
          {{ itemOfCurrentWeather.description }}
        </p>
        <img
          class="w-[150px] h-auto"
          :src="`http://openweathermap.org/img/wn/${itemOfCurrentWeather.icon}@2x.png`"
          alt=""
        />
      </div>
    </div>
    <hr class="border-white border-opacity-10 border w-full" />
    <div class="w-full py-12">
      <div class="mx-8 text-white">
        <h2>Hourly Weather</h2>
        <div class="flex gap-10 overflow-x-scroll py-4 w-full">
          <div
            v-for="items in weather.hourly"
            :key="items.dt"
            class="flex flex-col gap-4 items-center"
          >
            <p class="whitespace-nowrap text-md">
              {{
                new Date(items.currentTime).toLocaleTimeString("en-us", {
                  hour: "numeric",
                })
              }}
            </p>
            <img
              class="w-auto h-[50px] object-cover"
              :src="`http://openweathermap.org/img/wn/${items.weather[0].icon}@2x.png`"
            />
            <p class="text-xl">{{ Math.round(items.temp) }}&deg;</p>
          </div>
        </div>
      </div>
    </div>
    <div class="max-w-screen w-full py-12">
      <div class="mx-8 text-white">
        <h2>7 day Forecast</h2>
        <div
          v-for="day in weather.daily"
          :key="day.dt"
          class="flex items-center"
        >
          <p class="flex-1">
            {{
              new Date(day.dt * 1000).toLocaleDateString("en-us", {
                weekday: "long",
              })
            }}
          </p>
          <img
            class="w-[50px] h-[50px] object-cover"
            :src="`http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`"
            alt=""
          />
          <div class="flex gap-2 flex-1 justify-end">
            <p>H: {{ Math.round(day.temp.max) }}</p>
            <p>L: {{ Math.round(day.temp.min) }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import { useRoute } from "vue-router";
export default {
  data() {
    return {
      route: useRoute(),
      weather: [],
      currentWeather: [],
      itemOfCurrentWeather: [],
      date: new Date().toJSON().slice(0, 10).replace(/-/g, "/"),
    };
  },
  computed: {},
  mounted() {
    // this.getWeatherData();
    this.getData();
  },
  methods: {
    getData() {
      fetch(
        `https://api.openweathermap.org/data/2.5/onecall?lat=${this.route.query.lat}&lon=${this.route.query.lng}&exclude={part}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=metric`
      )
        .then((res) => res.json())
        .then((data) => {
          this.weather = data;
          this.currentWeather = this.weather.current;
          this.itemOfCurrentWeather = this.weather.current.weather[0];
          const localOffset = new Date().getTimezoneOffset() * 60000;
          const utc = this.weather.current.dt * 1000 + localOffset;
          this.weather.currentTime = utc + 1000 * this.weather.timezone_offset;
          // cal hourly weather offset
          this.weather.hourly.forEach((hour) => {
            const utc = hour.dt * 1000 + localOffset;
            hour.currentTime = utc + 1000 * this.weather.timezone_offset;
          });
        });
    },
  },
};
</script>
