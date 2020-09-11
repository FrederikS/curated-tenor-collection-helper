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
      <el-button @click="add">Add</el-button>
      <img :src="gifs[index].media[0].tinygif.url" />
      <el-button @click="next">Next</el-button>
    </div>
  </main>
</template>

<script>
import Vue from "vue";

export default {
  name: "App",
  data() {
    return {
      query: "",
      gifs: [],
      index: 0,
      pos: {}
    };
  },
  mounted() {
    if (localStorage.pos) {
      this.pos = JSON.parse(localStorage.pos);
    }
  },
  watch: {
    pos: {
      handler: function(newPos) {
        localStorage.pos = JSON.stringify(newPos);
      },
      deep: true
    }
  },
  methods: {
    async search() {
      const key = process.env.VUE_APP_TENOR_API_KEY;
      const res = await fetch(
        `https://api.tenor.com/v1/search?q=${
          this.query
        }&key=${key}&media_filter=minimal&pos=${this.pos[this.query] || 0}`
      );
      const data = await res.json();
      this.gifs = data.results;
      if (!this.pos[this.query]) {
        Vue.set(this.pos, this.query, 0);
      }
    },
    async next() {
      if (this.index < this.gifs.length - 1) {
        this.index++;
        Vue.set(this.pos, this.query, this.pos[this.query] + 1);
      } else {
        Vue.set(this.pos, this.query, this.pos[this.query] + 1);
        await this.search();
        this.index = 0;
      }
    },
    add() {}
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

.tinder {
  display: flex;
  align-items: flex-start;
  justify-content: center;
}

.tinder img {
  margin: 0 50px;
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
