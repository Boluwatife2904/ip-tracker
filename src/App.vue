<template>
  <div class="app-content">
    <header>
      <h1>IP Address Tracker</h1>
      <div class="tracker-form">
        <form>
          <input
            type="text"
            name="address"
            id="address"
            v-model.trim="address"
            placeholder="Search for any IP address or domain"
          />
          <button class="submit">
            <img src="./assets/images/icon-arrow.svg" alt="" />
          </button>
        </form>
      </div>
    </header>

    <div class="results-box">
      <div class="box">
        <span class="title">IP Address</span>
        <h6 class="text-content" v-if="!isLoading">{{ ipAddress }}</h6>
        <lazy-loader v-else></lazy-loader>
      </div>
      <div class="box">
        <span class="title">Location</span>
        <h6 class="text-content" v-if="!isLoading">
          {{ city }}, {{ country }}
        </h6>
        <lazy-loader v-else></lazy-loader>
      </div>
      <div class="box">
        <span class="title">Timezone</span>
        <h6 class="text-content" v-if="!isLoading">UTC {{ timeZone }}</h6>
        <lazy-loader v-else></lazy-loader>
      </div>
      <div class="box">
        <span class="title">ISP</span>
        <h6 class="text-content" v-if="!isLoading">{{ ispInfo }}</h6>
        <lazy-loader v-else></lazy-loader>
      </div>
    </div>
  </div>
</template>

<script>
import LazyLoader from "./components/LazyLoader.vue";

export default {
  components: { LazyLoader },
  name: "App",
  computed: {
    ipAddress() {
      return this.locationInformation.ip;
    },
    ispInfo() {
      return this.locationInformation.isp;
    },
    timeZone() {
      return this.locationInformation.location.timezone;
    },
    city() {
      return this.locationInformation.location.city;
    },
    country() {
      return this.locationInformation.location.country;
    },
  },
  data() {
    return {
      isLoading: true,
      address: "",
      locationInformation: {},
    };
  },
  methods: {
    async fetchLocation(address) {
      let ipAddress = address
        ? `https://geo.ipify.org/api/v1?apiKey=at_78UfYWzp6IU5V1ZGycSWeHBeh4hPU&ipAddress=${address}`
        : `https://geo.ipify.org/api/v1?apiKey=at_78UfYWzp6IU5V1ZGycSWeHBeh4hPU&`;
      this.isLoading = true;
      const response = await fetch(ipAddress);
      const responseData = await response.json();
      this.locationInformation = responseData;
      console.log(responseData);
      setTimeout(() => {
        this.isLoading = false;
      }, 1250);
    },
  },
  created() {
    this.fetchLocation();
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
  height: 220px;
  width: 100%;
  background: url("./assets/images/pattern-bg.png");
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  top: 0;
  left: 0;
  z-index: -1;
}

.app-content {
  header {
    max-width: 500px;
    width: 90%;
    padding-top: 30px;
    margin: 0 auto 40px;

    h1 {
      color: #ffffff;
      text-align: center;
      font-size: 24px;
      margin-bottom: 20px;
      font-weight: 400;
    }

    form {
      display: flex;
      height: 50px;

      input {
        width: 100%;
        height: 100%;
        background: #ffffff;
        border-radius: 15px 0 0 15px;
        border: none;
        outline: none;
        padding: 0 20px;
        font: inherit;
      }

      button {
        width: 60px;
        background: hsl(0, 0%, 17%);
        outline: none;
        border: none;
        border-radius: 0 15px 15px 0;
        height: 100%;
        cursor: pointer;
      }
    }
  }

  .results-box {
    background: #fff;
    border-radius: 15px;
    padding: 20px;
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

      &:after {
        position: absolute;
        content: "";
        height: 70px;
        width: 1px;
        background: rgba(150, 150, 150, 0.5);
        right: 10px;
        top: 21px;
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
