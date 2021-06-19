<template>
  <div class="app-content">
    <header>
      <h1>IP Address Tracker</h1>
      <div class="tracker-form">
        <form @submit.prevent="searchLocation">
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
import LazyLoader from "./components/LazyLoader.vue";

export default {
  components: { LazyLoader },
  name: "App",
  computed: {
    ipAddress() {
      return this.locationInformation.ip;
    },
    ispInfo() {
      if (this.locationInformation.isp !== "") {
        return this.locationInformation.isp;
      } else {
        return "-----";
      }
    },
    timeZone() {
      if (this.locationInformation.location.timezone !== "") {
        return this.locationInformation.location.timezone;
      } else {
        return "-----";
      }
    },
    city() {
      if (this.locationInformation.location.city !== "") {
        return this.locationInformation.location.city;
      } else {
        return "-----";
      }
    },
    country() {
      if (this.locationInformation.location.country !== "") {
        return this.locationInformation.location.country;
      } else {
        return "-----";
      }
    },
  },
  data() {
    return {
      isLoading: false,
      fetchError: false,
      address: "",
      locationInformation: null,
    };
  },
  methods: {
    async fetchLocation(address) {
      let ipAddress = address
        ? `https://geo.ipify.org/api/v1?apiKey=at_78UfYWzp6IU5V1ZGycSWeHBeh4hPU&ipAddress=${address}`
        : `https://geo.ipify.org/api/v1?apiKey=at_78UfYWzp6IU5V1ZGycSWeHBeh4hPU&`;
      this.isLoading = true;
      try {
        const response = await fetch(ipAddress);
        const responseData = await response.json();
        if (responseData !== {}) {
          console.log(responseData)
          this.locationInformation = responseData;
        }
        console.log(responseData);
        setTimeout(() => {
          this.isLoading = false;
        }, 1250);
      } catch (error) {
        console.log(error);
        setTimeout(() => {
          this.isLoading = false;
        }, 1250);
        this.fetchError = true;
      }
    },
    searchLocation() {
      if (this.address === "") {
        alert("Kindly enter a valid IP Address");
        return;
      } else if(!this.ValidateIPaddress(this.address)){
        alert("You have entered an invalid IP Address.");
        return;
      } else {
        this.fetchLocation(this.address);
      }
    },
    ValidateIPaddress(ipaddress) {
      return /^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/.test(
        ipaddress
      );
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
      height: 60px;

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
