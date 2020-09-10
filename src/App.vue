<template>
  <main id="app">
    <el-form inline @submit.native.prevent>
      <el-form-item>
        <el-input
          placeholder="Enter your search..."
          v-model="query"
          clearable
          @change="search"
        />
      </el-form-item>
    </el-form>
    <div class="tinder" v-if="gifs.length > 0">
      <img :src="gifs[index].media[0].gif.url" />
      <div class="actions">
        <el-button @click="next">Next</el-button>
      </div>
    </div>
  </main>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      query: "",
      gifs: [],
      index: 0
    };
  },
  methods: {
    async search() {
      const key = process.env.VUE_APP_TENOR_API_KEY;
      const res = await fetch(
        `https://api.tenor.com/v1/search?q=${this.query}&key=${key}`
      );
      const data = await res.json();
      this.gifs = data.results;
    },
    next() {
      if (this.index < this.gifs.length) {
        this.index++;
      }
    }
  }
};
</script>

<style>
html,
body {
  height: 100%;
  display: grid;
  background-color: #eaeaea;
}

.el-form .el-input[type="text"] {
  width: 500px;
}

.tinder img {
  margin: auto;
}

.tinder .actions {
  margin: 10px;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
