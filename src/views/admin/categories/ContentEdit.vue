<template>
  <v-dialog :width="200" v-model="dialog">
    <v-card>
      <v-card-text></v-card-text>
      <v-text-field
        v-model="item.label[$store.getters.getLangName]"
        :label="$t('keywords.title')"
        :placeholder="$t('keywords.title')"
        outlined
        dense
      ></v-text-field>
      <v-text-field
        v-model="item.icon"
        :label="$t('keywords.icon')"
        :placeholder="$t('keywords.icon')"
        hide-details
        outlined
        dense
      ></v-text-field>
    </v-card>
    <v-card-actions>
      <v-btn color="warning" text @click="close()">{{$t('keywords.close')}}</v-btn>
      <v-btn color="success" :disabled="disabled" text @click="save()"
        >{{$t('keywords.save')}}</v-btn
      >
    </v-card-actions>
  </v-dialog>
</template>

<script>
export default {
  props: {
    _dialog: {
      type: Boolean,
      default: false,
    },
    _item: {
      type: Object,
      default: () => {},
    },
    _process: {
      type: String,
      default: "",
    },
  },
  data() {
    return {
      disable: null,
      item: {},
      orjItem: {},
    };
  },
  computed: {
    disabled() {
      return this.disable;
    },
    dialog: {
      get() {
        return this._dialog;
      },
      set(value) {
        this.$emit("dialogClose", value);
      },
    },
  },
  mounted() {
    const { ...val } = this._item;
    this.orjItem = { ...val };
    this.item = { ...val };
  },
  methods: {
    close() {
      this.dialog = false;
    },
    save() {
      if (this._process == "edit")
        this.$emit(this._process, {
          process: "Update",
          data: {
            newdata: this.item,
            olddata: this.orjItem,
          },
        });
      else this.$emit(this._process, { process: "Add", data: this.item });
      this.dialog = false;
    },
  },
  watch: {
    item: {
      deep: true,
      handler(val) {
        if (this.orjItem.label == val.label && this.orjItem.icon == val.icon) {
          if (this._process == "createChild") this.disable = false;
          else this.disable = true;
        } else this.disable = false;
      },
    },
  },
  destroyed() {},
};
</script>

<style lang="scss" scoped>
</style>