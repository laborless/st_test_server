<template>
  <div v-if="session == 0" align="right">
    <a>user <input type="text" v-model="username" size=5 placeholder=""></a>
    <a> / </a>
    <a>pw <input type="password" v-model="password" size=5 placeholder=""></a>
    <a>&nbsp;</a>
    <button type="submit" @click="signIn()">Sign In</button>
  </div>
  <div v-else align="right">
    <a>Hello!</a>
    <a>&nbsp;</a>
    <button type="submit" @click="signOut()">Sign Out</button>
  </div>
  <nav>
    <router-link to="/">Home</router-link> |
    <router-link to="/udp">UDP</router-link> |
    <router-link to="/tcp">TCP</router-link> |
    <!--<router-link to="/udpdemo">UDP DEMO</router-link> |-->
    <router-link to="/about">About</router-link>
  </nav>
  <router-view/>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

nav {
  padding: 10px;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #03234B;
}
</style>

<script>
export default {
  data () {
    return {
      session: 0,
      username: '',
      password: ''
    }
  },
  mounted () {
    this.verifySession()
  },
  methods: {
    async signIn () {
      const body = { username: this.username, password: this.password }
      const header = { Accept: '*/*', 'Content-Type': 'application/json' }
      try {
        const response = await fetch('https://' + window.location.hostname + '/api/auth/session', {
          method: 'POST',
          // mode: 'cors',
          credentials: 'include',
          headers: header,
          body: JSON.stringify(body)
        })
        if (response.status === 200) {
          this.session = 1
          // console.log('login ok')
        } else {
          // console.log('login failed')
        }
      } catch (error) {
        // console.error('Fail to get page num')
      }
      this.username = ''
      this.password = ''
    },
    async signOut () {
      const header = { Accept: '*/*' }
      try {
        const response = await fetch('https://' + window.location.hostname + '/api/auth/session', {
          method: 'DELETE',
          // mode: 'cors',
          credentials: 'include',
          headers: header
        })
        if (response.status === 200) {
          // console.log('logout ok')
        } else {
          // console.log('logout failed')
          document.cookie += ';max-age=0'
          document.cookie += ';max-age=0'
        }
      } catch (error) {
        document.cookie += ';max-age=0'
        document.cookie += ';max-age=0'
      }
      this.session = 0
    },
    async refreshSession () {
      const header = { Accept: '*/*' }
      try {
        const response = await fetch('https://' + window.location.hostname + '/api/auth/refresh', {
          method: 'POST',
          // mode: 'cors',
          credentials: 'include',
          headers: header
        })
        if (response.status === 200) {
          this.session = 1
        } else {
          this.session = 0
        }
      } catch (error) {
        this.session = 0
      }
    },
    async verifySession () {
      const header = { Accept: '*/*' }
      try {
        const response = await fetch('https://' + window.location.hostname + '/api/auth/protected', {
          method: 'GET',
          // mode: 'cors',
          credentials: 'include',
          headers: header
        })
        if (response.status === 200) {
          this.session = 1
        } else if (response.status === 401) {
          this.refreshSession()
        } else {
          this.session = 0
        }
      } catch (error) {
        this.session = 0
      }
    }
  }
}
</script>
