<template>
  <span>
    <span v-if="!viewComments" class="text-center">
      <q-btn @click="viewComments = !viewComments" icon="forum" color="blue-grey-6" dense flat label="View replies" />
    </span>
    <span v-if="viewComments">
    <span v-if="loading">
    <div><q-skeleton text /><q-skeleton button /><q-skeleton button /></div>
    <q-card dense flat bordered v-for="i in 5" :key="i.index" style="width:400px">
      <q-card-section avatar>
        <q-skeleton circle width="40" height="40" />
      </q-card-section>
      <q-card-section>
        <q-skeleton text width="100%" height="20" />
      </q-card-section>
      <q-card-section>
        <q-skeleton text />
        <q-skeleton button />
        <q-skeleton button />
      </q-card-section>
    </q-card>
    </span>
    <q-card dense flat bordered v-if="!loading">
      <q-card-section class="text-h6 text-center">
          <div>
            {{ Object.keys(this.comments).length - 1 }} Replies
            <q-btn icon="settings" title="Comment filtering and sorting options" class="hvr" dense flat>
              <q-popup-proxy>
                <q-card class="q-pa-md shadow-4" dense bordered>
                  <span class="text-caption">Gray <q-checkbox v-model="filter.gray" /> Hidden <q-checkbox v-model="filter.hide" /></span>
                  <q-select v-model="commentSortMethod" :options="commentSortMethods" label="Sort" dense />
                  <q-select v-model="commentSortDirection" :options="commentSortDirections" label="Direction" dense />
              </q-card>
              </q-popup-proxy>
            </q-btn>
            <q-btn icon="close" color="red" title="Hide comments" @click="viewComments = !viewComments" dense flat class="hvr" />
          </div>
      </q-card-section>
      <span v-for="comment in comments" :key="comment.id">
        <q-card flat bordered v-if="comment.parent_permlink === permlink && returnFilterStatus(comment)">
          <comment :comment="comment" :comments="comments" :parentAuthor="author" :parentPermlink="permlink" :parentDepth="comment.depth" />
        </q-card>
      </span>
    </q-card>
    </span>
  </span>
</template>
<script>
import comment from 'components/comment.vue'
export default {
  name: 'comments',
  data () {
    return {
      comments: [],
      viewComments: true,
      loading: false,
      filter: {
        gray: true,
        hide: true
      },
      commentSortMethod: 'net_rshares',
      commentSortMethods: [
        { label: 'Votes (rshares)', value: 'net_rshares' },
        { label: 'Age', value: 'post_id' },
        { label: 'Author Reputation', value: 'author_reputation' },
        { label: 'Replies', value: 'children' }
      ],
      commentSortDirection: 'desc',
      commentSortDirections: [
        { label: 'Ascending', value: 'asc' },
        { label: 'Descending', value: 'desc' }
      ]
    }
  },
  watch: {
    viewComments: function () {
      if (this.viewComments) {
        this.getReplies()
      }
    },
    commentSortMethod: {
      deep: true,
      handler: 'resortComments'
    },
    commentSortDirection: {
      deep: true,
      handler: 'resortComments'
    }
  },
  props: ['author', 'permlink'],
  components: { comment },
  computed: {
  },
  methods: {
    resortComments () {
      var c = this.comments
      // this.comments = null
      this.comments = this.sortData(this.commentSortMethod.value, c, this.commentSortDirection.value)
    },
    returnFilterStatus (comment) {
      if (this.filter.gray && comment.stats.gray) { return false }
      if (this.filter.hide && comment.stats.hide) { return false }
      return true
    },
    async getReplies () {
      this.loading = true
      var params = { author: this.author, permlink: this.permlink }
      if (this.loggedInUser) { params.observer = this.loggedInUser }
      this.$hive.api.callAsync('bridge.get_discussion', params)
        .then(response => {
          this.loading = false
          this.comments = response
          this.resortComments()
        })
        .catch((err) => {
          this.loading = false
          console.error(err)
        })
    },
    sortData (key, data, type) {
      const ordered = {}
      let compareFunction = function (a, b) {
        return data[b][key] - data[a][key]
      }
      if (type === 'asc') {
        compareFunction = function (a, b) {
          return data[a][key] - data[b][key]
        }
      }
      Object.keys(data).sort(compareFunction).forEach(function (key) {
        ordered[key] = data[key]
      })
      return ordered
    }
  },
  mounted () {
    this.getReplies()
  }
}
</script>
