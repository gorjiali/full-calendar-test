<template>
  <v-menu
    ref="menu"
    v-model="menu"
    :close-on-content-click="false"
    :nudge-right="40"
    :return-value.sync="time"
    transition="scale-transition"
    offset-y
    max-width="290px"
    min-width="290px"
  >
    <template v-slot:activator="{ on, attrs }">
      <v-text-field v-model="time" :rules="requiredRule" dense outlined :label="label" prepend-inner-icon="mdi-clock-time-four-outline" readonly v-bind="attrs" v-on="on"></v-text-field>
    </template>
    <v-time-picker v-if="menu" no-title v-model="time" full-width @click:minute="$refs.menu.save(time)"></v-time-picker>
  </v-menu>
</template>

<script>
export default {
  props: {
    value: {
      type: String,
      required: true
    },
    label: {
      type: String,
      required: true
    }
  },

  data() {
    return {
      menu: false,
      requiredRule: [v => !!v || "required"]
    };
  },

  computed: {
    time: {
      get() {
        return this.value;
      },
      set(val) {
        this.$emit("input", val);
      }
    }
  }
};
</script>

<style></style>
