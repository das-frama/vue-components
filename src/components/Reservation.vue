<template>
  <div class="reservation new" :style="styleObject" @mousedown.self="startDrag">
    {{ timeString }}
    <div class="remove" @click="remove">
      <i class="la la-times"></i>
    </div>
    <div class="resizer" @mousedown="startResize"></div>
  </div>
</template>

<script>
export default {
  props: {
    dateTimeStart: {
      type: Date,
      required: true
    },
    minutes: {
      type: Number,
      default: 60
    },
    createdAt: {
      type: Number,
      required: true
    },
    calendarData: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      isDraggable: false,
      isResizable: false,
      mousePosX: null,
      mousePosY: null
    };
  },
  mounted() {
    window.addEventListener("mouseup", this.stopDrag);
    window.addEventListener("mousemove", this.drag);
    window.addEventListener("mouseup", this.stopResize);
    window.addEventListener("mousemove", this.resize);
  },
  computed: {
    dateTimeEnd() {
      let date = new Date(this.dateTimeStart.getTime());
      date.setMinutes(date.getMinutes() + this.minutes);

      return date;
    },
    hours() {
      return this.minutes / 60;
    },
    styleObject() {
      return {
        top: this.topString,
        left: this.leftString,
        height: this.heightString
      };
    },
    x() {
      let firstDate = new Date(this.calendarData.firstDate);
      let timeDiff = Math.abs(
        this.dateTimeStart.getTime() - firstDate.getTime()
      );
      let diffDays = Math.ceil(timeDiff / (1000 * 3600 * 24)) - 1;

      return (
        this.calendarData.firstCellWidth +
        this.calendarData.cellWidth * diffDays
      );
    },
    y() {
      return (
        this.calendarData.cellHeight +
        (this.dateTimeStart.getHours() - this.calendarData.dayTimeStart) *
          this.calendarData.cellHeight
      );
    },
    topString() {
      return this.y + "px";
    },
    leftString() {
      return this.x + "px";
    },
    heightString() {
      return this.calendarData.cellHeight * this.hours + "px";
    },
    timeString() {
      let options = {
        hour: "2-digit",
        minute: "2-digit"
      };
      let timeStart = new Intl.DateTimeFormat("ru", options).format(
        this.dateTimeStart
      );
      let timeEnd = new Intl.DateTimeFormat("ru", options).format(
        this.dateTimeEnd
      );

      return timeStart + " - " + timeEnd;
    }
  },
  methods: {
    startDrag(event) {
      this.isDraggable = true;
      this.mousePosX = event.clientX;
      this.mousePosY = event.clientY;
    },
    stopDrag() {
      this.isDraggable = false;
      this.mousePosX = null;
      this.mousePosY = null;
    },
    drag(event) {
      if (this.isDraggable) {
        if (event.clientY > this.mousePosY + this.calendarData.cellHeight / 2) {
          this.mousePosY = this.mousePosY + this.calendarData.cellHeight;
          this.$emit(
            "update-date-time",
            this.createdAt,
            new Date(this.dateTimeStart.getTime() + 1000 * 3600)
          );
        } else if (
          event.clientY <
          this.mousePosY - this.calendarData.cellHeight / 2
        ) {
          this.mousePosY = this.mousePosY - this.calendarData.cellHeight;
          this.$emit(
            "update-date-time",
            this.createdAt,
            new Date(this.dateTimeStart.getTime() - 1000 * 3600)
          );
        }
        if (event.clientX > this.mousePosX + this.calendarData.cellWidth / 2) {
          this.mousePosX = this.mousePosX + this.calendarData.cellWidth;
          this.$emit(
            "update-date-time",
            this.createdAt,
            new Date(this.dateTimeStart.getTime() + 1000 * 3600 * 24)
          );
        } else if (
          event.clientX <
          this.mousePosX - this.calendarData.cellWidth / 2
        ) {
          this.mousePosX = this.mousePosX - this.calendarData.cellWidth;
          this.$emit(
            "update-date-time",
            this.createdAt,
            new Date(this.dateTimeStart.getTime() - 1000 * 3600 * 24)
          );
        }
      }
    },
    startResize(event) {
      this.isResizable = true;
      this.mousePosX = event.clientX;
      this.mousePosY = event.clientY;
    },
    stopResize() {
      this.isResizable = false;
      this.mousePosX = null;
      this.mousePosY = null;
    },
    resize(event) {
      if (this.isResizable) {
        if (event.clientY > this.mousePosY + this.calendarData.cellHeight / 2) {
          this.mousePosY = this.mousePosY + this.calendarData.cellHeight;
          this.$emit("update-minutes", this.createdAt, this.minutes + 60);
        } else if (
          event.clientY <
          this.mousePosY - this.calendarData.cellHeight / 2
        ) {
          this.mousePosY = this.mousePosY - this.calendarData.cellHeight;
          this.$emit("update-minutes", this.createdAt, this.minutes - 60);
        }
      }
    },
    remove() {
      this.$emit("remove", this.createdAt);
    }
  }
};
</script>
