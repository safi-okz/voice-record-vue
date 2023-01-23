<script setup>
import { ref, onMounted } from 'vue';

const transcript = ref('');
const isRecording = ref(false);

const Recognition = window.SpeechRecognition || window.webkitSpeechRecognition;
const sr = new Recognition();

onMounted(() => {
  sr.continuous = true;
  sr.interimResults = true;

  sr.onstart = () => {
      console.log('SR Started');
      isRecording.value = true;
  };

  sr.onend = () => {
    console.log('SR Stopped');
    isRecording.value = false;
  }

  sr.onresult = (evt) => {
      console.log(evt);
      for(let i = 0; i < evt.results.length; i++){
        const result = evt.results[i];

        if(result.isFinal) checkForCommand(result);
      }

      const t = Array.from(evt.results).map(result => result[0]).map(result => result.transcript).join('');
      transcript.value = t;
      console.log('trans ', transcript.value);
  }

});

const checkForCommand = (result) => {
  const t = result[0].transcript;
  if(t.includes('stop recording')){
    sr.stop();
  } else if(t.includes('what is the time') || t.includes('what\s the time')){
    sr.stop();
    alert(new Date().toLocaleTimeString());
    sr.start();
  }
}

const onToggleMic = () => {
  if(isRecording.value) {
    sr.stop();
  } else {
    sr.start();
  }
}
</script>

<template>
  <div class="app">
    <button :class="`mic`" @click="onToggleMic">Record</button>
    <div class="transcript" >
{{ transcirpt }}
    </div>
  </div>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Fira sans', sans-serif;
}

body{
  background: #261238;
  color: #fff;
}
</style>
