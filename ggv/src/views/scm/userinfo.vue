<template>
  <div class="demo-app">
    <div class="demo-app-main">
      <FullCalendar
        ref="calendarRef"
        class="demo-app-calendar"
        :options="calendarOptions"
      >
      </FullCalendar>
    </div>
  </div>
</template>

<script>
import { defineComponent, ref } from "vue";
import FullCalendar from "@fullcalendar/vue3";
import dayGridPlugin from "@fullcalendar/daygrid";
import timeGridPlugin from "@fullcalendar/timegrid";
import interactionPlugin from "@fullcalendar/interaction";
import noticeMgrpopup from "./noticeMgrpopup.vue";
import { openModal } from "jenesius-vue-modal";
//import { INITIAL_EVENTS, createEventId } from '../../event-utils'

export default defineComponent({
  components: {
    FullCalendar,
  },
  data() {
    return {
      calendarOptions: {
        plugins: [dayGridPlugin, timeGridPlugin, interactionPlugin],
        headerToolbar: {
          left: "",
          center: "title",
          right: "prev,next today",
        },
        initialView: "dayGridMonth",
        initialEvents: [], // Initialize events array
        editable: true,
        selectable: true,
        selectMirror: true,
        dayMaxEvents: true,
        weekends: true,
        select: this.handleDateSelect,
        eventClick: this.handleEventClick,
        eventsSet: this.handleEvents,
        eventContent: this.handleEventContent,
        action: "",
      },
      todayStr: new Date().toISOString().replace(/T.*$/, ""),
      initialEvents: [],
      eventGrid: 0,
      currentEvents: [],
      calendarRef: ref(), // Corrected line: use ref() to define calendarRef
      mn_dtm: "",
      mn_no: "",
    };
  },
  mounted() {
    this.calenderlist();
  },
  methods: {
    calenderlist: function () {
      /* 가계부 캘린더 page - 가계부 조회  */
      let params = new URLSearchParams();
      params.append("mn_rgst_id", this.$store.state.loginInfo.loginId);

      this.axios
        .post("/scm/selectgagevueList.do", params)
        .then((response) => {
          const events = response.data.gagevueList.map((item) => ({
            title: item.mn_use_memo,
            startStr: item.mn_dtm,
            mn_no: item.mn_no,
          }));
          this.updateCalendarEvents(events);
        })
        .catch((error) => {
          alert("에러! API 요청에 오류가 있습니다. " + error);
        });
    },
    updateCalendarEvents(events) {
      this.$nextTick(() => {
        const calendar = this.$refs.calendarRef.getApi();

        if (calendar) {
          calendar.removeAllEvents();

          events.forEach((event) => {
            const startDate = new Date(event.startStr);

            calendar.addEvent({
              title: event.title,
              start: startDate,
              mn_no: event.mn_no,
            });
          });

          calendar.render();
        } else {
          console.error("FullCalendar instance is undefined.");
        }
      });
    },
    createEventId() {
      return String(this.eventGuid++);
    },
    handleWeekendsToggle() {
      this.calendarOptions.weekends = !this.calendarOptions.weekends; // update a property
    },
    handleEventContent(arg) {
      const { event } = arg;

      const content = document.createElement("div");
      /*content.innerHTML = `<strong>${event.title}</strong><br>${event.startStr}${event.extendedProps.mn_no}`;*/
      content.innerHTML = `<strong class='boxed-text'>${event.title}</strong>`;

      return { domNodes: [content] };
    },
    async handleDateSelect(selectInfo) {
      /* 달력 날짜 클릭 >> click calendar *click date -> selectInfo.startStr */

      /* 신규등록 */
      this.action = "INSERT";

      const modal = await openModal(noticeMgrpopup, {
        mn_dtm: selectInfo.startStr,
        action: this.action,
      });

      modal.onclose = () => {
        //Modal will not be closed
        this.calenderlist();
      };
    },
    async handleEventClick(clickInfo) {
      /* 수정 */
      this.action = "UPDATE";

      const modal = await openModal(noticeMgrpopup, {
        mn_no: clickInfo.event.extendedProps.mn_no,
        mn_dtm: clickInfo.event.startStr,
        action: this.action,
      });

      modal.onclose = () => {
        //Modal will not be closed
        this.calenderlist();
      };
    },
    handleEvents(events) {
      this.currentEvents = events;
    },
  },
});
</script>

<style lang="css">
.boxed-text {
  border: 1px solid white; /* Border color */
  padding: 2px; /* Padding around the text */
  display: inline-block; /* Make the box only as wide as the content */
  width: 100px;
  background-color: skyblue; /* Background color */
}
h2 {
  margin: 0;
  font-size: 16px;
}

ul {
  margin: 0;
  padding: 0 0 0 1.5em;
}

li {
  margin: 1.5em 0;
  padding: 0;
}

b {
  /* used for event dates/times */
  margin-right: 3px;
}

.demo-app {
  display: flex;
  min-height: 100%;
  font-family: Arial, Helvetica Neue, Helvetica, sans-serif;
  font-size: 14px;
}

.demo-app-sidebar {
  width: 300px;
  line-height: 1.5;
  background: #eaf9ff;
  border-right: 1px solid #d3e2e8;
}

.demo-app-sidebar-section {
  padding: 2em;
}

.demo-app-main {
  flex-grow: 1;
  padding: 3em;
}

.fc {
  /* the calendar root */
  max-width: 1100px;
  margin: 0 auto;
}

table.row {
  width: 120%;
}
</style>
