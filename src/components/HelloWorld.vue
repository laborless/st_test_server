<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>
      This is the service for testing Application protocol Layer
    </p>
    <p>
        Front End : <a href="https://github.com/laborless/st_test_server" target="_blank">https://github.com/laborless/st_test_server</a><br>
        Back  End : <a href="https://github.com/laborless/app_server_backend" target='_blank'>https://github.com/laborless/app_server_backend</a><br>
    </p>
    <p>
      <a>- Port 5000 : UDP Loopback</a><br>
      <a>- Port 5001 : UDP&emsp;&emsp;&emsp;&emsp;&ensp;</a><br>
      <a>- Port 3000 : TCP Loopback</a><br>
      <a>- Port 3001 : TCP&emsp;&emsp;&emsp;&emsp;&ensp;</a><br>
      <a>- Port 5683 : COAP REST (coap://[Host(domain)]/test)</a>
    </p>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #03234B;
}
</style>
