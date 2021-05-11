<template>
  <div class="date">
    <div class="date__title">
      Left until the draw
    </div>
    <div class="date__time">
      {{ currentHour }}h 
      {{ currentMinutes }}m
      {{ currentSeconds }}s
    </div>
  </div>
</template>

<script>

export default {
  name: 'Date',

  data() {
    return {
      currentHour: '',
      currentMinutes: '',
      currentSeconds: ''
    }
  },

  created() {
    this.checkTime()
  },

  methods: {
    checkTime() {
      setInterval(() => {
        const date = new Date()
        this.currentHour = 23 - date.getHours()
        this.currentMinutes = 59 - date.getMinutes()
        this.currentSeconds = 59 - date.getSeconds()
      }, 1000)
    }
  }
}

</script>

<style lang="scss" scoped>

.date {
  background-color: #F3F3F3;
  padding: 28px 68px;
  font-weight: 600;
  border-radius: 24px;
  margin-bottom: 49px;

  &__title {
    font-size: 12px;
    line-height: 24px;
    margin-bottom: 8px;
  }

  &__time {
    font-size: 56px;
    line-height: 80px;
    text-transform: uppercase;
  }
}

</style>