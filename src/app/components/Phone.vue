<template>
    
    <div class="softphone">
      <div class="header">
        <span>Grupo Nethexa</span>
        <span>{{msg}}</span>
      </div>
      <div class="dialscreen header">
        <div id="timespeak" class="timespeak"></div>
        <input type="text" id="number" />
        <audio id="remoteAudio" src="" controls style="display: none;">
            <p>Your browser doesn't support HTML5 audio.</p>
        </audio>
        <div id="shownumber" class="shownumber"></div>
      </div>
      
      <ol>
        <li class="keypad">1</li>
        <li class="keypad">2</li>
        <li class="keypad">3</li>
        <li class="keypad">4</li>
        <li class="keypad">5</li>
        <li class="keypad">6</li>
        <li class="keypad">7</li>
        <li class="keypad">8</li>
        <li class="keypad">9</li>
        <li class="keypad">*</li>
        <li class="keypad">0</li>
        <li class="keypad">#</li>
        <li style="background-color: #323232 !important;"><i class="fas fa-rss"></i></li>
        <li id="connect" style="background-color: #009900 !important;" ><i  class="fas fa-phone"></i></li>
        <li id="hangup" style="background-color: #B04417 !important;"><i  class="fas fa-phone-slash"></i></li>
      </ol>
    </div>
    <!-- /.container -->
   
      

    
  
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component';
import '@fortawesome/fontawesome-free/css/all.css'
import '@fortawesome/fontawesome-free/js/all.js'

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
<style scoped src="../assets/css/phone.css"><style> 
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
