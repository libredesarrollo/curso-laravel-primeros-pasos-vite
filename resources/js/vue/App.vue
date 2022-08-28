<template>
  <div>
    <header>
      <div class="flex gap-3 bg-gray-200">
        <router-link v-if="!$root.isLoggedIn" :to="{ name: 'login' }"
          >Login</router-link
        >

        <o-button v-if="$root.isLoggedIn" variant="danger" @click="logout">
          Logout
        </o-button>

        <p v-if="$root.isLoggedIn">
          {{ $root.user.name }}
        </p>
      </div>
    </header>

    <router-view></router-view>
  </div>
</template>
<script>
export default {
  data() {
    return {
      isLoggedIn: false,
      user: "",
      token: "",
    };
  },
  created() {
    if (window.Laravel.isLoggedIn) {
      this.isLoggedIn = true;
      this.user = window.Laravel.user;
      this.token = window.Laravel.token;
    } else {
      const auth = this.$cookies.get("auth");
      if (auth) {
        this.isLoggedIn = true;
        this.user = auth.user;
        this.token = auth.token;
        this.$axios
          .post("/api/user/token-check", {
            token: auth.token,
          })
          //.then(() => {})
          .catch(() => {
            this.setCookieAuth("");
            window.location.href = "/vue/login";
          });
      }
    }
  },
  methods: {
    logout() {
      this.$axios
        .post("/api/user/logout", {
          token: this.token,
        })
        .then((res) => {
          this.setCookieAuth("");
          // this.$root.setCookieAuth({
          //   isLoggedIn: false,
          //   token: "",
          //   user: this.user,
          // });
          window.location.href = "/vue/login";
        });
    },
    setCookieAuth(data) {
      this.$cookies.set("auth", data);
    },
  },
};
</script>