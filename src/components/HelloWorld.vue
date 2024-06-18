<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>
      This is the service for testing Application Layer protocol
    </p>
    <p>
        ST87M01 : <a href="https://www.st.com/en/wireless-connectivity/st87m01.html" target="_blank">https://www.st.com/en/wireless-connectivity/st87m01.html</a><br>
        STMicroelectronics Github : <a href="https://github.com/STMicroelectronics" target='_blank'>https://github.com/STMicroelectronics</a><br>
        STMicroelectronics : <a href="https://www.st.com" target='_blank'>https://www.st.com</a>
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
