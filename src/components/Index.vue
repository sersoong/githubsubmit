<template>
    <div id="demo">
        <h1>Latest Vue.js Commits</h1>
        <template v-for="(branch,index) in branches">
            <input type="radio"
            :value="branch"
            :id="branch"
            name="branch"
            v-model="currentBranch"
            v-bind:key="index"
            >
            <label :for="branch" v-bind:key="index">{{ branch }}</label>
        </template>
        <p>vuejs/vue@{{ currentBranch }}</p>
        <ul>
            <li v-for="record in commits" :key="record.num">
                <a :href="record.html_url" target="_blank" class="commit">{{ record.sha.slice(0,7) }}</a>
                - <span class="message">{{ record.commit.message | truncate }}</span><br>
                by <span class="author"><a :href="record.author.html_url" target="_blank">{{ record.commit.author.name }}</a></span>
                at <span class="date">{{ record.commit.author.date | formatDate }}</span>
            </li>
        </ul>
    </div>
</template>

<script>
export default {
  data () {
    return {
      apiURL: 'https://api.github.com/repos/vuejs/vue/commits?per_page=3&sha=',
      branches: ['master', 'dev'],
      currentBranch: 'master',
      commits: null
    }
  },
  created: function () {
    this.fetchData()
  },
  watch: {
    currentBranch: 'fetchData'
  },
  filters: {
    truncate: function (v) {
      var newline = v.indexOf('\n')
      return newline > 0 ? v.slice(0, newline) : v
    },
    formatDate: function (v) {
      return v.replace(/T|Z/g, ' ')
    }
  },
  methods: {
    fetchData: function () {
      var xhr = new XMLHttpRequest()
      var self = this
      xhr.open('GET', this.apiURL + self.currentBranch)
      xhr.onload = function () {
        self.commits = JSON.parse(xhr.responseText)
        // console.log(xhr.responseText)
        // console.log(self.commits[0].html_url)
      }
      xhr.send()
    }
  }
}
</script>
