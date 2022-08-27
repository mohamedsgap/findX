<template>
  <div class="container">
    <h1 class="title">FindX, A Vue.JS app based on NLP</h1>
    <h4 v-if="showHint">Please allow mircophone!</h4>
    <div class="input">
      <input v-model="videoUrl" placeholder="enter youtube video url" />
      <!-- <video v-if="videoUrl.trim().length > 0" id="video" controls>
        <source :src="reshapeVideoUrl" />
        <track
          default
          kind="captions"
          label="English"
          srclang="en"
          ref="videoTrack"
        />
      </video> -->
      <embed
        :src="reshapeVideoUrl"
        v-if="videoUrl.trim().length > 0"
        ref="embedVideo"
      />
      <p class="result">{{ videoText }}</p>
    </div>
  </div>
</template>

<script>
const SpeechRecognition = window.SpeechRecognition || webkitSpeechRecognition; // eslint-disable-line
const recognition = new SpeechRecognition();
recognition.continuous = true;
recognition.lang = "en-US";
recognition.interimResults = true;
recognition.maxAlternatives = 2;
export default {
  data() {
    return {
      videoUrl: "",
      showHint: true,
      videoText: "",
    };
  },
  computed: {
    reshapeVideoUrl() {
      const getYouTubeVideoId = this.videoUrl.substring(
        this.videoUrl.indexOf("=") + 1
      );
      // console.log("getYouTubeVideoId", getYouTubeVideoId);
      return `https://www.youtube.com/embed/${getYouTubeVideoId}`;
    },
  },
  methods: {
    initRecognition() {
      recognition.start();
    },
    getRecognitionResult() {
      recognition.onresult = (e) => {
        this.videoText = e.results[0][0].transcript;
        console.log("result123", this.videoText);
      };
    },
    checkIfRecognitionDisributed() {
      recognition.onsoundend = () => this.initRecognition();
      recognition.onaudioend = () => this.initRecognition();
      recognition.onspeechend = () => this.initRecognition();
    },
    checkIfRecognitionStarted() {
      recognition.onsoundstart = () => this.getRecognitionResult();
      recognition.onaudiostart = () => this.getRecognitionResult();
      recognition.onspeechstart = () => this.getRecognitionResult();
    },
  },
  mounted() {
    setInterval(() => {
      this.checkIfRecognitionDisributed();
      this.checkIfRecognitionStarted();
    }, 1000);
  },
  watch: {
    // eslint-disable-next-line
    videoUrl(url, oldurl) {
      if (url.trim().length > 0) {
        this.initRecognition();
      }
    },
  },
};
</script>

<style>
body {
  margin: 0px;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #ffffff;
  width: 100vw;
  height: 100vh;
  background: linear-gradient(160deg, #02ccba 0%, #aa7ecd 100%);
  display: flex !important;
  justify-content: center;
}
.title {
  text-align: center;
  margin-top: 25px;
}
.input {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin-top: 15px;
}
input {
  width: 300px;
  height: 40px;
}
video {
  width: 800px;
  height: 400px;
  margin-top: 20px;
}
embed {
  width: 800px;
  height: 400px;
  margin-top: 20px;
}
.result {
  margin-top: 20px;
  text-align: center;
}
</style>
