<template>
  <h3>📥TCP Echo server packets📤</h3>
  <div v-if="$root.session == 0">
    <a><strong>Please Sign In</strong></a>
  </div>
  <div v-else>
    <p style="text-align: right; margin-right: 20px;"><a href="#" @click.prevent="downloadDB"><strong>Save DB💾</strong></a></p>
    <div class="Wrapper"></div>
    <div>
      <p><strong>Page: {{currPage}}</strong></p>
      <div class="row" v-for="record in records" v-bind:key="record.id">
        <span class="record" v-if="record.type === 'Tx'" style="background-color: AliceBlue; margin-left: 20px;">
          📭<strong>Tx</strong> {{ record.time }} : "{{ record.payload }}" to {{ record.address }}:{{ record.port }}
        </span>
        <span class="record" v-else-if="record.type === 'Rx'" style="background-color:lightyellow;">
          📫<strong>Rx</strong> {{ record.time }} : "{{ record.payload }}" from {{ record.address }}:{{ record.port }}
        </span>
        <span class="record" v-else>
          Unknown Packet! type: {{record.type}}
        </span>
      </div>
      <hr>
      <span v-if="currPage > 1">
        <a class="page" href="#" @click="loadPage(currPage-2)">Prev</a>
      </span>
      <span v-for="n in pageNum" v-bind:key="n">
        <a class="page" href="#" v-if="n < 4" @click="loadPage(n-1)" >{{ n }}</a>
        <a class="page" v-else-if="n == 4 && n != currPage"  >..</a>
        <a class="page" v-else-if="n == (pageNum - 3) && n != currPage" >..</a>
        <a class="page" href="#" v-else-if="n > pageNum - 3" @click="loadPage(n-1)" >{{ n }}</a>
        <a class="page" href="#" v-else-if="n == currPage" @click="loadPage(n-1)" >{{ n }}</a>
      </span>
      <span v-if="currPage < pageNum">
        <a class="page" href="#" @click="loadPage(currPage)" >Next</a>
      </span>
    </div>
    </div>
</template>

<script>
export default {
  // props: ['session'],
  data () {
    return {
      recordNum: 0,
      pageNum: 0,
      currPage: 0,
      records: {}
    }
  },
  mounted () {
    this.loadPageNum()
    this.loadPage(this.pageNum)
  },
  methods: {
    async loadPageNum () {
      try {
        const response = await fetch('https://' + window.location.hostname + '/api/tcpecho/count', {
          // mode: 'cors',
          credentials: 'include'
        })
        if (response.status === 200) {
          const jsonData = await response.json()
          this.recordNum = jsonData[0]['COUNT(*)']
          this.pageNum = parseInt(this.recordNum / 10) + 1
        } else if (response.status === 401) {
          await this.$root.refreshSession()
          if (this.$root.session === 1) {
            const response = await fetch('https://' + window.location.hostname + '/api/tcpecho/count', {
              // mode: 'cors',
              credentials: 'include'
            })
            const jsonData = await response.json()
            this.recordNum = jsonData[0]['COUNT(*)']
            this.pageNum = parseInt(this.recordNum / 10) + 1
          }
        } else {
          this.$root.session = 0
        }
      } catch (error) {
        // console.error('Fail to get page num')
      }
    },
    async loadPage (num) {
      try {
        const response = await fetch('https://' + window.location.hostname + '/api/tcpecho/page/' + num, {
          // mode: 'cors',
          credentials: 'include'
        })
        if (response.status === 200) {
          this.records = await response.json()
          this.currPage = num + 1
        } else if (response.status === 401) {
          await this.$root.refreshSession()
          if (this.$root.session === 1) {
            const response = await fetch('https://' + window.location.hostname + '/api/tcpecho/page/' + num, {
              // mode: 'cors',
              credentials: 'include'
            })
            this.records = await response.json()
            this.currPage = num + 1
          }
        } else {
          this.$root.session = 0
        }
      } catch (error) {
        // console.error('Fail to get records')
      }
    },
    async downloadDB () {
      try {
        const response = await fetch('https://' + window.location.hostname + '/api/tcpecho/db', {
          // mode: 'cors',
          credentials: 'include'
        })
        if (response.status === 200) {
          const blob = await response.blob()
          const url = window.URL.createObjectURL(blob)
          const a = document.createElement('a')
          a.style.display = 'none'
          a.href = url
          a.download = 'tcpecho_db'
          document.body.appendChild(a)
          a.click()
          window.URL.revokeObjectURL(url)
        } else if (response.status === 401) {
          await this.$root.refreshSession()
          if (this.$root.session === 1) {
            const response = await fetch('https://' + window.location.hostname + '/api/tcpecho/db', {
              // mode: 'cors',
              credentials: 'include'
            })
            const blob = await response.blob()
            const url = window.URL.createObjectURL(blob)
            const a = document.createElement('a')
            a.style.display = 'none'
            a.href = url
            a.download = 'tcpecho_db'
            document.body.appendChild(a)
            a.click()
            window.URL.revokeObjectURL(url)
          }
        } else {
          this.$root.session = 0
        }
      } catch (error) {
        // console.error('Fail to download DB')
      }
    }
  },
  watch: {
    '$root.session': function (val, oldVal) {
      if (val === 1) {
        this.loadPageNum()
        this.loadPage(this.pageNum)
      }
    }
  }
}
</script>

<style>
div .row {
  padding-top: 3px;
  padding-bottom: 3px;
  display: flex;
}
div .record {
  padding: 5px 5px;
  border: solid gray;
  border-width: thin;
  border-radius: 10px;
  text-align: left;
  /* margin: 2px; */
  /* flex: 1; */
}
div .page {
  padding: 5px 3px;
}
</style>
