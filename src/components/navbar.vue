<template lang="pug">
  #navbar
    ul
      li
        router-link(to="/") Twitch(vue clone)
      li.drowdown
        .dropbtn Games
        .dropdownContent
          router-link(v-for="(game, index) in games" :to=" '/games/' + game.router" :key="index" :search="game.search") {{ game.name }} 
      li.search
        input.form-control(type="text" v-model="text" placeholder="Search..." @keyup="userInput")
        .result 
          .stream(v-for="(stream, index) in filterStreams")
            a(:href="stream.channel.url" target="_blank")  
              img(:src="stream.channel.logo")
              p {{ stream.channel.name }}
</template>


<script>
import axios from 'axios';

const getGames = 'https://api.twitch.tv/kraken/games/top?limit=6&client_id=9j9fga8sofpqh8t10cig2lihlms5sh';
const getStreams = 'https://api.twitch.tv/kraken/streams/?limit=100&client_id=9j9fga8sofpqh8t10cig2lihlms5sh';

export default {
  data() {
    return {
      games: [],
      streams: [],
      text: '',
    }
  },
  created() {
    axios.get(getGames)
    .then(res => {
      this.games = res.data.top.map(x => {
        return {
          name: x.game.name,
          router: x.game.name.split(/\s/g).join('-'),
          search: x.game.name.split(/\s/g).join('%20')
        };
      });
    })
    .catch(err => {
      console.error(err);
    });

    axios.get(getStreams)
    .then(res => {
      this.streams = res.data.streams;
    })
    .catch(err => {
      console.error(err);
    });
  },
  computed: {
    filterStreams() {
      return this.text == '' ? [] : this.streams.filter(obj => obj.channel.name.indexOf(this.text) !== -1);
    }
  },
  methods: {
    userInput() {
      console.log(this.filterStreams);
    }
  }
}
</script>

<style lang="sass">
#navbar
  background: #21273D
  width: 100%
  height: 60px
  margin: 0
  padding: 0
  .drowdown
    @media screen and (max-width: 768px) 
      display: none
    position: relative
    width: 120px
    &:hover
      .dropdownContent
        display: flex
        width: 1000px
    .dropbtn
      color: #B9D4F1
      cursor: pointer
    .dropdownContent
      display: none
      position: absolute
      width: 120px
      z-index: 2
      a 
        color: #B9D4F1
        font-size: 12px
        padding: 0 10px
        border: 1px solid rgba(0, 0, 0, 0.5)
        &:hover
          background: #21273D
  ul
    list-style-type: none
    width: 100%
    height: 100%
    margin: 0
    padding: 0
    .search 
      float: right
      margin: 5px 40px 5px 0
      padding-top: 10px
      @media screen and (max-width: 768px)
        width: 35%
        margin: 5px
    li
      display: inline-block
      font-size: 25px
      line-height: 60px
      margin: 0 10px
      float: left
      a
        text-decoration: none
      &:hover
        .result
          display: block
      .result
        display: none
        position: absolute
        color: #F38181
        font-size: 12px
        height: 400px
        overflow: scroll
        z-index: 2 
        .stream
          background: #21273D
          border: 1px solid rgba(0, 0, 0, 0.7)
          cursor: pointer
          &:hover
            transform: scale(1.1, 1.1)
          img 
            width: 40px
            border-radius: 50%
          p
            display: inline
            margin: 10px 15px
</style>
