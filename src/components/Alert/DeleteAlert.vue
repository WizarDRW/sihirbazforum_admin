<template>
  <v-alert :type="type" elevation="2" transition="scale-transition">
    <v-row align="center">
      <v-col class="grow">
        {{
          $t(
            type == "warning"
              ? "message.delete_content"
              : type == "success"
              ? "message.delete_content_success"
              : "message.delete_content_error",
            { id: msg, error: msg }
          )
        }}
        <v-progress-linear
          v-if="type != 'success'"
          :buffer-value="100"
          :value="value"
          :width="100"
          color="amber"
          :indeterminate="loading"
        >
        </v-progress-linear>
      </v-col>
      <v-col class="shrink">
        <v-btn @click="clear()" icon v-if="!cancel && type != 'success'">
          <v-icon> mdi-undo </v-icon>
        </v-btn>
      </v-col>
    </v-row>
  </v-alert>
</template>

<script>
export default {
  props: {
    _content: {
      type: Object,
      default: () => {},
    },
    _queue_id: {
      type: Number,
      default: -1,
    },
  },
  data() {
    return {
      interval: {},
      cancel: false,
      value: 100,
      type: "warning",
      msg: "",
      loading: false,
    };
  },
  created() {
    this.msg = this._content.msg;
    this.interval = setInterval(async () => {
      if (this.value === 0) {
        this.loading = true;
        this.deleteAsync();
        this.cancel = true;
      }
      this.value -= 10;
    }, 1000);
    this.cancel = false;
  },
  computed: {
    alert: {
      get() {
        return this._alert;
      },
      set(val) {
        this.$emit("input", val);
      },
    },
  },
  methods: {
    clear() {
      this.cancel = true;
      clearInterval(this.interval);
      this.$store.dispatch("outQueue", this._queue_id);
    },
    async deleteAsync() {
      if (!this.cancel) {
        var result = await this.$store.dispatch(
          this._content.func,
          this._content.itemid
        );
        if (result == 200) {
          this.type = "success";
        } else {
          this.msg = result.message;
          this.type = "error";
        }
        setTimeout(() => {
          this.clear();
        }, 2000);
      }
    },
  },
};
</script>

<style lang="scss" scoped>
</style>