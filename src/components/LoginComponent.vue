<template>
  <div class="mdl-grid">
    <div class="mdl-layout-spacer"></div>
    <div class="mdl-card mdl-shadow--4dp my-login-card-size">
      <form @submit.prevent="logMeIn">
        <div class="mdl-card__title mdl-color--primary mdl-color-text--white">
          <h2 class="mdl-card__title-text">Login:</h2>
        </div>
        <div class="mdl-card__supporting-text">
          <div class="mdl-textfield mdl-js-textfield mdl-textfield--floating-label">
            <input ref="txtEmail" @input="checkEmailValidator" class="mdl-textfield__input" type="email" id="email">
            <label class="mdl-textfield__label" for="email">Email Address:</label>
            <span class="mdl-textfield__error">Please insert a valid email address.</span>
          </div>
          <div class="mdl-textfield mdl-js-textfield mdl-textfield--floating-label">
            <input ref="txtPassword" @input="checkPasswordValidator" class="mdl-textfield__input" type="password"
                   :pattern="passwordPattern" id="password">
            <label class="mdl-textfield__label" for="password">Password:</label>
            <span class="mdl-textfield__error">{{ passwordMessage}}</span>
          </div>
          <label class="mdl-checkbox mdl-js-checkbox mdl-js-ripple-effect" for="rememberMe">
            <input type="checkbox" id="rememberMe" class="mdl-checkbox__input" v-model="rememberMe">
            <span class="mdl-checkbox__label">Remember Me</span >
          </label>

        </div>
        <div class="mdl-card__actions mdl-card--border">
          <button :disabled="submitBtnDisabled"
                  class="mdl-button mdl-js-button mdl-button--raised mdl-js-ripple-effect mdl-button--accent">
            <span v-if="isLoading">Loading...</span>
            <span v-else>Sign In</span>
          </button>

          <span v-if="errorMessage" class="mdl-color-text--red-500"><strong>{{ errorMessage }}</strong></span>
        </div>
      </form>
    </div> <!-- End mdl-card -->
    <div class="mdl-layout-spacer"></div>
  </div>
</template>

<script>
  export default {
    name: 'LoginComponent',
    props: {
      passwordMessage: {
        type: String,
        required: false,
        default: 'Password length must be 2 to 8 characters.'
      },
      passwordPattern: {
        type: String,
        required: false,
        default: '.{2,8}',
        validator: function (value) {
          // Can not set the passwordPattern explicitly to ''
          return value !== ''
        }
      },
      errorMessage: {
        type: String,
        required: false,
        default: ''
      }
    },
    watch: {
      errorMessage: function () {
        if (this.errorMessage !== '') {
          this.isLoading = false
          this.submitBtnDisabled = false
        }
      }
    },
    data () {
      return {
        submitBtnDisabled: true,
        rememberMe: false,
        isLoading: false
      }
    },
    mounted: function () {
      let email = document.cookie.match('(^|;)\\s*' + 'email' + '\\s*=\\s*([^;]+)')
      let password = document.cookie.match('(^|;)\\s*' + 'password' + '\\s*=\\s*([^;]+)')
      this.$refs.txtEmail.value = email ? email.pop() : ''
      this.$refs.txtPassword.value = password ? password.pop() : ''
      if (email) this.submitBtnDisabled = false
      console.log('We just check to see if there were cookies: ' + document.cookie)
    },
    methods: {
      checkEmailValidator: function () {
        if (this.$refs.txtEmail.checkValidity()) {
          if (this.$refs.txtPassword.checkValidity() && this.$refs.txtPassword.value !== '') this.submitBtnDisabled = false
        } else {
          this.submitBtnDisabled = true
        }
      },
      checkPasswordValidator: function () {
        if (this.$refs.txtPassword.checkValidity()) {
          if (this.$refs.txtEmail.checkValidity() && this.$refs.txtEmail.value !== '') this.submitBtnDisabled = false
        } else {
          this.submitBtnDisabled = true
        }
      },
      logMeIn: function () {
        let email = this.$refs.txtEmail.value.trim()
        let password = this.$refs.txtPassword.value.trim()

        // COOKIE FUNCTIONS!
        // 'key=value; expires=current dateTime in UTC; path=/'
        if (this.rememberMe) {
          let d = new Date()
          d.setTime(d.getTime() + (180 * 24 * 60 * 60 * 1000)) //
          document.cookie = 'email=' + email + ';expires=' + d.toUTCString() + ';path=/'
          document.cookie = 'password=' + password + ';expires=' + d.toUTCString() + ';path=/'
          console.log('We just set the cookies: ' + document.cookie)
        } else {
          document.cookie = 'email=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/;'
          document.cookie = 'password=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/;'
          console.log('We just deleted the cookies: ' + document.cookie)
        }
        // INDICATE WORKING & CLEAN UP ANY PREVIOUS ERRORS
        this.isLoading = true
        this.submitBtnDisabled = true
        // EMIT THE LOGIN EVENT
        this.$emit('loginCredentials',
          {
            'email': email,
            'password': password
          })
      }
    }
  }
</script>

<style scoped>
  .my-login-card-size {
    max-width: 320px;
    height: auto;
  }
</style>
