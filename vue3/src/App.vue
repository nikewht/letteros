<template>
  <section v-if="postsList" class="main">
    <div class="card-action">
      <button class="card-btn" @click="italicToggle()" :class="{'card-btn--active': isItalic }">Курсив</button>
      <button class="card-btn" @click="addItem">Добавить еще</button>
    </div>
    <div class="card-group">
      <div class="card" v-for="post in postsList" :key="post.id">
        <div class="card-title">{{ post.title }}</div>
        <img class="card-img" src="https://via.placeholder.com/600/92c952">
        <div class="card-body">
          <editor v-model="post.body" @selectionChange="handlerFunction"
                  api-key="v8ur67l08qz9v0d9sm08bw0chx47hzpqq8lweq48lr3u8auc"
                  :init="{menubar: false , toolbar: false}"/>
        </div>
      </div>
    </div>
  </section>
</template>

<script>

import axios from 'axios'
import Editor from '@tinymce/tinymce-vue'

export default {
  name: 'App',
  components: {
    'editor': Editor
  },
  data()
  {
    return {
      postsList: null,
      selectedText: '',
      editorInstance: null,
      limit: 3,
    }
  },
  computed: {
    isItalic: function ()
    {
      return this.selectedText.includes('<em>') || this.selectedText.includes('</em>');
    }
  },
  methods: {
    handlerFunction(e, inst)
    {
      this.editorInstance = inst.editorManager.activeEditor
      this.selectedText = this.editorInstance.selection.getContent({format: 'html'})
    },
    italicToggle()
    {
      if (this.isItalic)
      {
        this.editorInstance.setContent(`${this.selectedText.selection.replace('<em>', '').replace('</em>', '')}`)
        return
      }
      this.editorInstance.selection.setContent(`<em>${this.selectedText}</em>`)
    },
    async getPosts()
    {
      let params = {
        params: {
          _limit: this.limit
        }
      }
      let response = await axios.get('/api/posts', params)
      this.postsList = response.data
    },
    addItem()
    {
      this.limit++
      this.getPosts()
    }
  },
  mounted()
  {
    this.getPosts()
  }
}
</script>

<style>
.main {
  display: flex;
  flex-flow: row nowrap;
  justify-content: center;;
  margin: 50px 0;
}

.card-group {
  display: flex;
  flex-flow: column wrap;
  width: 500px;
}

.card {
  margin-bottom: 50px;
  box-shadow: 0px 17px 42px rgb(0 0 0 / 15%);
  padding: 25px;
}

.card-title {
  font-size: 24px;
  margin-bottom: 25px;
}

.card-img {
  max-width: 100%;
  margin-bottom: 25px;
  max-height: 200px;
  width: 100%;
  object-fit: cover;
}

.card-body {
  font-size: 18px;
}

.card-action {
  width: 300px;
}

.card-btn {
  background: #fff;
  color: #bbb;
  border: 1px solid #bbbbbb;
  border-radius: 4px;
  padding: 5px 10px;
  cursor: pointer;
}

.card-btn--active {
  background: #92c952;
  color: #fff;
}

.card-btn:hover {
  background: #bbb;
  color: #fff;
}
</style>
