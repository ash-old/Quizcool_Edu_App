<template lang="html">
  <div class="columns">
    <div class= "column is-one-third"></div>
    <div class= "card column is-one-third card-bkg has-text-centered">
      <h1 class="main-font"><b>Hey {{selectedUser.name}}, you scored {{totalScore + puzzleScore}} points!.</b></h1>
      <br>
      <h2 class="main-font">You answered <strong>{{currentScore.length}}</strong> questions.</h2>
      <h2 class="main-font" v-if="currentScore.length">You got <strong>{{totalScore}}</strong> correct, (that's {{percentage}}%) and got <strong>{{puzzleScore}}</strong> points for the puzzle!</h2>

      <br>
      <h2 class="main-font" v-if="highScoreString">{{highScoreString}}</h2>
      <br>
      <h2 v-if="levelMessage">{{levelMessage}}</h2>
      <br>
      <button class="button" v-if="!displayLeaderboard" v-on:click="leaderboard">Display Leaderboard</button>
      <button class="button" v-on:click="playAgain">Play Again</button>
      <div v-if="displayLeaderboard">
        <table class="table is-striped" style="width:100%">
          <tr class="">
            <th>Name</th>
            <th>High Score</th>
          </tr>
          <tr v-for="user in users">
            <td>{{user.name}}</td>
            <td>{{user.highScore}}</td>
          </tr>
        </table>
      </div>
    </div>
    <div class="column is-one-third">

    </div>
  </div>
</template>

<script>
import UserDetails from './UserDetails.vue'
import UserService from '../services/UserService.js'
import { eventBus } from '../main.js'

export default {
  name: 'results',
  data(){
    return {
      displayLeaderboard: null,
      users: null
    }
  },
  props: ['currentScore', 'highScoreString', 'selectedUser', 'puzzleScore', 'levelMessage'],
  mounted(){

    UserService.getUsers()
    .then(users => (this.users = users));


    // this.sortUsers();

  },
  computed: {
    totalScore: function() {
      return this.currentScore.reduce((sum, current) => sum + current, 0);
    },
    percentage: function() {
      return Math.round((100 / this.currentScore.length) * this.totalScore);
    },
    sortedUsers: function() {
      return this.sortUsers();
    }

  },
  methods: {
    sortUsers: function(){
    if (this.users){
        this.users.sort(function(a, b) {return b.highScore - a.highScore});
      }
    },

      leaderboard: function() {
        this.sortUsers()
        this.displayLeaderboard = true;
        return this.users;
      },

      playAgain(){
        location.reload();
      },
      sleep: function(milliseconds) {
        const date = Date.now();
        let currentDate = null;
        do {
          currentDate = Date.now();
        } while (currentDate - date < milliseconds);
      },
    }
  }
  </script>

  <style lang="css" scoped>
    .card{
      margin-top: 100px;
      margin-bottom: 400px;
    }
    .button{
      margin-left: 10px;
      margin-bottom: 15px;
    }
    .card-bkg{
      background-color: #ffe680;
    }
  </style>
