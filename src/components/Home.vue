<template>
    <div class="container home">
      <div class="columns">
        <div class="column is-half">
          <img class="home-avatar" src="../assets/img/girl.svg" />

          <div>
            <h1 v-if="userName" class="home-user">{{ userName }}</h1>
            <p v-else></p>
          </div>

          <NeedBar 
            v-for="need in needs" 
            :title="need.name"
            :key="need.name"
            :status="need.status"
            class="column is-one-third-mobile is-two-thirds-tablet is-half-desktop"
            v-on:switch_overview="switchOverview"></NeedBar>

        </div>

        <div class="column is-half flex">

          <div class="entry">
            <router-link :to="{ name: 'Entry' }" class="btn-entry">
              <button class="entry__btn">
                <img v-if="userStatus == 'empty'" class="home-icon--entry" src="../assets/img/add.svg" />
                <span v-if="userStatus == 'empty'" class="entry-copy">Add entry</span>
                <span v-else class="entry-copy">Update entry</span>
              </button>
            </router-link>
          </div>

          <div class="overview">
            <h2>Weekly Overview</h2>
            <weekly-chart v-if="showOverview == 'Sleep'" :chart-data="sleepEntries"></weekly-chart>
            <weekly-overview v-if="showOverview == 'Social Life'" :entries="socialEntries"></weekly-overview>
          </div>
        </div>

      </div>
    </div>

</template>

<script>
import NeedBar from '../components/NeedBar';
import WeeklyChart from '../components/WeeklyChart';
import WeeklyOverview from '../components/WeeklyOverview';

export default {
    name: 'home',
    components: {
        NeedBar,
        WeeklyChart,
        WeeklyOverview
  },
  beforeCreate(){
    this.$store.dispatch('getUserData');
  },
  data (){
      return {
          showOverview: 'Sleep',
      };
  },
  computed: {
    needs(){
      return this.$store.state.needs;
    },
    userName(){
      return this.$store.state.users[0] ? this.$store.state.users[0].firstName : '';
    },
    userStatus(){
      return this.$store.state.users[0] ? this.$store.state.users[0].status : '';
    },
    sleepEntries(){
      if (this.$store.state.entries) {
        return {
          labels: this.$store.state.weekdays,
          datasets: [
            {
              label: 'Hrs slept',
              backgroundColor: this.$store.state.chartColors,
              data: this.$store.state.weeklyEntriesSleep
            }
          ]
        };
      }
    },
    socialEntries(){
      return this.$store.state.entries[1].social ? this.$store.state.entries[1].social : '';
    },
    entries(){
      return this.$store.state.entries ? this.$store.state.entries : '';
    }
  },
  methods: {
    switchOverview(overview) {
      this.showOverview = overview;
    }
  }
}
</script>

<style lang="sass">
.main-container 
  position: relative
  height: 100%

  .home-user
    font-size: 1.5em
    font-weight: bold

  .home-avatar
    height: 200px

  .flex
    display: flex
    flex-direction: column

  .entry
    height: 15%
    display: flex
    justify-content: center
    align-items: center
    margin-bottom: 30px

    &__btn
      display: flex
      height: 50px
      width: 250px
      padding: 10px
      cursor: pointer

      img 
        height: 100%
        width: 50%

      span
        width: 50%
        margin: auto

  .overview
    height: 85%

    h2
      margin-bottom: 20px
      font-weight: bold


</style>
