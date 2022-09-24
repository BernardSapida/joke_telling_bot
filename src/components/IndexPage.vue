<template>
  <div class="container pt-5">
    <select name="voiceOptions" class="form-select mb-5" id="voiceOptions">
      <option v-for="(voice, index) in voiceList" :data-name="voice.name" :data-lang="voice.lang" :key="index" :selected="index == 95 ? 'selected' : ''">
        {{voice.name}}
      </option>
    </select>
    <div class="row mt-5">
      <div class="col-6 m-auto">
        <div class="qoobee_joke mb-3 pt-3 pb-1 px-3 rounded">
          <p>{{maleMessage}}</p>
        </div>
        <img src="../../public/images/QoobeeThinking.gif" height="250px" alt="Qoobee Thinking">
      </div>
      <div class="col-6 m-auto">
        <div class="qoobee_question mb-5 pt-3 pb-2 px-3 rounded">
          <p>{{femaleMessage}}</p>
        </div>
        <img src="../../public/images/QoobeeAsking.gif" height="210px" alt="Qoobee Asking">
        <button v-if="speechEnded" class="btn d-block mt-3 m-auto askQuestion px-3 py-2" @click="talkQoobee('Love, can you please make me laugh?', 'female')">TELL ME A JOKE</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
  data() {
    return {
      femaleMessage: "Hello love! How are you?",
      maleMessage: "I'm fine, thanks!",
      synth: window.speechSynthesis,
      voiceList: [],
      choosenVoice: 0,
      speechEnded: true
    }
  },
  mounted() {
    this.voiceList = this.synth.getVoices();
    this.synth.onvoiceschanged = () => {
      this.voiceList = this.synth.getVoices();
      if(this.speechEnded == false) {
        // this.talkQoobee(this.femaleMessage, "female");
        // this.talkQoobee(this.maleMessage, "male");
        // setTimeout(() => this.speechEnded = true, 6000)
      }
    }
  },
  methods: {
    talkQoobee(grammar, gender) {
      const toSay = new SpeechSynthesisUtterance(grammar);

      this.speechEnded = false;

      if(gender == "female") this.femaleMessage = grammar;
      else this.maleMessage = grammar;

      toSay.voice = this.voiceList[95]
      this.synth.speak(toSay);

      if(grammar == 'Love, can you please make me laugh?') {
        this.talkQoobee("Yes, sure HAHA!", "male")
        setTimeout(() => this.speechEnded = true, 6000)
        setTimeout(() => this.randomJokes(), 6000);
      }
    },
    async randomJokes() {
      let requestJoke = await fetch("https://official-joke-api.appspot.com/jokes/programming/random");
      let response = await requestJoke.json();
      this.talkQoobee(response[0].setup, "male");
      await setTimeout(() => this.talkQoobee(response[0].punchline, "male"), 6000);
      await setTimeout(() => this.talkQoobee("You are funny! I love you, love!", "female"), 6000);
      this.speechEnded = true;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
