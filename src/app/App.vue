<template>
  <Phone msg="Demo de WEBCTR por nethexa"/>
</template>
<script lang="ts">
import { Options, Vue } from 'vue-class-component';
import Phone from './components/Phone.vue';
import { Web, UserAgent, UserAgentOptions, Inviter, SessionState, Session, Registerer, Invitation} from 'sip.js';
/*** Declarando variables **/
 //    https://12ea-137-184-206-20.ngrok.io  https://1723-137-184-206-20.ngrok.io
const server = "wss://asterisk.nethexa.com/ws";
var arreglo:number[] = [];
const remoteStream = new MediaStream();
const uri = UserAgent.makeURI("sip:500@127.0.0.1");
const transportOptions = {
   server: server
  };
var sessions:Session;
@Options({
  components: {
    Phone,
  },
})

export default class App extends Vue {
  mounted(){
     if (!Notification) {
        alert('Desktop notifications not available in your browser. Try Chromium.');
        return null;
       }

    if (Notification.permission !== 'granted'){
        Notification.requestPermission();
      }
    //recibe el evento de llamada entrante **/
    function onInvite(invitation: Invitation) {
      
      var notification = new Notification('Notification title', {
         icon: 'https://nethexa.com/wp-content/uploads/2020/05/favicon.ico',
         body: '¡Estas recibiendo una llamada!',
         //actions: [{action: 'archive', title: "Archive"}]
        });

        var bandera=false;
        notification.onclick = function(evt) {
          console.log("**********click")
          console.log(evt);
          invitation.stateChange.addListener((state: SessionState) => {
            sessions = invitation;
            switch (state) {
              case SessionState.Initial:
                break;
              case SessionState.Establishing:
                break;
              case SessionState.Established:
                setupRemoteMedia(invitation);
                break;
              case SessionState.Terminating:
                // fall through
              case SessionState.Terminated:
                console.log("terminado");
                //cleanupMedia();
                break;
              default:
                throw new Error("Unknown session state.");
              }
          });
          invitation.accept();
          bandera=true;
        };

        notification.onclose = function(evt){
          console.log("**********closet",bandera)
          if(!bandera){
            invitation.reject();
          }
          console.log(evt);
        }
        
        
      // if(confirm("Estas recibiendo una llamada, ¿quieres aceptarla?")){
      //   notifyMe();
      //   invitation.stateChange.addListener((state: SessionState) => {
      //     sessions = invitation;
      //     switch (state) {
      //       case SessionState.Initial:
      //         break;
      //       case SessionState.Establishing:
      //         break;
      //       case SessionState.Established:
      //         setupRemoteMedia(invitation);
      //         break;
      //       case SessionState.Terminating:
      //         // fall through
      //       case SessionState.Terminated:
      //         console.log("terminado");
      //         //cleanupMedia();
      //         break;
      //       default:
      //         throw new Error("Unknown session state.");
      //       }
      //   });
      //   invitation.accept();
      // }else{
      //   invitation.reject();
      // }
    }
    /******** Declarando el useragent **/
    const userAgentOptions: UserAgentOptions = {
      authorizationPassword: '500',
      authorizationUsername: '500',
      transportOptions,
      delegate: {
        onInvite
      },
      uri
    };
    const userAgent2 = new UserAgent(userAgentOptions);
    const registerer = new Registerer(userAgent2);
    /** apenas carga la pagina se registra el usuario */
    userAgent2.start().then(() => {
      registerer.register();
    });
    /* variables del dom */
    var elements = document.getElementsByClassName("keypad");
    var call = document.getElementById("connect");
    var hangup = document.getElementById("hangup");

    for (var i = 0; i < elements.length; i++) {
      elements[i].addEventListener('click',function(event){
        let button = event.target as Element ;
        if(!button){
          alert("vacio");
        }else{
          arreglo.push(Number(button.id));
          shownumber();
        }
      });
    }
    if(call){
      call.addEventListener('click', called, false);
    }
    if(hangup){
      hangup.addEventListener('click', hangups, false);
    }
    /* muestra el numero en el dom, se puede mejorar con props */
    function shownumber () {
      var div = document.getElementById('shownumber') as Element;
      cleartextnumber();
      for (let data of arreglo) {
        div.innerHTML += data;
      }
        
    }
    /* colgando llamda */
    function hangups(){
      if(sessions){
        switch(sessions.state) {
          case SessionState.Established:
            sessions.bye();
          break;
          case SessionState.Terminating:
          case SessionState.Terminated:
            // Cannot terminate a session that is already terminated
          break;
        }
      }
    }
    /* realizando llamada */
    function called(){
      if(arreglo.length<2){
        alert("Debe marcar un númer a llamar");
        return false;
      }
      let number="";
      for (let data of arreglo) {
        number += data;
      }
      console.log("comienza a realizar la llamada al siguiente texto", number);
      userAgent2.start().then(() => {
          const target = UserAgent.makeURI("sip:"+number+"@127.0.0.1");
          if (!target) {
            throw new Error("Failed to create target URI.");
          }
          const inviter = new Inviter(userAgent2, target, {
              sessionDescriptionHandlerOptions: {
                constraints: { audio: true, video: false }
              }
          });
          inviter.stateChange.addListener((state: SessionState) => {
          sessions = inviter;
            switch (state) {
              case SessionState.Establishing:
              console.log("estableciendo ****************************");
                // Session is establishing
              break;
              case SessionState.Established:
                console.log("en llamada ****************************");
                // Session has been established
                console.log(inviter);
                  setupRemoteMedia(inviter);

              break;
              case SessionState.Terminated:
                // Session has terminated
                console.log("hangup ****************************");
                cleartextnumber();
                cleararraynumber();
              break;
              default:
              break;
            }
          });
          inviter.invite()
          .then(() => {
            // INVITE sent
          })
          .catch((error: Error) => {
            // INVITE did not send
            console.log(error);
          });
      });
    }
   
  

    /**
     *  limpia el texto de numero
     * */
    function cleartextnumber(){
      var div = document.getElementById('shownumber') as Element;
      div.innerHTML="";
    }
    /* limpia el arreglo del dial */
    function cleararraynumber(){
      arreglo=[];
    }
    /* hace el stream poara el audio*/
    function setupRemoteMedia(session: Session){
      if (!session.sessionDescriptionHandler || !(session.sessionDescriptionHandler instanceof Web.SessionDescriptionHandler)) {
        throw new Error("Invalid session description handler.");
      }
      var pc = session.sessionDescriptionHandler.peerConnection;
      if (!pc?.getReceivers()){
        throw new Error("Invalid session description handler.");
      }
      pc.getReceivers().forEach(function(receiver) {
      var track = receiver.track;
      if (track) {
          remoteStream.addTrack(track);
        }
      });
      const mediaElement = document.getElementById('remoteAudio') as HTMLAudioElement;
      if (!mediaElement){
        throw new Error("Invalid session description handler.");
      }
      mediaElement.srcObject = remoteStream;
      mediaElement.play();
    }
  }

}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
