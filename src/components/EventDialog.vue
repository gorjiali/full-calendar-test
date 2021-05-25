<template>
  <v-dialog v-model="dialog" width="700">
    <v-card class="pb-3">
      <v-card-title class="success white--text mb-8">Insert Event Info</v-card-title>
      <v-card-text>
        <v-form ref="form" @submit.prevent="confirmEvent">
          <v-row>
            <v-col class="py-0" cols="12" md="6">
              <DatePickerMenu label="Start At" v-model="startDate" />
            </v-col>
            <v-col class="py-0" cols="12" md="6">
              <TimePickerMenu label="Time" v-model="startTime" />
            </v-col>
          </v-row>
          <v-row>
            <v-col class="py-0" cols="12" md="6">
              <DatePickerMenu label="EndAt" v-model="endDate" />
            </v-col>
            <v-col class="py-0" cols="12" md="6">
              <TimePickerMenu label="Time" v-model="endTime" />
            </v-col>
          </v-row>
          <v-row>
            <v-col class="py-0" cols="12">
              <v-textarea :rules="requiredRule" v-model="title" outlined dense label="Event Description"></v-textarea>
            </v-col>
          </v-row>
          <v-row justify="end">
            <v-btn type="button" @click="$emit('removeEvent', id)" depressed class="mr-3" color="error">Delete</v-btn>
            <v-btn type="submit" depressed class="mr-3" color="primary">Save</v-btn>
          </v-row>
        </v-form>
      </v-card-text>
    </v-card>
  </v-dialog>
</template>

<script>
import DatePickerMenu from "./DatePickerMenu.vue";
import TimePickerMenu from "./TimePickerMenu.vue";
export default {
  components: { DatePickerMenu, TimePickerMenu },
  props: {
    value: { type: Boolean, default: false }
  },

  data() {
    return {
      id: "",
      title: "",
      startDate: "",
      endDate: "",
      startTime: "",
      endTime: "",
      mode: "create",
      requiredRule: [v => !!v || "required"]
    };
  },

  computed: {
    dialog: {
      get() {
        return this.value;
      },
      set(val) {
        this.$emit("input", val);
      }
    }
  },

  watch: {
    dialog: function(newVal) {
      if (!newVal) {
        this.resetDialog();
      }
    }
  },

  methods: {
    confirmEvent() {
      if (this.$refs.form.validate()) {
        const event = {
          id: this.id,
          title: this.title,
          start: `${this.startDate}T${this.startTime}:00`,
          end: `${this.endDate}T${this.endTime}:00`
        };
        if (this.mode === "create") {
          this.$emit("addEvent", event);
        } else {
          this.$emit("updateEvent", event);
        }
      }
    },
    formatSelectedDate(dateWithTime) {
      // pull date from dateWithTime and return it
      return dateWithTime.split("T")[0];
    },
    formatSelectedTime(dateWithTime) {
      // pull time from dateWithTime and return it without seconds & locale info
      return dateWithTime
        .split("T")[1]
        .split("+")[0]
        .split(":")
        .slice(0, 2)
        .join(":");
    },
    setSelectedInfo(info) {
      // has time, selected from week or day view
      if (info.startStr.includes("T")) {
        this.startDate = this.formatSelectedDate(info.startStr);
        this.startTime = this.formatSelectedTime(info.startStr);
        this.endDate = this.formatSelectedDate(info.endStr);
        this.endTime = this.formatSelectedTime(info.endStr);
      }
      // hasn't time, selected from month view
      else {
        this.startDate = info.startStr;
        this.startTime = "00:00";
        this.endDate = info.endStr;
        this.endTime = "00:00";
      }
      // edit mode
      if (info.title) {
        this.title = info.title;
      }
      if (info.id) {
        this.id = info.id;
      }
    },
    setSelectedDate(info) {
      this.setSelectedInfo(info);
    },
    setSelectedEvent(info) {
      console.log(info);
      this.setSelectedInfo(info.event);
      this.mode = "edit";
    },
    resetDialog() {
      this.title = "";
      this.startDate = "";
      this.endDate = "";
      this.startTime = "";
      this.endTime = "";
      this.mode = "create";
      this.$refs.form.resetValidation();
    }
  }
};
</script>
