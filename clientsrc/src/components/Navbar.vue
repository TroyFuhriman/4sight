<template>
  <nav class="navbar navbar-expand-lg navbar-light bg-dark">
    <router-link class="navbar-brand text-light" :to="{ name: 'boards' }"
      >4sight</router-link
    >
    <button
      class="navbar-toggler"
      type="button"
      data-toggle="collapse"
      data-target="#navbarText"
      aria-controls="navbarText"
      aria-expanded="false"
      aria-label="Toggle navigation"
    >
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse  navbar-collapse" id="navbarText">
      <ul class="navbar-nav mr-auto">
        <li
          class="nav-item"
          v-if="!$auth.isAuthenticated"
          :class="{ active: $route.name == 'home' }"
        >
          <router-link :to="{ name: 'home' }" class="nav-link text-light"
            >Home</router-link
          >
        </li>
        <li
          class="nav-item"
          v-if="$auth.isAuthenticated"
          :class="{ active: $route.name == 'boards' }"
        >
          <router-link class="nav-link text-light" :to="{ name: 'boards' }"
            >My-Dashboard</router-link
          >
        </li>
      </ul>
      <span class="navbar-text">
        <button
          class="btn btn-success btn-outline-dark"
          @click="login"
          v-if="!$auth.isAuthenticated"
        >
          Login
        </button>
        <button
          class="btn btn-danger btn-outline-dark"
          @click="showLogoutAlert"
          v-else
        >
          logout
        </button>
      </span>
    </div>
  </nav>
</template>

<script>
import axios from "axios";

let _api = axios.create({
  baseURL: "https://localhost:3000",
  withCredentials: true,
});
export default {
  name: "Navbar",
  methods: {
    async login() {
      await this.$auth.loginWithPopup();
      this.$store.dispatch("setBearer", this.$auth.bearer);
      await this.$store.dispatch("getProfile");
      this.$router.push({ name: "boards" });
      console.log("this.$auth.user: ");
      console.log(this.$auth.user);
    },
    async logout() {
      await this.$auth.logout({ returnTo: window.location.origin });
    },
    showLogoutAlert() {
      swal({
        title: "Are you sure you want to log out?",
        icon: "warning",
        buttons: true,
        dangerMode: true,
      }).then((willLogOut) => {
        if (willLogOut) {
          this.logout();
        }
      });
    },
  },
};
</script>

<style></style>
