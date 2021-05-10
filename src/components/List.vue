<template>
  <div class="list">
    <div class="list__titles">
      <span>Participant</span>
      <span>work email</span>
      <span>signed up</span>
    </div>

    <div
      v-for="user in userInfo"
      :key="user.id"
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
      All users loaded
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
      userInfo: ''
    }
  },

  created () {
    this.getInfo()
  },

  methods: {
    async getInfo() {
      const { data: { results } } = await axios.get( 'https://randomuser.me/api/?results=50&?inc=picture,name,email,registered&noinfo')
      this.userInfo = results.map(el => {
        // let time = new Date(el.registered.date)
        // time = time.getDate()

        // let rightNow = new Date()
        // rightNow = rightNow.getDate()

        return {
          id: el.index,
          image: el.picture.thumbnail,
          firstName: el.name.first,
          lastName: el.name.last,
          email: el.email,
          time: el.registered.date
        }
      })
    }
  }
}

</script>

<style lang="scss" scoped>

.list {
  margin: 0 auto;

  &__titles {
    display: flex;
    color: #808080;
    font-size: 12px;
    line-height: 24px;
    font-weight: 400;
  }

  &__participants {
    display: grid;
    grid-template-columns: 15% 30% 40% 15%;
    grid-template-rows: 61px auto;
  }

  &__last-message {
    display: flex;
    justify-content: center;
    background-color: #EDF9F2;
    color: #219653;
    border-radius: 67px;
    padding: 26px 0;
  }
}

</style>
