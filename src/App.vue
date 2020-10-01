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
          <a :href="`http://midnightsnacks.fm/show/${show.number}/${show.date}`" @click="linkClick" target="_blank">
            Show #{{String(show.number).padStart(padNum, '0')}}
          </a>
        </span>
        <span class="date">{{ show.date }}</span>
      </div>
      <div class="selectBorder" style=""></div>
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
  background-color:#F1F3F4;
  z-index: 9999;
}
.show{
  padding: 10px 35px; 
  position: relative;
  background-color: #FFF;
  /* margin: 3px 0px; */
  border-bottom: 2px solid #CCC;
  box-sizing: border-box;
  }
  .show a {
    text-decoration: none;
    font-weight: bold;
    padding: 10px 10px 10px 40px;
    display: inline-block;
    box-sizing: border-box;
    color: rgb(28, 126, 218);
    position: absolute;
    top: 0px;
    left: 0px;
    border-right: 2px solid #AAA;
  }
  .show a:hover{
    background-color:#DDD; 
    color: rgb(28, 180, 218);
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
    background-color:#EFEFEF; 
    /* box-shadow: 0px 0px 2px; */
    font-weight: bold;
    }

  .current{
    font-weight: bold;
    position: sticky;
    background-color:#EEE; 
    }
    .current .selectBorder{
      display: block;
    }

    .current a{
      z-index: 9999;
      top: -1px;
      padding: 8px 10px 8px 40px;
      border-top: #AAA solid;
      border-bottom: #AAA solid;
      box-sizing: border-box;
    }

  .selectBorder{
    display: none;
    position: absolute; 
    top: 0px; 
    left: 0px; 
    box-sizing: border-box; 
    width: 100%; 
    height: 100%; 
    border: #AAA 2px solid;
    border-right: none;
    border-left: none;
  }
</style>
