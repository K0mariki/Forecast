<script setup>
import { ref, onMounted, computed } from "vue";
import { API_KEY, BASE_URL } from "./constans";
import WeatherSummary from "./components/WeatherSummary.vue";
import Highlights from "./components/Highlights.vue";
import Humidity from "./components/Humidity.vue";
import Coords from "./components/Coords.vue";

const city = ref("Ростов-на-Дону");
const weatherInfo = ref(null);
const isError = computed(() => weatherInfo.value?.cod !== 200);

function getWeather() {
  fetch(`${BASE_URL}?q=${city.value}&units=metric&appid=${API_KEY}`)
    .then((response) => response.json())
    .then((data) => (weatherInfo.value = data));
}

onMounted(getWeather);
</script>

<template>
  <div class="page">
    <main class="main">
      <div class="container">
        <div class="laptop">
          <div class="sections">
            <section
              :class="['section', 'section-left', { 'section-error': isError }]"
            >
              <div class="info">
                <div class="city-inner">
                  <input
                    @keyup.enter="getWeather"
                    v-model="city"
                    type="text"
                    class="search"
                  />
                </div>
                <WeatherSummary v-if="!isError" :weatherInfo="weatherInfo" />
                <div v-else class="error">
                  <div class="error-title">Упс! Неверное название</div>
                  <div class="error-massage">Город не найден</div>
                </div>
              </div>
            </section>

            <section v-if="!isError" class="section section-right">
              <Highlights :weatherInfo="weatherInfo" />
            </section>
          </div>
            <div v-if="!isError" class="section-two">
              <Coords :coord="weatherInfo.coord" />
              <Humidity :humidity="weatherInfo.main.humidity" />
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<style lang="sass" scoped>
@import './assets/styles/main'
.page
  position: relative
  display: flex
  justify-content: center
  align-items: center
  min-height: 100vh
  padding: 20px 0
  background-color: #59585d

.laptop
  width: 100%
  padding: 20px
  background-color: #0e100f
  border-radius: 25px

.section-two
  display: flex
  gap: 10px
  justify-content: space-between

.sections
  display: flex
  justify-content: space-between
  width: 100%

  @media (max-width: 767px)
    flex-direction: column

.section-left
  width: 30%
  &.section-error
      min-width: 235px
      width: auto
      padding-right: 0

  @media (max-width: 767px)
    width: 100%
    padding-right: 0

.section-right
  width: 70%
  padding-left: 10px

  @media (max-width: 767px)
    width: 100%
    margin-top: 20px
    padding-left: 0

.city-inner
  position: relative
  display: inline-block
  width: 100%

  &::after
    content: ''
    position: absolute
    top: 0
    right: 10px
    width: 25px
    height: 25px
    background: url('./assets/img/search.svg') no-repeat 50% 50%
    background-size: contain
    transform: translateY(50%)
    cursor: pointer
    

.info
  height: 100%
  padding: 16px
  background: url('./assets/img/gradient-1.jpg') no-repeat 50% 50%
  background-size: cover
  border-radius: 25px

.search
  width: 100%
  padding: 16px
  font-family: 'Inter', Arial, sans-serif
  color: $white
  background-color: rgba(0, 0, 0, 0.75)
  border-radius: 16px
  border: none
  outline: none
  cursor: pointer

.section-bottom
  width: 50%
  margin-top: 20px

  @media (max-width: 767px)
    width: 100%

.error
  padding-top: 20px

  &-massage
    font-size: 14px

  &-title
    font-size: 18px
    font-weight: 700
</style>
