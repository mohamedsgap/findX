<template>
  <div class="container">
    <h1 class="title">FindX, A Vue.JS app based on NLP</h1>
    <h4 v-if="showHint">Please allow mircophone!</h4>
    <div class="input">
      <a-input-search
        v-model:value="videoUrl"
        placeholder="enter youtube video url"
        :loading="inputLoading"
      />
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
import { ref, computed, onMounted, watch } from "vue";
const SpeechRecognition = window.SpeechRecognition || webkitSpeechRecognition; // eslint-disable-line
const recognition = new SpeechRecognition();
recognition.continuous = true;
recognition.lang = "en-US";
recognition.interimResults = true;
recognition.maxAlternatives = 2;
export default {
  setup() {
    const videoUrl = ref("");
    const showHint = ref(true);
    const videoText = ref("");
    const inputLoading = ref(false);

    const reshapeVideoUrl = computed(() => {
      const getYouTubeVideoId = videoUrl.value.substring(
        videoUrl.value.indexOf("=") + 1
      );
      return `https://www.youtube.com/embed/${getYouTubeVideoId}`;
    });

    const initRecognition = () => {
      recognition.start();
    };

    const getRecognitionResult = () => {
      recognition.onresult = (e) => {
        videoText.value = e.results[0][0].transcript;
        console.log("result123", videoText.value);
      };
    };

    const checkIfRecognitionDisributed = () => {
      recognition.onsoundend = () => initRecognition();
      recognition.onaudioend = () => initRecognition();
      recognition.onspeechend = () => initRecognition();
    };

    const checkIfRecognitionStarted = () => {
      recognition.onsoundstart = () => getRecognitionResult();
      recognition.onaudiostart = () => getRecognitionResult();
      recognition.onspeechstart = () => getRecognitionResult();
    };

    onMounted(() => {
      setInterval(() => {
        checkIfRecognitionDisributed();
        checkIfRecognitionStarted();
      }, 1000);
    });

    watch(videoUrl, (url) => {
      if (url.trim().length > 0) {
        inputLoading.value = true;
        initRecognition();
        showHint.value = false;
        console.log("123", url);
      }
    });

    watch(reshapeVideoUrl, (val) => {
      if (val.length > 0) {
        setTimeout(() => {
          inputLoading.value = false;
        }, 2000);
        console.log("123", val);
      }
    });

    return {
      videoUrl,
      showHint,
      videoText,
      reshapeVideoUrl,
      inputLoading,
    };
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

.ant-input-search {
  width: 400px !important;
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
