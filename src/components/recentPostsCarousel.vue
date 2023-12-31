<template>
  <div>
    <div>
      <q-card flat bordered>
      <div class="text-h6 text-center">
        <q-icon name="rss_feed" color="orange" /> <span v-if="postType === 'blog'" @click="postType = 'posts'; init()" class="text-primary cursor-pointer">Recently shared</span><span v-if="postType === 'posts'" @click="postType = 'blog'; init()" class="text-primary cursor-pointer">Recently posted</span> by <span @click="settings = true" class="text-primary cursor-pointer">{{ this.account }}</span> <q-btn v-if="false" icon="settings" @click="settings = true" />
      </div>
      <q-skeleton height="250px" width="400" animation="fade" rect v-if="loading" />
      <q-carousel
        v-if="posts.length > 0"
        v-model="slide"
        transition-prev="jump-left"
        transition-next="jump-right"
        swipeable
        animated
        control-color="primary"
        padding
        arrows
        infinite
        :autoplay="autoplaySlides"
        height="250px"
        width="450px"
        @mouseenter="autoplaySlides = false"
        class="bg-dark text-white shadow-2 rounded-borders"
      >
        <q-carousel-slide :name="post.permlink" class="column no-wrap flex-center" v-for="post in posts" :key="post.index" :img-src="returnPostImage(post)">
          <div class="custom-caption">
            <router-link :to="returnPostPath(post.author, post.permlink)">{{ s(post.title).substr(0,100) }}</router-link><br />
            by <span class="text-bold"><router-link :to="linkAccount(post.author)">@{{ post.author }}</router-link></span><br />
            <span class="text-caption">{{ timeDelta(post.created) }}</span><br />
            <span class="text-caption wrap" v-if="post.json_metadata.description"> {{s(post.json_metadata.description).substr(0,100)}}</span>
            <span class="text-caption wrap" v-else> {{ returnPostSummary(post) }}</span>
          </div>
          <div class="absolute-bottom text-center"><q-avatar size="3em"><q-img :src="getHiveAvatarUrl(post.author)" /></q-avatar></div>
        </q-carousel-slide>
      </q-carousel>
      <q-dialog v-model="settings">
        <q-card flat bordered class="q-pa-md text-center">
          <q-input label="Account to view" v-model="account" @submit="init()"/>
          <q-btn-toggle push glossy toggle-color="primary" :options="[{ label: 'Posts', value: 'posts' }, { label: 'Blog', value: 'blog' }]" v-model="postType" />
          <q-btn dense flat icon="refresh" label="update" color="green" @click="settings = false; init()" style="margin:both"/>
        </q-card>
      </q-dialog>
      </q-card>
    </div>
  </div>
</template>
<style scoped>
a { text-decoration: none; color: #1d8ce0 }
a:link { color: #1d8ce0; font-weight: normal; text-decoration: none; }
a:visited { color: #1d8ce0; }
.custom-caption { text-align: center; padding: 12px; font-weight: normal; color: white; background-color: rgba(0, 0, 0, .9) }
</style>
<script>
import moment from 'moment'
import sanitize from 'sanitize-html'
import { postBodySummary, catchPostImage } from '@ecency/render-helper'
export default {
  name: 'recentPostsCarousel',
  data () {
    return {
      posts: [],
      showResteems: true,
      slide: null,
      limit: 10,
      settings: false,
      loading: false,
      autoplaySlides: this.autoplay,
      postType: this.type
    }
  },
  watch: {
    $route: function () {
      this.getRecentPosts()
    }
  },
  props: {
    account: String,
    autoplay: {
      type: Boolean,
      required: false,
      default: true
    },
    type: {
      type: String,
      default: 'posts' // posts or blog
    }
  },
  computed: {
    firstPermlink: function () {
      if (this.posts.length > 0) {
        return this.posts[0].permlink
      } else {
        return null
      }
    },
    autoplayProp: function () {
      return this.autoplay
    }
  },
  methods: {
    returnPostImage (post) {
      return catchPostImage(post)
    },
    returnPostPath (author, permlink) {
      return '/@' + author + '/' + permlink
    },
    returnPostSummary (post) {
      return postBodySummary(post, 150)
    },
    linkAccount (author) {
      return '/@' + author
    },
    getHiveAvatarUrl (user) {
      return 'https://images.hive.blog/u/' + user + '/avatar'
    },
    timeDelta (timestamp) {
      var now = moment.utc()
      var stamp = moment.utc(timestamp)
      var diff = stamp.diff(now, 'minutes')
      return moment.duration(diff, 'minutes').humanize(true)
    },
    async getRecentPosts () {
      this.loading = true
      this.posts = []
      var params = { sort: this.postType, account: this.account, limit: this.limit, observer: this.loggedInUser || this.account, start_author: null, start_permlink: null }
      this.$hive.api.callAsync('bridge.get_account_posts', params)
        .then(res => {
          this.loading = false
          this.posts = res
          this.slide = this.firstPermlink
        })
    },
    s (input) {
      // Render markdown to html, strip all tags and attributes, remove URLS
      var options = { allowedTags: [], allowedAttributes: [], disallowedTagsMode: 'discard' }
      return sanitize(input, options)
      //  .replace(/\b((?:[a-z][\w-]+:(?:\/{1,3}|[a-z0-9%])|www\d{0,3}[.]|[a-z0-9.-]+[.][a-z]{2,4}\/)(?:[^\s()<>]+|\(([^\s()<>]+|(\([^\s()<>]+\)))*\))+(?:\(([^\s()<>]+|(\([^\s()<>]+\)))*\)|[^\s`!()[\]{};:'".,<>?«»“”‘’]))/g, '')
    },
    init () {
      this.getRecentPosts()
    }
  },
  mounted () {
    this.init()
  }
}
</script>
