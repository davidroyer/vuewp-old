<template>
  <div id="app">

    <div class="container">
      <h1>{{ msg }}</h1>
      <div class="row">

        <div class="col-md-6">
          <div class="post_listings">
            <div class="post" v-for="post in allPosts">
              <h3>{{post.title.rendered}}</h3>
              <small>{{post.id}}</small>
              <div class="">
                {{{post.excerpt.rendered}}}
              </div>
              <button class="btn btn-info" type="button" @click="editPost(post.id, post.title.rendered, post.content.rendered)" name="button">Edit Post</button>
              <button class="btn btn-danger" type="button" @click="deletePost(post.id)" name="button">Delete Post</button>
              <hr>
            </div>
          </div>
        </div>
        <!-- <div class="post-editable" v-if="inEditMode">

          <textarea name="post-content" rows="8" cols="40" v-model="contentBeingEdited"></textarea>
        </div> -->

      <div class="col-md-6">
        <div class="post-editable" v-if="inEditMode">
          <input type="text" v-model="titleBeingEdited" name="post-title">
          <html5-editor :content.sync="contentBeingEdited" :height="500"></html5-editor>
          <button type="button" name="save-edit" @click="saveEdit">Save Edit</button>
        </div>
      </div>

      </div>  <!-- End of Row -->

      <div class="row">
        <div class="col-md-12">
          <div class="post-mutating">
            <div class="post_add">
              <button class="btn btn-info" type="button" @click="addPost" name="button">Test Add Post</button>
            </div>
          </div>
        </div>
      </div>

    </div>  <!-- End of Container -->

  </div>
</template>

<script>
var Vue = require('vue');
var VueResource = require('vue-resource');
var editor = require("vue-html5-editor")
Vue.directive('maxchars', {
    bind: function () {
        var self = this;
        this.handler = function () {
            var max_chars = self.vm.$get(self.expression)
            if (this.el.value.length > max_chars) {
                this.el.value = this.el.value.substr(0, max_chars)
            }
        }.bind(this)
        this.el.addEventListener('input', this.handler)
    },
    unbind: function () {
        this.el.removeEventListener('input', this.handler)
    }
})
 // v-maxchars="140"
Vue.use(editor, {image: {height: 300, width: 300}});
Vue.use(VueResource);
Vue.http.headers.common['Authorization'] = 'Basic ZHJveWVyOkRhbmNlNGxpZmU=';

export default {
  data () {
    return {
      // note: changing this line won't causes changes
      // with hot-reload because the reloaded component
      // preserves its current state and we are modifying
      // its initial state.
      msg: 'Hello Vue!',
      content: "<h3>vue html5 editor</h3>",
      allPosts: '',
      titleBeingEdited: '',
      contentBeingEdited: '',
      idBeingEdited: '',
      inEditMode: false
    }
  },
  ready: function () {
    this.$http.get('http://got2dance.loc/wp-json/wp/v2/posts').then((response) => {
      this.$set('allPosts', response.json())
     })


  },
  methods: {
    addPost: function () {
      this.$http.post('http://got2dance.loc/wp-json/wp/v2/posts',
        {
          title: 'Sat 942am From Vue',
          content: 'Content from Vue'
        }
      ).then((response) => {
          // console.log(response);
      })
    },
    editPost: function (id, title, content) {
      this.inEditMode = true
      this.idBeingEdited = id
      this.titleBeingEdited = title
      this.contentBeingEdited = content
    },

    saveEdit: function () {
      this.$http.put('http://got2dance.loc/wp-json/wp/v2/posts/' + this.idBeingEdited + '',
        {
          title: this.titleBeingEdited,
          content: this.contentBeingEdited
        }
      ).then((response) => {
          // console.log(response);
      })
    },
    deletePost: function () {

    }
  }
}
</script>
<style>
body {
  font-family: Helvetica, sans-serif;
}
</style>
