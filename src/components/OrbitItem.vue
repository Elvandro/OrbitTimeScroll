<template>
  <div class="orbit-container">
    <div
      class="orbit"
      :style="{
        width: calcOrbitDiameter + '%',
        height: calcOrbitDiameter + '%'
      }"
    >
      <div
        class="point"
        v-for="(point, index) in points"
        :key="index"
        :style="{
          left: `${point.x}%`,
          top: `${point.y}%`
        }"
        @mouseenter="showInfo(point, $event)"
        @mouseleave="onMouseLeave"
      >
        <img class="orbit-avatar" :src="point.img" alt="avatar" />
      </div>
    </div>
  </div>
  <div
    class="card"
    v-if="selectedPoint"
    :style="{ top: cardPosition.top + 'px', left: cardPosition.left + 'px' }"
    @mouseenter="onMouseEnterCard"
    @mouseleave="onMouseLeaveCard"
  >
    <div class="card-header">
      <img class="card-avatar" :src="selectedPoint.img" alt="avatar" />
      <div class="name-info">
        <div class="name">{{ selectedPoint.name }}</div>
        <div class="position">{{ selectedPoint.position }}</div>
        <div class="position">{{ selectedPoint.city }}</div>
      </div>
    </div>
    <div class="card-body">
      <div class="email">
        <div class="email-icon">
          <!-- SVG code for email icon -->
          <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor">
            <path
              d="M12 13.5L2 7V17a2 2 0 0 0 2 2h16a2 2 0 0 0 2-2V7l-10 6.5zM12 11.5L22 5H2l10 6.5z"
            />
          </svg>
        </div>
        <div>Reply from Emery Wells</div>
      </div>
      <div class="time-info">
        <div>{{ formatDate }}</div>
        <div>{{ relativeTime }}</div>
      </div>
      <div class="more-info">
        <div class="message-head">{{ selectedPoint._orbits_last_message.message_head }}</div>
        <div class="message-body">
          <div :class="{ message: true, expanded: isExpanded }">
            {{ selectedPoint._orbits_last_message.message }}
          </div>

          <div @click="toggleText" class="toggle-more">
            {{ isExpanded ? 'Less' : 'More' }}
            <div class="chevron-icon" v-if="!isExpanded">
              <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor">
                <path d="M12 15.5L3 6.5 4.5 5 12 12.5 19.5 5 21 6.5z" />
              </svg>
            </div>
            <div class="chevron-icon" v-if="isExpanded">
              <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor">
                <path d="M12 8.5L4.5 16 3 14.5 12 5.5 21 14.5 19.5 16z" />
              </svg>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


<script>
export default {
  props: {
    orbit: {
      type: Object,
      required: false
    },
    orbitIndex: {
      type: Number,
      required: false
    },
    noOfOrbits: {
      type: Number,
      required: false
    }
  },

  data() {
    return {
      points: [],
      numberOfPoints: 0,
      selectedPoint: null,
      cardPosition: { top: 0, left: 0 },
      moreInfoVisible: false,
      hoveringCard: false,
      hoveringPoint: false,
      isExpanded: false
    }
  },

  computed: {
    calcOrbitDiameter() {
      return (100 / this.noOfOrbits) * (this.noOfOrbits + 1 - this.orbitIndex)
    },
    formatDate() {
      const timestamp = this.selectedPoint?.created_at
      const date = new Date(timestamp / 1000)

      // Format the date
      const daysOfWeek = [
        'Sunday',
        'Monday',
        'Tuesday',
        'Wednesday',
        'Thursday',
        'Friday',
        'Saturday'
      ]
      const monthsOfYear = [
        'January',
        'February',
        'March',
        'April',
        'May',
        'June',
        'July',
        'August',
        'September',
        'October',
        'November',
        'December'
      ]

      const dayOfWeek = daysOfWeek[date.getUTCDay()]
      const month = monthsOfYear[date.getUTCMonth()]
      const day = date.getUTCDate()
      const year = date.getUTCFullYear()

      // Format the time
      let hours = date.getUTCHours()
      const minutes = date.getUTCMinutes()
      const isAM = hours < 12
      hours = hours % 12 || 12 // Convert to 12-hour format

      const formattedMinutes = minutes < 10 ? '0' + minutes : minutes
      const period = isAM ? 'AM' : 'PM'

      // Combine the date and time into the desired format
      return `${dayOfWeek}, ${month} ${day} ${year} at ${hours}:${formattedMinutes} ${period} UTC`
    },

    relativeTime() {
      const timestamp = this.selectedPoint?.created_at
      const date = new Date(timestamp / 1000)

      const now = new Date()
      const differenceInMillis = now - date
      const differenceInDays = Math.floor(differenceInMillis / (1000 * 60 * 60 * 24))
      return `${differenceInDays} days ago`
    }
  },

  mounted() {
    this.calculatePoints()
  },

  methods: {
    calculatePoints() {
      this.points = this.orbit?.array
      this.numberOfPoints = this.points.length
      const diameter = this.calcOrbitDiameter
      const radius = diameter / 2
      const angleStep = Math.PI / (this.numberOfPoints - 1)

      this.points.forEach((point, index) => {
        const angle = Math.PI - index * angleStep // Start from 180 degrees and move backwards to 0
        const x = ((radius + radius * Math.cos(angle)) / diameter) * 100
        const y = ((radius - radius * Math.sin(angle)) / diameter) * 100

        point.x = x
        point.y = y
      })
    },

    showInfo(point, event) {
      this.selectedPoint = point
      this.cardPosition = {
        top: event.clientY,
        left: event.clientX
      }
      this.hoveringPoint = true
    },
    hideInfo() {
      this.selectedPoint = null
    },
    onMouseLeave() {
      this.hoveringPoint = false
      this.checkHideCard()
    },
    onMouseEnterCard() {
      this.hoveringCard = true
    },
    onMouseLeaveCard() {
      this.hoveringCard = false
      this.checkHideCard()
    },
    checkHideCard() {
      setTimeout(() => {
        if (!this.hoveringCard && !this.hoveringPoint) {
          this.hideInfo()
        }
      }, 100)
    },
    toggleText() {
      this.isExpanded = !this.isExpanded
    }
  }
}
</script>

<style scoped>
.orbit-container {
  position: absolute;
  height: 100%;
  width: 100vh;
  margin-top: 5%;
}

.orbit {
  position: relative;
  transform: translate(-50%, -50%);
  border: 2px #f6f6f6 solid;
  border-bottom: 0;
  border-left: 0;
  border-right: 0;
  border-radius: 100% 100% 0 0;
  margin-top: 5%;
  z-index: 0;
}

.point {
  width: 30px;
  height: 30px;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 0;
  position: absolute;
  transform: translate(-50%, -50%);
  cursor: pointer;
}

.orbit-avatar {
  height: 100%;
  width: 100%;
  border-radius: 50%;
  z-index: 0;
}

.card {
  position: absolute;
  z-index: 999;
  height: fit-content;
  width: 400px;
  padding: 10px;
  background-color: black;
  color: #fff;
  border-radius: 4px;
  cursor: pointer;
  font-family: Inter;
  border: 1px solid gray;
}

.card-header {
  display: flex;
  padding: 5px;
}

.card-avatar {
  width: 50px;
  height: 50px;
  top: 10px;
  left: 10px;
  border-radius: 50%;
  border: 1px solid white;
}

.name-info {
  margin-left: 20px;
}

.name {
  font-size: 16px;
  font-weight: 700;
  text-align: left;
}

.position {
  font-size: 12px;
  font-weight: 400;
  text-align: left;
}

.card-body {
  padding: 5px;
}

.email {
  display: flex;
  font-size: 13px;
  font-weight: 300;
  text-align: left;
}

.email-icon {
  width: 20px;
  height: 20px;
  display: inline-block;
  margin-right: 5px;
}

.time-info {
  display: flex;
  font-size: 13px;
  font-weight: 200;
  text-align: left;
  justify-content: space-between;
  color: lightgray;
}

.more-info {
  margin-top: 10px;
}

.message-head {
  font-size: 14px;
  font-weight: 700;
  text-align: left;
}

.message-body {
  margin-top: 3px;
}

.message {
  font-size: 13px;
  font-weight: 300;
  display: flex;
  flex-direction: column;
  box-sizing: border-box;
  text-align: left;
  max-height: 18px;
  overflow: hidden;
  text-overflow: ellipsis;
  transition: height 0.3s ease-out;
  overflow-wrap: break-word;
}

.message.expanded {
  overflow: visible;
  height: auto;
  max-height: none;
}

.toggle-more {
  margin-top: 5px;
  color: #fff;
  font-size: 13px;
  font-weight: 400;
  cursor: pointer;
  display: flex;
}

.chevron-icon {
  width: 12px;
  height: 12px;
  display: inline-block;
  margin: 3px 5px;
}
</style>

