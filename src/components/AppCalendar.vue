<template>
  <div class="pa-5">
    <FullCalendar ref="fullCalendar" :options="calendarOptions" />
    <EventDialog ref="eventDialogRef" v-model="eventDialog" @addEvent="addEvent" @updateEvent="updateEvent" @removeEvent="removeEvent" />
  </div>
</template>

<script>
import FullCalendar from "@fullcalendar/vue";
import dayGridPlugin from "@fullcalendar/daygrid";
import timeGridPlugin from "@fullcalendar/timegrid";
import interactionPlugin from "@fullcalendar/interaction";
import EventDialog from "./EventDialog.vue";
import { v4 as uuidv4 } from "uuid";

export default {
  components: {
    FullCalendar,
    EventDialog
  },
  data() {
    return {
      eventDialog: false,
      calendarOptions: {
        editable: true,
        selectable: true,
        plugins: [dayGridPlugin, timeGridPlugin, interactionPlugin],
        initialView: "timeGridDay",
        headerToolbar: {
          start: "prev today next addEventButton",
          center: "title",
          end: "dayGridMonth,timeGridWeek,timeGridDay"
        },
        customButtons: {
          addEventButton: {
            text: "add event",
            click: () => (this.eventDialog = true)
          }
        },
        select: info => this.selectDate(info),
        eventClick: info => this.selectEvent(info)
      }
    };
  },

  computed: {
    calendarApi() {
      return this.$refs.fullCalendar.getApi();
    }
  },

  methods: {
    addEvent(event) {
      event.id = uuidv4();
      this.calendarApi.addEvent(event);
      this.eventDialog = false;
    },
    updateEvent(event) {
      const updatedEvent = this.calendarApi.getEventById(event.id);
      updatedEvent.setProp("title", event.title);
      updatedEvent.setStart(event.start);
      updatedEvent.setEnd(event.end);
      this.eventDialog = false;
    },
    removeEvent(eventId) {
      const removedEvent = this.calendarApi.getEventById(eventId);
      removedEvent.remove();
      this.eventDialog = false;
    },
    selectDate(info) {
      this.$refs.eventDialogRef.setSelectedDate(info);
      this.eventDialog = true;
    },
    selectEvent(info) {
      this.$refs.eventDialogRef.setSelectedEvent(info);
      this.eventDialog = true;
    }
  }
};
</script>
