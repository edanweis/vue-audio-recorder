<style lang="scss">
  .ar {
    width: 100%;
    font-family: 'Roboto', sans-serif;
    // border-radius: 16px;
    background-color: #FAFAFA;
    // box-shadow: 0 4px 18px 0 rgba(0,0,0,0.17);
    position: relative;
    box-sizing: content-box;
    display: flex;
    align-items: start;
    // justify-content: start;
    flex-wrap: wrap;
    justify-content: space-between;

    &-upload-text{
      pointer-events: all !important;
      cursor: pointer;
      color: black;
      border-radius: 4px;
      border: 1px solid grey;
      font-weight: 500;
      padding: 0px 8px;
      margin: 0px 4px;
      background-color: rgba(245,245,245,0.2);

      &:hover {
        background-color: rgba(245,245,245,0.2);        
      }
    }



    &-content {
      width: 100%;
      padding: 16px;
      display: flex;
      flex-direction: row;
      align-items: stretch;
      justify-content: start;
    }

    &-records {
      // height: 138px;
      padding-top: 1px;
      overflow-y: visible;
      flex-grow: 5;
      // margin-bottom: 20px;
      align-items: end;
      justify-content: end;

      &__record {
        // width: 320px ;
        height: 45px;
        padding: 10px 20px;
        margin: 10px auto;
        line-height: 45px;
        display: flex;
        justify-content: space-between;
        // border-bottom: 1px solid #E8E8E8;
        background-color: #FFFFFF;
        border-radius: 7px;
        position: relative;
        cursor: pointer;
        align-items: center;

        &--selected {
          // border: 1px solid #E8E8E8;
          // border-radius: 24px;
          background-color: #FFFFFF;
          // margin-top: 1px;
          padding-right: 34px;
          cursor: default;


        }

        &--selected .ar-player{
          opacity: 1;
        }

      }
    }

    &-recorder {
      position: relative;
      display: flex;
      flex-direction: row;
      align-items: center;
      flex-wrap: no-wrap;

      &__duration {
        transition: opacity 120ms ease-out;
        color: #AEAEAE;
        font-size: 32px;
        font-weight: 500;
        margin-top: 20px;
        margin-bottom: 16px;
        margin-left: 20px;
        margin-right: 20px;
        opacity: 1;

        &--hidden{
          opacity: 0;
        }

      }

      &__stop {
        // position: relative;
        top: 10px;
        margin-left: 10px;
        opacity: 1;

        &--hidden {
          opacity: 0;
        }

      }

      &__time-limit {
        position: absolute;
        color: #AEAEAE;
        font-size: 12px;
        top: 128px;
      }

      &__records-limit {
        position: absolute;
        color: #AEAEAE;
        font-size: 13px;
        top: 78px;
      }
    }

    &-spinner {
      display: flex;
      height: 30px;
      position: absolute;
      left: 0;
      right: 0;
      top: 0;
      bottom: 0;
      margin: auto;
      width: 144px;
      z-index: 10;

      &__dot {
        display: block;
        margin: 0 8px;
        border-radius: 50%;
        width: 30px;
        height: 30px;
        // background: #05CBCD;
        animation-name: blink;
        animation-duration: 1.4s;
        animation-iteration-count: infinite;
        animation-fill-mode: both;

        &:nth-child(2) { animation-delay: .2s; }

        &:nth-child(3) { animation-delay: .4s; }

        @keyframes blink {
          0%    { opacity: .2; }
          20%   { opacity: 1;  }
          100%  { opacity: .2; }
        }
      }
    }


    &__text {
      color: rgba(84,84,84,0.5);
      font-size: 16px;
      white-space: nowrap;

    }



    &__clip {
      border-radius: 50%;
      background: rgba(0,0,0,0.66);
      width: 30px;
      height: 30px;
      color: white;
      font-weight: 600;
      font-size: 13px;
      white-space: nowrap;
      display: flex;
      align-items: center;
      justify-content: center;
      pading: 5px;
    }

    &__blur {
      // filter: blur(2px);
      // opacity: 0.7;
    }

    &__overlay {
      position: absolute;
      width: 100%;
      height: 100%;
      z-index: 10;
    }

    &__upload-status {
      text-align: center;
      font-size: 10px;
      padding: 2px;
      letter-spacing: 1px;
      position: absolute;
      bottom: 0;

      &--success {
        color: green;
      }

      &--fail {
        color: red;
      }
    }

    &__rm {
      cursor: pointer;
      position: absolute;
      width: 6px;
      height: 6px;
      padding: 0 10px;
      line-height: 6px;
      margin: auto 0px auto 0px;
      right: 10px;
      bottom: 0;
      top: 0;
      font-size: 30px !important;
      color: rgb(244, 120, 90);
    }

    &__downloader,
    

    &__downloader {
      right: 115px;
    }

    
  }

  @import '../scss/icons';
</style>

<template>
  <div class="ar" :style="{'background-color': backgroundColor+' !important'}">
    {{$eventBus.uploadStatus}}
    <div class="ar__overlay" v-if="isUploading"></div>
    <div class="ar-spinner" v-if="isUploading">
      <div class="ar-spinner__dot"></div>
      <div class="ar-spinner__dot"></div>
      <div class="ar-spinner__dot"></div>
    </div>


    <div class="ar-content" :class="{'ar__blur': isUploading}">
      <div class="ar-recorder">
        <icon-button
          class="ar-icon ar-icon__lg"
          :name="iconButtonType"
          :class="{
            'ar-icon--rec': isRecording,
            'ar-icon--pulse': isRecording && volume > 0.02
          }"
          @click.native="toggleRecorder"/>
        <icon-button
          :class="['ar-icon ar-icon__sm ar-recorder__stop', {'ar-recorder__stop--hidden':!isRecording}]"
          name="stop"
          @click.native="stopRecorder"/>
      <div :class="['ar-recorder__duration', {'ar-recorder__duration--hidden': !isRecording} ]">{{recordedTime}}</div>
      </div>



      <!-- <div class="ar-recorder__records-limit" v-if="attempts">Attempts: {{attemptsLeft}}/{{attempts}}</div> -->

      
      <!-- <div class="ar-recorder__time-limit" v-if="time">Record duration is limited: {{time}}m</div> -->

     

        <div class="ar-records">
      <div
        class="ar-records__record"
        :class="{'ar-records__record--selected': record.id === selected.id}"
        :key="record.id"
        v-for="(record, idx) in recordList"
        @click="choiceRecord(record)">
          <div class="ar__clip">{{idx + 1}}</div>
          <audio-player style="zoom: 0.7; margin: 0 30px 0 30px;" :record="selected"/>
          <div class="ar__text" v-if="record.id !== selected.id">{{record.duration}}</div>
          <div
            class="ar__rm"
            v-if="(record.id === selected.id) && !isUploading"
            @click="removeRecord(idx)">&times;</div>

          <downloader
            v-if="record.id === selected.id && showDownloadButton"
            class="ar__downloader"
            :record="record"
            :filename="filename"/>

          <uploader
            v-if="record.id === selected.id && showUploadButton"
            class=""
            :record="record"
            :filename="filename"
            :headers="headers"
            :upload-url="uploadUrl">
              <template v-slot:upload>
                <slot name="upload"></slot>
              </template>
            </uploader>
      </div>
    </div>

  </div>
  </div>
</template>

<script>
  import AudioPlayer from './player'
  import Downloader  from './downloader'
  import IconButton  from './icon-button'
  import Recorder    from '@/lib/recorder'
  import Uploader    from './uploader'
  import UploaderPropsMixin from '@/mixins/uploader-props'
  import { convertTimeMMSS }  from '@/lib/utils'

  export default {
    mixins: [UploaderPropsMixin],
    props: {
      attempts : { type: Number },
      time     : { type: Number },

      backgroundColor: { type: String},

      bitRate    : { type: Number, default: 128   },
      sampleRate : { type: Number, default: 44100 },

      showDownloadButton : { type: Boolean, default: false },
      showUploadButton   : { type: Boolean, default: true },

      micFailed        : { type: Function },
      beforeRecording  : { type: Function },
      pauseRecording   : { type: Function },
      afterRecording   : { type: Function },
      failedUpload     : { type: Function },
      beforeUpload     : { type: Function },
      successfulUpload : { type: Function },
      selectRecord     : { type: Function },

      uploadStatus        : { type: Object}
    },
    data () {
      return {
        isUploading   : false,
        recorder      : this._initRecorder(),
        recordList    : [],
        selected      : {},
      }
    },
    components: {
      AudioPlayer,
      Downloader,
      IconButton,
      Uploader
    },
    mounted () {
      this.$eventBus.$on('start-upload', (id) => {
        this.isUploading = true
        this.beforeUpload && this.beforeUpload('before upload', id)
      })

      this.$eventBus.$on('end-upload', (msg) => {
        console.log('ended upload')
        this.isUploading = false

        if (msg.status === 'success') {
          this.successfulUpload && this.successfulUpload(msg.response)
          setTimeout(e=>{
            this.removeRecord(this.recordList.findIndex(e=>e.index==msg.id))
          }, 1000);
        } else {
          this.failedUpload && this.failedUpload(msg.response)
        }
      })
    },
    beforeDestroy () {
      this.stopRecorder()
    },
    watch: {

      selected(val){
        this.$emit('selected', val)
      },

      uploadStatus(val){
        console.log('you externally stopped')
        if(val && val.status=='success'){
          this.$eventBus.$emit('end-upload', { status: 'success', response: 'success', id: val.id  })
        }
        if(val && val.status=='fail'){
          this.$eventBus.$emit('end-upload', { status: 'fail', response: 'fail' })
        } else{
          this.$eventBus.$emit('end-upload', { status: 'fail', response: 'fail' })
        }
      }
    },
    methods: {
      toggleRecorder () {
        if (this.attempts && this.recorder.records.length >= this.attempts) {
          return
        }

        if (!this.isRecording || (this.isRecording && this.isPause)) {
          this.recorder.start()
        } else {
          this.recorder.pause()
        }
      },
      stopRecorder () {
        if (!this.isRecording) {
          return
        }

        this.recorder.stop()
        this.recordList = this.recorder.recordList()
        if(this.recordList.length==1){
            this.selected = this.recorder.recordList()[0]
        } else if(this.recordList.length>1) {
          this.selected = this.recorder.recordList()[this.recordList.length-1]
        }
      },
      removeRecord (idx) {
        this.recordList.splice(idx, 1)
        this.$set(this.selected, 'url', null)
        this.$eventBus.$emit('remove-record')
      },
      choiceRecord (record) {
        if (this.selected === record) {
          return
        }
        this.selected = record
        this.selectRecord && this.selectRecord(record)
      },
      _initRecorder () {
        return new Recorder({
          beforeRecording : this.beforeRecording,
          afterRecording  : this.afterRecording,
          pauseRecording  : this.pauseRecording,
          micFailed       : this.micFailed,
          bitRate         : this.bitRate,
          sampleRate      : this.sampleRate,
          format          : this.format
        })
      }
    },
    computed: {
      attemptsLeft () {
        return this.attempts - this.recordList.length
      },
      iconButtonType () {
        return this.isRecording && this.isPause ? 'mic' : this.isRecording ? 'pause' : 'mic'
      },
      isPause () {
        return this.recorder.isPause
      },
      isRecording () {
        return this.recorder.isRecording
      },
      recordedTime () {
        if (this.time && this.recorder.duration >= this.time * 60) {
          this.stopRecorder()
        }
        return convertTimeMMSS(this.recorder.duration)
      },
      volume () {
        return parseFloat(this.recorder.volume)
      }
    }
  }
</script>

