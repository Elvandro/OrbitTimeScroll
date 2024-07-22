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
        @mouseover="showInfo(index)"
        @mouseleave="hideInfo"
      >
        <img :src="point.img" alt="avatar" />

        <div v-if="infoVisible === index" class="info-popup">
          <OrbitPointInfo :point="point" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import OrbitPointInfo from './OrbitPointInfo.vue'

export default {
  components: { OrbitPointInfo },
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
      infoVisible: null
    }
  },

  computed: {
    calcOrbitDiameter() {
      return (100 / this.noOfOrbits) * (this.noOfOrbits + 1 - this.orbitIndex)
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

    showInfo(index) {
      this.infoVisible = index
    },
    hideInfo() {
      this.infoVisible = null
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
  z-index: 1;
}

.point {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1;
  position: absolute;
  transform: translate(-50%, -50%);
}

img {
  height: 100%;
  width: 100%;
  border-radius: 50%;
  z-index: 1;
}

.info-popup {
  position: absolute;
  top: 0;
  left: 100%;
  margin-left: 10px;
  padding: 10px;
  background-color: black;
  color: #fff;
  border-radius: 4px;
  z-index: 10;
  white-space: nowrap;
  pointer-events: none;
}
</style>