<template>
  <div class="card">
    <div class="card-header">
      <img :src="point.img" alt="avatar" />
      <div>
        <div class="name">{{ point.name }}</div>
        <div class="position">{{ point.position }}</div>
        <div class="position">{{ point.city }}</div>
      </div>
    </div>
    <div class="card-body">
      <div class="email">
        <div>Reply from Emery Wells</div>
      </div>
      <div class="more-info">
        <div>Saturday, November 4 2023 at 9:04 AM EST</div>
        <div>2 days ago</div>
      </div>
      <div>
        <div>Collaboration Opportunity for Product Expansion</div>
        <div>
          I hope this message finds you well. I wanted to reach out
          <br />
          <button @click="showMore()" v-if="!moreInfoVisible">more</button>
          <span v-if="moreInfoVisible">
            to discuss a potential collaboration opportunity that could significantly benefit both
            of our organizations.
            <button @click="showLess()">less</button>
          </span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    point: {
      type: Object,
      required: false
    }
  },

  data() {
    return {
      moreInfoVisible: false
    }
  },

  methods: {
    showMore() {
      this.moreInfoVisible = true
    },
    showLess() {
      this.moreInfoVisible = false
    }
  }
}
</script>

<style scoped>
.card {
  height: fit-content;
  width: fit-content;
  z-index: 10;
}

.card-header {
  display: flex;
}

img {
  width: 100px;
  height: 100px;
  top: 10px;
  left: 10px;
  gap: 0px;
  border-radius: 50%;
  border: 1px 0px 0px 0px;
  opacity: 0px;
}
.name {
  font-family: Inter;
  font-size: 16px;
  font-weight: 700;
  line-height: 19.36px;
  text-align: left;
}
.position {
  font-family: Inter;
  font-size: 12px;
  font-weight: 400;
  line-height: 14.52px;
  text-align: left;
}
.email {
  display: flex;
  font-family: Inter;
  font-size: 16px;
  font-weight: 300;
  line-height: 19.36px;
  text-align: left;
}
.more-info {
  display: flex;
  font-family: Inter;
  font-size: 14px;
  font-weight: 400;
  line-height: 16.94px;
  text-align: left;
}
</style>