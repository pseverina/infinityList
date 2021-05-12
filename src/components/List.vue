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
      users: ''    }
  },

  beforeMount() {
    this.getInfo()
  },

  mounted () {
    this.$nextTick(() => {
      window.addEventListener('scroll', this.onScroll)
      this.onScroll()
    });
  },

  beforeDestroy() {
    window.removeEventListener('scroll', this.onScroll);
  },

  methods: {
    onScroll() {
       window.onscroll = () => {
        const bottomOfWindow = (window.innerHeight + window.scrollY) >= document.body.offsetHeight

         if (bottomOfWindow && this.userInfo?.length === 50) {
           this.areUsersLoaded = true
         } else {
            setTimeout(() => {
              this.getInfo()
            }, 1000);
           this.areUsersLoaded = false
         }
       }
    },

    async getInfo() {
      let { data: { results } } = await axios.get( 'https://randomuser.me/api/?results=20&?inc=picture,name,email,registered&noinfo')

      // sorting by time
      const participants = results.sort((a, b) => b.registered.date > a.registered.date ? 1 : -1)
      // fist call
      this.sortUsers(participants)
      
      // update info each minute
      setInterval(() => {
        this.sortUsers(participants)
      }, 60000)
    },

    sortUsers(users) {
      const rightNow = new Date()
      const currentYear = rightNow.getFullYear()
      const currentDate = rightNow.getDate()
      const currentHour = rightNow.getHours()
      const currentMinutes = rightNow.getMinutes()

      // adjusting array
      if(this.userInfo?.length < 50) {
        users = users.map(el => {
          let time
          const userTime = new Date(el.registered.date)

          if (userTime.getFullYear() !== currentYear) {
            // not in this year
            time = `${userTime.getDate()} ${userTime.toLocaleString('default', { month: 'short' })} ${userTime.getFullYear()}`
          } else {
            // today
            if (currentDate === userTime.getDate()) {
              if (currentHour === userTime.getHours()){
                if (currentMinutes === userTime.getMinutes()) {
                  time = 'just now'
                } else {
                  // recently
                  time = `${59 - userTime.getMinutes()}m ago`
                }
              } else {
                // during the day
                time = `${23 - userTime.getHours()}h ago`
              }
            } else if(currentDate - userTime.getDate() === 1) {
              time = 'yesterday'
            } else {
              // not recently , but in this year
              time = `${userTime.getDate()} ${userTime.toLocaleString('default', { month: 'short' })}`
            }
          }

          return {
            id: el.index,
            image: el.picture.thumbnail,
            firstName: el.name.first,
            lastName: el.name.last,
            email: el.email,
            time
          }
        });
        
        // очень костыльная проверка на кол-во пользователей
        (!this.userInfo?.length) ? this.userInfo = users : this.userInfo.push(...users)
        if (this.userInfo?.length > 50) {
          do {
            this.userInfo.pop()
          } while (this.userInfo?.length > 50)
        }
      }
    }
  }
}

</script>

<style lang="scss" scoped>

.list {
  display: table;
  width: 90%;
  margin: 0 auto;

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
