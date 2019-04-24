<template>
  <div class="infinite-scroll">
    <div class="input-area">
      <input type="text" v-model="inputWord" @focus="focus" />
      <button @click="changeButtonStatus">検索</button>
      {{message}}
    </div>
    <div v-for="(item, $index) in list" :key="$index">
        {{item.title}} 
    </div>
    <infinite-loading spinner="circles" @infinite="infiniteHandler" v-if="buttonStatus">
      <div slot="no-more">No more</div>
      <div slot="no-results">No result</div>
    </infinite-loading>
  </div>
</template>

<script>
import axios from 'axios';

let api = 'https://ci.nii.ac.jp/books/opensearch/search?title=';

export default {
  data() {
    return {
      inputWord: '',
      buttonStatus: false,
      page: 1,
      list: [],
      message: ''
    };
  },
  methods: {
    changeButtonStatus() {
      this.buttonStatus = true;
    },
    infiniteHandler($state) {
      axios.get((api + this.inputWord), {
          params: {
              p: this.page,
              format: 'json'
          }
      }).then(({data}) => {
        const items = data['@graph'][0]['items'];
        setTimeout(() => {
          if (items) {
            console.log(items)
            this.page += 1;
            this.list.push(...items);
            $state.loaded();
            return
          } else {
            $state.complete();
          } 
        }, 1000)
      }).catch((error) => {
        this.message = 'error'
      })
    },
    focus() {
      this.inputWord =  '',
      this.buttonStatus = false,
      this.page = 1,
      this.list = [],
      this.message = ''
    }
  },
};
</script>

<style>
.infinite-scroll {
  margin: 100px;
}
</style>