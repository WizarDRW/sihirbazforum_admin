<template>
  <v-subheader app>
    <div class="route">
      <v-breadcrumbs :items="$route.matched">
        <template v-slot:divider>
          <v-icon>mdi-chevron-right</v-icon>
        </template>
        <template v-slot:item="{ item }">
          <v-breadcrumbs-item
            :href="item.path === '' ? '/' : item.path"
            :disabled="
              item.path === $route.matched[$route.matched.length - 1].path
            "
          >
            {{ edit_title ? $t(item.meta.lang, {edit: edit_title}).toUpperCase():$t(item.meta.lang).toUpperCase() }}
          </v-breadcrumbs-item>
        </template>
      </v-breadcrumbs>
    </div>
    <slot name="buttons"></slot>
  </v-subheader>
</template>

<script>
export default {
  props: {
    edit_title: {
      type: String,
      default: null
    }
  }
};
</script>

<style scoped>
.route {
  display: inline;
  width: 100%;
}
.route a {
  color: #fff;
  text-decoration: none;
}
</style>