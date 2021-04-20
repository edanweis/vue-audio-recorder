<style lang="scss">
  .toggle {
    cursor: pointer;
    margin: 20px;
  }
</style>

<template>
  <div class="row">
    <div class="toggle" @click="setStatus">status</div>

    <audio-recorder v-if="showRecorder"
      upload-url="some url"
      filename="ninja"
      format="wav"
      :attempts="3"
      :time="2"
      :headers="headers"
      :before-recording="callback"
      :pause-recording="callback"
      :after-recording="callback"
      :select-record="callback"
      :before-upload="callback"
      :successful-upload="callback"
      :failed-upload="callback"
      :bit-rate="192"
      :sample-rate="48000"
      :uploadStatus="uploadStatus"
      />

    <audio-player :src="mp3" v-if="!showRecorder"/>
  </div>
</template>

<script>
  export default {
    name: 'app',
    data () {
      return {
        uploadStatus: null,
        mp3: '/demo/example.mp3',
        showRecorder: true,
        headers: {
          'X-Custom-Header': 'some data'
        }
      }
    },
    methods: {
      callback (msg) {
        console.debug('Event: ', msg)
      },
      setStatus () {
        // console.log('success')
        this.uploadStatus = 'success'
        setTimeout(e=>{
          this.uploadStatus = null
        }, 300);
      }
    }
  }
</script>
