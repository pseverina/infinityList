<template>
  <div class="list">
    <div class="list__titles">
      <span>Participant</span>
      <span>work email</span>
      <span>signed up</span>
    </div>

    <div
      v-for="user in userInfo"
      :key="user.index"
      class="list__participants"
    >
      <img :src="user.image" alt="">
      <span>{{ user.firstName }} {{ user.lastName }}</span>
      <span>{{ user.email }}</span>
      <span>{{ user.time }}</span>
    </div>

    <div
      v-if="areUsersLoaded"
      class="list__last-message"
    >
      <img src="../assets/check_mark.svg" class="list__last-message-img" alt=''>All users loaded
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'List',

  data() {
    return {
      areUsersLoaded: false,
      userInfo: '',
      users: '',
      interval: '',
      time: '' 
    }
  },

  mounted () {
    this.$nextTick(() => {
      window.addEventListener('scroll', this.onScroll)
      this.onScroll()
    });
  },

  created () {
    this.getInfo()
  },

  beforeDestroy() {
    clearInterval(this.interval)
    window.removeEventListener('scroll', this.onScroll);
  },

  methods: {
    onScroll() {
      const bottomOfWindow = (window.innerHeight + window.scrollY) >= document.body.offsetHeight;
      (bottomOfWindow && this.userInfo?.length === 50) ? this.areUsersLoaded = true : this.areUsersLoaded = false

      // const list = document.querySelector('.list')
      // const windowHeight = window.innerHeight
      // if (list.offsetHeight > windowHeight) {
      //   list.style.maxHeight = windowHeight - 295 + 'px'
      // }
    },

    async getInfo() {
      let { data: { results } } = await axios.get( 'https://randomuser.me/api/?results=50&?inc=picture,name,email,registered&noinfo')

      // sorting by time
      const participants = results.sort((a, b) => b.registered.date > a.registered.date ? 1 : -1)
      // first call
      this.sortUsers(participants)
      
      // update info each minute
      this.interval = setInterval(() => {
        this.sortUsers(participants)
      }, 60000)
    },

    sortUsers(users) {
      this.userInfo = users.map(el => {
        const date = +new Date()
        const rightNow = new Date(date)
        const userTime = new Date(el.registered.date)

        const currentYear = rightNow.getFullYear() === userTime.getFullYear()
        const currentDate = rightNow.getDate() === userTime.getDate()
        const currentHour = rightNow.getHours() === userTime.getHours()
        const currentMinutes = rightNow.getMinutes() === userTime.getMinutes()
        const yesterday = currentYear && rightNow.getDate() - userTime.getDate() === 1

        if (currentYear) {
          if (currentDate && currentHour) {
            (currentMinutes) ? this.time = 'just now' : this.time = `${59 - userTime.getMinutes()}m ago`
          } else if (currentDate && !currentHour) {
            this.time = `${23 - userTime.getHours()}h ago`
          } else if (yesterday) {
            this.time = 'yesterday'
          } else {
            this.time = `${userTime.getDate()} ${userTime.toLocaleString('default', { month: 'short' })}`
          }
        } else {
          this.time = `${userTime.getDate()} ${userTime.toLocaleString('default', { month: 'short' })} ${userTime.getFullYear()}`
        }

        return {
          id: el.index,
          image: el.picture.thumbnail,
          firstName: el.name.first,
          lastName: el.name.last,
          email: el.email,
          time: this.time
        }
      })
    }
  }
}

</script>

<style lang="scss" scoped>

.list {
  width: 90%;
  margin: 0 auto;
  overflow: hidden;

  &__titles {
    display: grid;
    grid-template-columns: 15% 30% 40% 15%;
    grid-template-rows: 61px auto;
    color: #808080;
    font-size: 12px;
    line-height: 24px;
    font-weight: 400;

    & span:first-child {
      grid-column-start: 2;
    }
  }

  &__participants {
    display: grid;
    grid-template-columns: 15% 30% 40% 15%;
    grid-template-rows: 61px auto;

    & img {
      border-radius: 24px;
    }
  }

  &__last-message {
    display: flex;
    justify-content: center;
    background-color: #EDF9F2;
    color: #219653;
    border-radius: 67px;
    padding: 26px 0;

    &-img {
      margin-right: 8px;
    }
  }
}

</style>
