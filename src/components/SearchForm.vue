<template>
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
          <img src="@/assets/images/icon-arrow.svg" alt="" />
        </button>
      </form>
    </div>
  </header>
</template>

<script>
import { ref } from "vue";
export default {
  name: "SearchForm",
  setup(_, context) {
    const address = ref("");
    const searchLocation = () => {
      context.emit("search-location", address.value)
    }
    return { address, searchLocation }
  }
};
</script>

<style lang="scss" scoped>
header {
  max-width: 500px;
  width: 90%;
  padding-top: 30px;
  margin: 0 auto 150px;

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
</style>