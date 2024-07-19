<script setup>
import { ref } from "vue";
import { FFmpeg } from '@ffmpeg/ffmpeg';

const linkPrefix = ref('')
const link = ref('');

const isCORSProxyEnabled = ref(true)
const isPrefixEnabled = ref(true)

const audioURL = ref('')
// const audioBeforeConversionURL = ref('')

const errorMessage = ref('')

const ffmpeg = new FFmpeg()

async function fetchAudioFile(url) {
  const proxyURL = 'https://cors-anywhere.herokuapp.com/'
  let fullURL = ''

  if (isCORSProxyEnabled.value) {
    fullURL = proxyURL + url
  } else {
    fullURL = url
  }

  const response = await fetch(fullURL, {
    mode: 'cors',
    headers: {
      'origin': 'http://localhost:8080', // Adjust the origin to match your app's URL
    }
  });

  const data = await response.blob();

  if (!response.ok) {
    throw new Error(`Response status: ${response.status}`)
  }

  // audioBeforeConversionURL.value = URL.createObjectURL(data);
  return new Uint8Array(await data.arrayBuffer());
}

async function convertFile(url) {
  await ffmpeg.load()

  const fileName = url.split('/').pop();
  const inputFileName = `input.${fileName.split('.').pop()}`;
  const outputFileName = 'output.mp3';

  let buffer = null
  try {
    buffer = await fetchAudioFile(url)
  } catch (error) {
    errorMessage.value = 'Failed to fetch'
    console.error(error.message)
  }

  ffmpeg.writeFile(inputFileName, buffer).then(function(value) {
    if (value) {
      console.log('Data succesfully loaded');
    } else {
      errorMessage.value = 'Unable to load data'
      console.log('Error occured while loading the data');
      return
    }
  })

  const conversionReturnMessage = await ffmpeg.exec(['-i', inputFileName, outputFileName]);
  if (conversionReturnMessage == 0) {
    console.log('Conversion succesful');
  } else {
    errorMessage.value = 'Failed to convert'
    console.log('Conversion failed');
    return
  }
  const result = await ffmpeg.readFile(outputFileName);

  const blob = new Blob([result.buffer], { type: 'audio/mp3' });
  console.log(`Conversion complete, output blob size: ${blob.size}`);
  return URL.createObjectURL(blob);
}

async function playSound() {
  if (link.value) {
    try {
      let fullLink = ''
      if (isPrefixEnabled.value) {
        fullLink = linkPrefix.value + link.value
      } else {
        fullLink = link.value
      }

      audioURL.value = await convertFile(fullLink);
      console.log(audioURL.value);
    } catch (error) {
      errorMessage.value('Failed to convert')
      console.log('Error converting or playing the audio:', error);
    }
  } else {
    errorMessage.value = 'Please provide a valid URL'
    console.log('Please provide a valid URL.');
  }
}

function downloadFile() {
  const downloadLink = document.createElement('a');
  downloadLink.href = audioURL.value;
  downloadLink.download = 'converted_audio.mp3';
  document.body.appendChild(downloadLink);
  downloadLink.click();
  document.body.removeChild(downloadLink);
}

</script>

<template>
  <h1>Play your sound!</h1>
  <div id="form-div">
    <form @submit.prevent="playSound">
    <input v-if="isPrefixEnabled" v-model="linkPrefix" placeholder="https://example.com/">
    <input v-model="link" :placeholder="isPrefixEnabled ? 'audio/example.wma' : 'Enter audio file URL'">
    <button type="submit">Convert and Play!</button>
    <div id="checkboxes">
      <input type="checkbox" v-model="isCORSProxyEnabled"> Use CORS proxy
      <input type="checkbox" v-model="isPrefixEnabled"> Use a prefix
    </div>
    <div id="error-message" v-if="errorMessage != ''">
      <p>{{ errorMessage }}</p>
    </div>
    <audio controls :src="audioURL" />
  </form>
  </div>
  <div v-if="audioURL">
    <button @click="downloadFile">Download Converted File</button>
  </div>
</template>


<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

#form-div {
  width: fit-content;
  margin: auto;
}

form {
  display: flex;
  flex-direction: column;
}

input, button, audio {
  padding: 5px;
  margin: 5px;
}

#checkboxes {
  font-size: 15px;
}

#error-message {
  color: red;
}

</style>
