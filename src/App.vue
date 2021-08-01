<template>
  <div class="app-content">
    <SearchForm @search-location="searchLocation" />
    <div class="results-box">
      <div class="box">
        <span class="title">IP Address</span>
        <lazy-loader v-if="isLoading"></lazy-loader>
        <h6 class="text-content" v-else-if="!isLoading && !fetchError">
          {{ ipAddress }}
        </h6>
        <h6 class="error-content" v-else>-----</h6>
      </div>
      <div class="box">
        <span class="title">Location</span>
        <lazy-loader v-if="isLoading"></lazy-loader>
        <h6 class="text-content" v-else-if="!isLoading && !fetchError">
          {{ city }}, {{ country }}
        </h6>
        <h6 class="error-content" v-else>-----</h6>
      </div>
      <div class="box">
        <span class="title">Timezone</span>
        <lazy-loader v-if="isLoading"></lazy-loader>
        <h6 class="text-content" v-else-if="!isLoading && !fetchError">
          UTC {{ timeZone }}
        </h6>
        <h6 class="error-content" v-else>-----</h6>
      </div>
      <div class="box">
        <span class="title">ISP</span>
        <lazy-loader v-if="isLoading"></lazy-loader>
        <h6 class="text-content" v-else-if="!isLoading && !fetchError">
          {{ ispInfo }}
        </h6>
        <h6 class="error-content" v-else>-----</h6>
      </div>
    </div>
    <!-- MAPS -->
    <div class="map">
      <div id="mapid"></div>
      <div class="loader" v-if="isLoading">
        <div></div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed, onMounted } from "vue";
import leaflet from "leaflet";
import LazyLoader from "./components/LazyLoader.vue";
import SearchForm from "./components/SearchForm.vue";

export default {
  components: { LazyLoader, SearchForm },
  name: "App",
  setup() {
    let mymap;
    // Data
    const isLoading = ref(false);
    const fetchError = ref(false);
    const locationInformation = ref(null);

    onMounted(() => {
      mymap = leaflet.map("mapid").setView([51.505, -0.09], 9);
      leaflet
        .tileLayer(
          "https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=pk.eyJ1IjoiYm9sdXdhdGlmZSIsImEiOiJja3JyZHBqemE1anU3MnVwOGZjOXRzcHdlIn0.vUD6lmkxdUolOjiAS3P7CQ",
          {
            attribution:
              'Map data &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
            maxZoom: 18,
            id: "mapbox/streets-v11",
            tileSize: 512,
            zoomOffset: -1,
            accessToken:
              "pk.eyJ1IjoiYm9sdXdhdGlmZSIsImEiOiJja3JyZHBqemE1anU3MnVwOGZjOXRzcHdlIn0.vUD6lmkxdUolOjiAS3P7CQ",
          }
        )
        .addTo(mymap);
    });

    // Methods
    const fetchLocation = async (location) => {
      let ipAddress = location
        ? `https://geo.ipify.org/api/v1?apiKey=at_78UfYWzp6IU5V1ZGycSWeHBeh4hPU&ipAddress=${location}`
        : `https://geo.ipify.org/api/v1?apiKey=at_78UfYWzp6IU5V1ZGycSWeHBeh4hPU&`;
      isLoading.value = true;
      try {
        const response = await fetch(ipAddress);
        const responseData = await response.json();
        if (!response.ok) {
          const error = response.statusText;
          throw error;
        }
        if (responseData.code === 422) {
          isLoading.value = false;
          fetchError.value = true;
        } else {
          locationInformation.value = responseData;
        }
        setTimeout(() => {
          isLoading.value = false;
          leaflet
            .marker([responseData.location.lat, responseData.location.lng])
            .addTo(mymap);
          mymap.setView(
            [responseData.location.lat, responseData.location.lng],
            13
          );
        }, 500);
      } catch {
        isLoading.value = false;
        fetchError.value = true;
      }
    };
    fetchLocation();

    const ValidateIPaddress = (ipaddress) => {
      return /^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/.test(
        ipaddress
      );
    };

    const searchLocation = (value) => {
      if (value === "") {
        alert("Kindly enter a valid IP Address");
        return;
      } else if (!ValidateIPaddress(value)) {
        alert("You have entered an invalid IP Address.");
        return;
      } else {
        fetchLocation(value);
      }
    };

    // Computed properties
    const ipAddress = computed(() => locationInformation.value.ip);
    const ispInfo = computed(() => {
      if (locationInformation.value.isp !== "") {
        return locationInformation.value.isp;
      } else {
        return "-----";
      }
    });
    const timeZone = computed(() => {
      if (locationInformation.value.location.timezone !== "") {
        return locationInformation.value.location.timezone;
      } else {
        return "-----";
      }
    });
    const city = computed(() => {
      if (locationInformation.value.location.city !== "") {
        return locationInformation.value.location.city;
      } else {
        return "-----";
      }
    });
    const country = computed(() => {
      if (locationInformation.value.location.country !== "") {
        return locationInformation.value.location.country;
      } else {
        return "-----";
      }
    });

    return {
      isLoading,
      fetchError,
      locationInformation,
      searchLocation,
      ipAddress,
      ispInfo,
      timeZone,
      city,
      country,
    };
  },
};
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Rubik:wght@300;400;500;600;700&display=swap");

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html {
  scroll-behavior: smooth;
}

body {
  font-family: "Rubik", sans-serif;
  position: relative;
  z-index: 1;
}

body::after {
  content: "";
  position: absolute;
  height: 300px;
  width: 100%;
  background: url("./assets/images/pattern-bg.png");
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  top: 0;
  left: 0;
  z-index: -1;

  @media screen and (max-width: 576px) {
    height: 340px;
  }
}

#mapid {
  height: 700px;
  z-index: 10;
}

@keyframes rota {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

.map {
  position: relative;
  height: 700px;
  z-index: 10;
  overflow: hidden;

  .loader {
    position: absolute;
    top: 0;
    left: 0;
    background: rgba(255, 255, 255, 0.95);
    width: 100%;
    height: 700px;
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 11;

    div {
      height: 50px;
      width: 50px;
      border: 5px solid rebeccapurple;
      border-radius: 50%;
      border-top: 5px solid transparent;
      animation: rota 1s ease-in-out infinite;
    }
  }
}

.app-content {
  position: relative;
  .results-box {
    background: #fff;
    border-radius: 15px;
    padding: 30px 20px;
    max-width: 1024px;
    width: 90%;
    margin: auto;
    display: flex;
    justify-content: space-between;
    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
    flex-wrap: wrap;
    position: absolute;
    top: 200px;
    left: 50%;
    transform: translateX(-50%);
    z-index: 20;

    .box {
      position: relative;
      padding: 10px 20px;
      width: 25%;
      flex: 1 1 25%;

      @media screen and (max-width: 992px) {
        width: 50%;
        flex: 1 1 50%;
      }

      @media screen and (max-width: 576px) {
        width: 100%;
        flex: 1 1 100%;
        text-align: center;
        margin-bottom: 10px;
        padding: 10px;

        &:after {
          display: none;
        }
      }

      .title {
        text-transform: uppercase;
        font-size: 14px;
        color: hsl(0, 0%, 59%);
        font-weight: 500;
        display: inline-block;
        margin-bottom: 10px;
      }

      .text-content {
        color: hsl(0, 0%, 17%);
        font-size: 24px;
        font-weight: 500;
        display: -webkit-box;
        -webkit-line-clamp: 1;
        -webkit-box-orient: vertical;
        overflow: hidden;
      }

      .error-content {
        color: hsl(0, 0%, 17%);
        font-size: 24px;
        font-weight: 500;
        margin: 0 0 20px;
      }

      &:after {
        position: absolute;
        content: "";
        height: 70px;
        width: 1px;
        background: rgba(150, 150, 150, 0.5);
        right: 10px;
        top: 1px;
      }

      &:last-child {
        &:after {
          display: none;
        }
      }

      &:nth-child(2) {
        @media screen and (max-width: 992px) {
          &:after {
            display: none;
          }
        }
      }
    }
  }
}
</style>
