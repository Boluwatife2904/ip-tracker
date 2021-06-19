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
  </div>
</template>

<script>
import { ref, computed } from "vue";
import LazyLoader from "./components/LazyLoader.vue";
import SearchForm from "./components/SearchForm.vue";

export default {
  components: { LazyLoader, SearchForm },
  name: "App",
  setup() {
    // Data
    const isLoading = ref(false);
    const fetchError = ref(false);
    const locationInformation = ref(null);

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
          alert(responseData.messages);
        } else {
          locationInformation.value = responseData;
        }
        setTimeout(() => {
          isLoading.value = false;
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
  height: 250px;
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

.app-content {
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
