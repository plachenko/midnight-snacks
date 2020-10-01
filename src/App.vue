<template>
  <h1>The <a style="text-decoration: none; color: rgb(28, 126, 218); font-weight: bold" target="_blank" href="http://midnightsnacks.fm">Midnight Snacks</a> archive</h1>
  <audio :src="currentShow.url" controls autoplay @ended="ended"></audio>

  <div>
    <div 
      class="show" 
      v-for="(show, idx) in shows.slice().reverse()" 
      :key="idx" 
      :class="currentShow.number == (shows.length - idx) + 10 ? 'current' : ''" 
      @click="play(show)"
      >
      <div>
        <span class="number">
          <a :href="`http://midnightsnacks.fm/show/${show.number}/${show.date}`" target="_blank">
            Show #{{String(show.number).padStart(padNum, '0')}}
          </a>
        </span>
        <span class="date">{{ show.date }}</span>
      </div>
    </div>
  </div>

</template>

<script>
import moment from 'moment'

export default {
  name: 'App',
  data() {
    return{
      shows: [],
      currentShow: {}
    }
  },
  computed:{
    padNum(){
      return this.shows.length.toString().length;
    },
  },
  mounted() {
    const today = new moment();
    const first = new moment(new Date("03/30/2005"));
    const diff = today.diff(first, 'week');
    const baseURL = 'http://midnightsnacks.fm/archive/Midnight_Snacks';

    for(let showNum = 1; showNum <= diff; showNum++){
      const showDate = showNum == 1 ? first : first.add(1, 'week');
      const show = {
        number: showNum,
        date: showDate.format('MM.DD.YY')
      }

      if(showNum <= 400){
        // Before show 401 format url: http://midnightsnacks.fm/archive/Midnight_Snacks-[date].mp3
        show.url = `${baseURL}-${show.date}.mp3`;
      } else {
        // After format url: http://midnightsnacks.fm/archive/Midnight_Snacks-[number]_[date].mp3
        show.url = `${baseURL}-${showNum}_${show.date}.mp3`;
      }

      // First playable show is the 11th show
      if(showNum >= 11){
        this.shows.push(show);
      }
    }
  },
  methods: {
    play(show){
      this.currentShow = show;
      console.log()
    },
    ended(){
      const showIdx = this.currentShow.number - 11; 
      this.currentShow = this.shows[showIdx + 1];
    }
  }
}
</script>

<style>
body, html{
  margin: 0px;
  padding: 0px;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
audio{
  width: 100%;
  position: sticky; 
  top: 0px; 
  background-color:#FFF
}
.show{
  padding: 10px 35px; 
  background-color:#CCC; 
  margin: 3px 0px;
  box-sizing: border-box;
  }
  .show a {
    text-decoration: none;
    font-weight: bold;
    padding: 0px 10px;
    display: inline-block;
    box-sizing: border-box;
    color: rgb(28, 126, 218);
  }
  .show a:hover{
    background-color: #FFF;
  }
  .show .number {
    display: block;
    float: left;
  }
  .show .date {
    display: block;
    text-align: right;
  }

  .show:hover{
    cursor: pointer;
    background-color:#333; 
    color: #FFF;
    }

  .current{
    border: #F00 3px solid;
  }
</style>
