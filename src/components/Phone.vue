<template>
  <div class="phone">
    <h1>{{ msg }}</h1>
    <h2>Demo: Audio Call - Outbound</h2>
      <div>
        <ol>
          <li>Place call to destination</li>
          <li>Hangup call</li>
        </ol>
      </div>
      <ol>
        <li><button id="connect">Connect</button> with <code><span id="server"></span></code></li>
        <li><button id="hangup" >Hangup Call</button></li>
      </ol>
      <audio id="remoteAudio" src="" controls >
          <p>Your browser doesn't support HTML5 audio.</p>
      </audio>
      <div id="shownumber"></div>
      <div class="dtmf">Marcar número: <span id="dtmf"></span></div>
      <div>
        <div>
          <button class="keypad" id="1" >1</button>
          <button class="keypad" id="2" >2</button>
          <button class="keypad" id="3" >3</button>
        </div>
        <div>
          <button class="keypad" id="4" >4</button>
          <button class="keypad" id="5" >5</button>
          <button class="keypad" id="6" >6</button>
        </div>
        <div>
          <button class="keypad" id="7" >7</button>
          <button class="keypad" id="8" >8</button>
          <button class="keypad" id="9" >9</button>
        </div>
        <div>
          <button class="keypad" id="*" >*</button>
          <button class="keypad" id="0" >0</button>
          <button class="keypad" id="#" >#</button>
        </div>
      </div>

    
  </div>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component';

@Options({
  props: {
    msg: String
  }
})
export default class Phone extends Vue {
  msg!: string
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
audio {
  margin: 8px;
}

body {
  display: flex;
  flex-direction: column;
  justify-content: center;
}

button {
  width: 100px;
  margin: 8px;
}

input {
  margin: 8px;
}

.dtmf {
  margin: 8px;
}
</style>
