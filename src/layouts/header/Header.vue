<template>
  <div>
    <v-app-bar color="header" app>
      <v-app-bar-nav-icon
        color="header_theme_btn"
        @click.stop="$store.dispatch('changeSidebar', !$store.getters['getShowSidebar'])"
      ></v-app-bar-nav-icon>

      <v-toolbar-title class="header_theme_btn--text">
        {{ user.first_name }}</v-toolbar-title
      >

      <v-spacer></v-spacer>

      <div class="auto-complete">
        <auto-complete :_items="finds"></auto-complete>
      </div>
      <div class="theme-mode navbar-nav">
        <v-btn
          name="themeBtn"
          color="header_theme_btn"
          @click="themeMode(!$store.getters.getTheme.isDark)"
          icon
        >
          <v-icon>{{
            $store.getters.getTheme.isDark
              ? "mdi-weather-sunny"
              : "mdi-weather-night"
          }}</v-icon>
        </v-btn>
      </div>

      <div class="lang">
        <!-- I18N -->
        <v-menu offset-y>
          <template v-slot:activator="{ on, attrs }">
            <v-btn fab x-small dark v-bind="attrs" v-on="on">
              <v-img width="0px" :src="$store.getters.getLang.icon"></v-img>
            </v-btn>
          </template>
          <div
            v-for="(item, index) in languages.filter(
              (x) => x.name != $store.getters.getLang.name
            )"
            :key="index"
            style="margin-top: 10px"
          >
            <v-btn fab x-small dark @click="setLang(item)">
              <v-img width="0px" :src="item.icon"></v-img>
            </v-btn>
          </div>
        </v-menu>
      </div>
      <v-menu left bottom offset-y>
        <template v-slot:activator="{ on, attrs }">
          <v-avatar v-if="user.image_path" v-bind="attrs" v-on="on">
            <img :src="user.image_path" :alt="user.first_name" />
          </v-avatar>
          <v-avatar v-else v-bind="attrs" v-on="on">
            <span class="white--text headline">{{ user.first_char }}</span>
          </v-avatar>
        </template>
        <v-list>
          <v-list-item @click="() => {}">
            <v-list-item-title
              ><v-icon>mdi-car-shift-pattern</v-icon>&nbsp;{{
                $t("keywords.tasks")
              }}</v-list-item-title
            >
          </v-list-item>
          <v-list-item @click="() => {}">
            <v-list-item-title
              ><v-icon>mdi-message</v-icon>&nbsp;
              {{ $t("keywords.messages") }}</v-list-item-title
            >
          </v-list-item>
          <v-list-item
            @click="
              () => {
                resetDialog = true;
              }
            "
          >
            <v-list-item-title
              ><v-icon>mdi-lock-reset</v-icon>&nbsp;
              {{ $t("phrases.resetPassword") }}</v-list-item-title
            >
          </v-list-item>
          <v-list-item @click="$router.push({ name: 'UserOption' })">
            <v-list-item-title
              ><v-icon>mdi-cog-outline</v-icon>&nbsp;
              {{ $t("keywords.options") }}</v-list-item-title
            >
          </v-list-item>
          <v-divider></v-divider>
          <v-list-item @click="logout()">
            <v-list-item-title
              ><v-icon>mdi-lock</v-icon>&nbsp;{{
                $t("keywords.logout")
              }}</v-list-item-title
            >
          </v-list-item>
        </v-list>
      </v-menu>
    </v-app-bar>
    <reset-password
      :_dialog="resetDialog"
      v-on:dialogClose="
        (value) => {
          resetDialog = value;
        }
      "
    ></reset-password>
  </div>
</template>

<script>
import finds from "@/core/datas/find.json";
export default {
  components: {
    ResetPassword: () => import("@/views/auth/ResetPassword"),
    AutoComplete: () => import(`@/components/Autocomplete.vue`),
  },
  data() {
    return {
      user: {},
      resetDialog: false,
      languages: [
        {
          name: "en",
          icon: require("../../assets/vendor/svg/en.svg"),
        },
        {
          name: "de",
          icon: require("../../assets/vendor/svg/de.png"),
        },
        {
          name: "fr",
          icon: require("../../assets/vendor/svg/fr.svg"),
        },
        {
          name: "tr",
          icon: require("../../assets/vendor/svg/tr.svg"),
        },
      ],
      finds: finds
    };
  },
  mounted() {
    this.user = this.firstChar(this.$store.getters.currentUser);
  },
  methods: {
    logout() {
      this.$store.dispatch("logout").then((x) => {
        if (x) {
          this.$router.push({
            name: "Login",
            query: { returnPath: this.$route.path },
          });
        }
      });
    },
    firstChar(x) {
      return {
        ...x,
        first_char: x.first_name + "" + x.last_name,
      };
    },
    /** Theme Mode */
    themeMode(status) {
      this.$store.dispatch("changeUserTheme", status);
    },
    setLang(item) {
      this.$store.dispatch("lang", item);
    },
  },
};
</script>

<style scoped>
.lang {
  margin-right: 10px;
}
.auto-complete{
  width: 400px;
}
</style>