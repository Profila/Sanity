<template>
  <div id="header">
    <nav class="header-left">
      <router-link to="/" class="header_link_logo">
        <Logo :size="26" />
      </router-link>
      <router-link to="/requests">Requests</router-link>
      <router-link to="/stream">Stream</router-link>
      <router-link to="/horizon">Horizon</router-link>
      <router-link to="/utils">Utils</router-link>
    </nav>
    <div class="header-right">
      <div
        @click="emitExportEvent"
        v-if="exportAvailable"
        class="header-export"
      >
        <span>EXPORT <Icon size="12" type="md-download" /></span>
      </div>
      <div v-if="$store.state.user" class="header-user">
        <span @click="logout"
          >{{ $store.state.user.display }} <Icon size="12" type="md-exit"
        /></span>
      </div>
    </div>
  </div>
</template>

<script>
import { EventBus } from "@/helpers/eventBus";

import notifications from "@/mixins/notifications";

import Logo from "@/components/Logo.vue";

export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: "Header",
  components: {
    Logo,
  },
  mixins: [notifications],
  computed: {
    exportAvailable() {
      return !["Utils", "Access", "Labels"].includes(this.$route.name);
    },
  },
  methods: {
    logout() {
      if (this.$store.state.firebaseInstance.auth) {
        this.$store.state.firebaseInstance
          .auth()
          .signOut()
          .then(() => {
            this.$store.commit("logoutUser");

            this.notificationSuccess("You have been logged out successfully.");
            this.$router.push("/");
          })
          .catch((error) => {
            this.notificationError(error);
          });

        return;
      }

      this.$store.commit("logoutUser");
      this.notificationSuccess("You have been logged out successfully.");
      this.$router.push("/");
    },
    emitExportEvent() {
      EventBus.$emit(`export-${this.$route.name.toLowerCase()}`);
    },
  },
};
</script>
