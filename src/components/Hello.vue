<template>
  <div class="hello">
    <login-component
      passwordPattern=".{4,10}"
      passwordMessage="Password length must be 4 to 10 characters long."
      @loginCredentials="loginAttempt"
      :errorMessage="errorMessage"
    ></login-component>
  </div>
</template>

<script>
import LoginComponent from './LoginComponent.vue'

export default {
  name: 'hello',
  components: {
    'login-component': LoginComponent
  },
  data () {
    return {
      errorMessage: ''
    }
  },
  methods: {
    loginAttempt: function (credentials) {
      console.log('Email is: ' + credentials.email)
      console.log('Password is: ' + credentials.password)
      this.errorMessage = ''
      // let self = this
      setTimeout(() => {
        this.errorMessage = 'Bad Email or Password'
      }, 3000)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
