<template>
  <div class="calendar">
    <div class="weekdays">
      <div class="month cell">Август</div>
      <div
        class="weekday cell"
        v-for="(weekDay, index) in weekDays"
        :key="index"
      >
        {{ weekDay }}
      </div>
    </div>
    <div class="days">
      <div class="time" v-for="dayTime in dayTimes" :key="dayTime">
        <div class="cell">{{ dayTime }}</div>
        <div
          class="cell free"
          v-for="(date, index) in dates"
          :key="index"
          @click="addReservation(`${date} ${dayTime}`)"
        ></div>
      </div>
    </div>
    <div ref="reservations" class="reservations">
      <reservation
        v-for="reservation in reservations"
        :key="reservation.createdAt"
        ref="reservationChild"
        @update-date-time="onUpdateDateTime"
        @update-minutes="onUpdateMinutes"
        @remove="onDelete"
        :date-time-start="reservation.dateTime"
        :minutes="reservation.minutes"
        :created-at="reservation.createdAt"
        :calendar-data="calendarData"
      />
    </div>
    <div class="prev">
      <svg viewBox="-22 -2 50 80" xml:space="preserve">
        <polyline
          fill="none"
          stroke="#3a3f45"
          stroke-width="1"
          stroke-linecap="round"
          stroke-linejoin="round"
          points="25.63,75.8 0.375,38.087 25.63,0.375"
        ></polyline>
      </svg>
    </div>
    <div class="next">
      <svg viewBox="-2 -2 50 80" xml:space="preserve">
        <polyline
          fill="none"
          stroke="#3a3f45"
          stroke-width="1"
          stroke-linecap="round"
          stroke-linejoin="round"
          points="0.375,0.375 25.63,38.087 0.375,75.8"
        ></polyline>
      </svg>
    </div>
  </div>
</template>

<script>
import Reservation from "./Reservation";

export default {
  // props: ['week', 'year'],
  components: {
    Reservation
  },
  data() {
    return {
      dayTimeStart: 8,
      dayTimeEnd: 20,
      dates: [
        "2018-08-13",
        "2018-08-14",
        "2018-08-15",
        "2018-08-16",
        "2018-08-17",
        "2018-08-18",
        "2018-08-19"
      ],
      reservations: []
    };
  },
  computed: {
    calendarData() {
      return {
        firstCellWidth: this.firstCellWidth,
        cellWidth: this.cellWidth,
        cellHeight: this.cellHeight,
        firstDate: this.dates[0],
        dayTimeStart: this.dayTimeStart
      };
    },
    firstCellWidth() {
      return this.$el.getElementsByClassName("cell")[0].offsetWidth;
    },
    cellWidth() {
      return this.$el.getElementsByClassName("cell free")[0].offsetWidth;
    },
    cellHeight() {
      return this.$el.getElementsByClassName("cell free")[0].offsetHeight;
    },
    dayTimes() {
      let dayTimes = [];
      for (let i = this.dayTimeStart; i <= this.dayTimeEnd; i++) {
        dayTimes.push((i < 10 ? "0" + i : i) + ":00");
      }

      return dayTimes;
    },
    weekDays() {
      let weekDays = [];
      let options = {
        weekday: "short",
        day: "2-digit"
      };
      this.dates.slice(0).forEach(dateString => {
        weekDays.push(
          new Intl.DateTimeFormat("ru", options).format(new Date(dateString))
        );
      });

      return weekDays;
    }
  },
  watch: {
    reservations: {
      handler() {
        this.collapseReservations();
      },
      deep: true
    }
  },
  methods: {
    onUpdateDateTime(key, dateTime) {
      let index = this.reservations.findIndex(
        reservation => reservation.createdAt === key
      );
      this.reservations[index].dateTime = dateTime;
    },
    onUpdateMinutes(key, minutes) {
      let index = this.reservations.findIndex(
        reservation => reservation.createdAt === key
      );
      this.reservations[index].minutes = minutes;
    },
    onDelete(key) {
      let index = this.reservations.findIndex(
        reservation => reservation.createdAt === key
      );
      this.reservations.splice(index, 1);
    },
    addReservation(dateTimeString) {
      this.reservations.push({
        dateTime: new Date(dateTimeString),
        minutes: 60,
        createdAt: Date.now()
      });
    },
    collapseReservations() {
      if (this.reservations.length === 0) {
        return;
      }
      this.reservations.forEach(reservation1 => {
        let reservation1TimeStart = reservation1.dateTime.getTime();
        let reservation1TimeEnd =
          reservation1TimeStart + 1000 * 60 * reservation1.minutes;
        this.reservations.forEach((reservation2, index2, object2) => {
          if (reservation1 === reservation2) {
            return;
          }
          let reservation2TimeStart = reservation2.dateTime.getTime();
          let reservation2TimeEnd =
            reservation2TimeStart + 1000 * 60 * reservation2.minutes;
          if (reservation1TimeEnd === reservation2TimeStart) {
            reservation1.minutes += reservation2.minutes;
            object2.splice(index2, 1);
          } else if (reservation1TimeStart === reservation2TimeEnd) {
            reservation1.dateTime = new Date(reservation2TimeStart);
            reservation1.minutes += reservation2.minutes;
            object2.splice(index2, 1);
          }
        });
      });
    }
  }
};
</script>
