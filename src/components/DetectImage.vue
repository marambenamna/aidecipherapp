<template>
  <div> <!-- Wrap the entire content in a single div -->
    <div class="hello">
      <h1>{{ msg }}</h1>
      <p>
        For a guide and recipes on how to configure / customize this project,<br>
        check out the
        <a href="https://cli.vuejs.org" target="_blank" rel="noopener">vue-cli documentation</a>.
      </p>
      <h3>Installed CLI Plugins</h3>
      <ul>
        <li><a href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-babel" target="_blank" rel="noopener">babel</a></li>
        <li><a href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-pwa" target="_blank" rel="noopener">pwa</a></li>
        <li><a href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-eslint" target="_blank" rel="noopener">eslint</a></li>
      </ul>
      <h3>Essential Links</h3>
      <ul>
        <li><a href="https://vuejs.org" target="_blank" rel="noopener">Core Docs</a></li>
        <li><a href="https://forum.vuejs.org" target="_blank" rel="noopener">Forum</a></li>
        <li><a href="https://chat.vuejs.org" target="_blank" rel="noopener">Community Chat</a></li>
        <li><a href="https://twitter.com/vuejs" target="_blank" rel="noopener">Twitter</a></li>
        <li><a href="https://news.vuejs.org" target="_blank" rel="noopener">News</a></li>
      </ul>
      <h3>Ecosystem</h3>
      <ul>
        <li><a href="https://router.vuejs.org" target="_blank" rel="noopener">vue-router</a></li>
        <li><a href="https://vuex.vuejs.org" target="_blank" rel="noopener">vuex</a></li>
        <li><a href="https://github.com/vuejs/vue-devtools#vue-devtools" target="_blank" rel="noopener">vue-devtools</a></li>
        <li><a href="https://vue-loader.vuejs.org" target="_blank" rel="noopener">vue-loader</a></li>
        <li><a href="https://github.com/vuejs/awesome-vue" target="_blank" rel="noopener">awesome-vue</a></li>
      </ul>
    </div>
    <div>
      <h1>{{ msg }}</h1>
      <div>
        <img class="image" ref="img" :src="require('../assets/images/' + getImgIndex + '.jpg')" />
      </div>
      <div>
        <button class="button" @click="next()" :disabled="disable">Next</button>
      </div>
      <div v-for="pred in predictions" :key="pred.index">
        {{ pred.label }}: {{ pred.probability.toFixed(0) + '%' }}
      </div>
      <div v-if="!predictions.length">hmm.....</div>
    </div>
    <div v-for="pred in predictions" :key="pred.index">{{ pred.label }}: {{ pred.probability.toFixed(0) + '%' }}</div>
<div v-if="!predictions.length">hmm.....</div>
  </div> <!-- Close the wrapper div -->
</template>
<script>
import * as cvstfjs from "customvision-tfjs";
import labels from "raw-loader!../../public/models/labels.txt";

export default {
  name: 'DetectImage',
  props: {
    msg: String
  },
  data() {
    return {
      image: 0,
      numImages: 16,
      predictions: [],
      model: null,
      labels: labels.split("\n").map(e => e.trim()), // Ensure labels are parsed once
    };
  },
  computed: {
    getImgIndex() {
      return this.image.toString();
    },
    disable() {
      return this.image === this.numImages;
    }
  },
  async mounted() {
    this.image++;
    this.model = new cvstfjs.ClassificationModel();
    await this.model.loadModelAsync("models/model.json");
    this.predict();
  },
  methods: {
    next() {
      this.image++;
      this.predictions = [];
      setTimeout(this.predict, 500);
    },
    async predict() {
      if (!this.$refs.img) return;
      let prediction = await this.model.executeAsync(this.$refs.img);
      this.predictions = prediction[0].map((p, i) => ({
        index: i,
        label: this.labels[i],
        probability: p * 100,
      }));
    }
  }
}
</script>


<style scoped>
h3 {
  margin: 40px 0 0;
}
.image {
  min-height: 300px;
  max-height: 300px;
  max-width: 100%;
}
.button {
  width: 200px;
  height: 50px;
  border-radius: 5px;
  background-color: blueviolet;
  color: white;
  font-size: 20pt;
  margin: 10px;
}
.button:disabled,
.button[disabled] {
  border: 1px solid #999999;
  background-color: #cccccc;
  color: #666666;
}
</style>
