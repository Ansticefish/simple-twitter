<template>
  <div class="container">
    <div class="row">
      <div class="signin col-lg-4 col-xl-4">
        <img 
         class="
         signin__logo 
         mt-8"
         src="../assets/icons/Icon.png"
        >
        <h3 
         class="
         signin__header 
         mt-6"
         >
          登入 Alphitter
        </h3>
        <form 
          @submit.stop.prevent="handleSubmit"
          class="signin__form"
        >
          <div 
           class="
           signin__form__wrapper 
           mt-8"
          >
            <label for="signin__form__wrapper__account">
            帳號
            </label>
            <input type="text" 
            v-model="account"
            name="account"
            id="signin__form__wrapper__account"
            :class="{'error': a.error}" 
            placeholder="請輸入帳號" 
            required
            autofocus
            >
            <label
             class="error-message"
            >
              {{ a.warning }}
            </label>
          </div> 
          <div 
            class="
            signin__form__wrapper 
            mt-6"
          >
            <label for="signin__form__wrapper__password">
            密碼
            </label>
            <input type="password" 
            v-model="password"
            :class="{'error': p.error }"
            name="password"
            id="signin__form__wrapper__password" 
            placeholder="請輸入密碼" 
            required
            >
            <label
             class="error-message"
            >
              {{ p.warning }}
            </label>
          </div> 
          <button   
           class="signin__form__btn--submit
           mt-8"
           :disabled="isProcessing"
          >
            登入
          </button>
        </form>
        <ul 
         class="signin__ul
         mt-4"
         >
            <li 
            class="signin__ul__li
            mr-3"
            >
              <router-link to="/register">
                註冊
              </router-link>
            </li>
            <li 
             class="signin__ul__li
             mr-3">
              <div 
              class="signin__ul__li__dot" 
              >
              </div>
            </li>
            <li
             class="signin__ul__li"
            >
              <router-link to="/admin/signIn">
                後台登入
              </router-link>
            </li>
          </ul>
      </div> 
    </div>
  </div>
</template>

<script>
import { Toast, ToastIcon } from '../utils/helpers'
import { preventInputBlank } from '../utils/mixins'
import authorizationAPI from '../apis/authorization'

export default {
  name: 'SignIn',
  data () {
    return {
      account: '',
      password: '',
      a: {
        error: false,
        warning: ''
      },
      p: {
        error: false,
        warning: ''
      },
      isProcessing: false
    }
  },
  mixins: [preventInputBlank],
  methods: {
    async handleSubmit () {
      try {
        this.isProcessing = true
        // avoid empty data
        if (!this.account.trim() || !this.password.trim()){
          Toast.fire({
            title: '帳號、密碼不可空白',
            html: ToastIcon.redCrossHtml
          })
          this.isProcessing = false
          return
        }

        const { data } = await authorizationAPI.signIn( {
          account: this.account,
          password: this.password
        })

        Toast.fire({
          title: '登入成功',
          html: ToastIcon.greenCheckHtml
        })

        

        localStorage.setItem('token', data.token)
        this.$store.commit('setCurrentUser', data.user)
        this.$router.push({ name: 'home-page'})
      

      } catch (error) {
        this.isProcessing = false
        const errorMsg = error.response.data.message
        if( errorMsg === 'Error:帳號不存在！'){
          this.a.error = true
          this.a.warning = '帳號不存在！'
        } else {
          const message = errorMsg.slice(6)
          Toast.fire({
            title: `${message}`,
            html: ToastIcon.redCrossHtml
          })
          this.password = ''
        }
      }   
    }
  },
}
</script>

<style lang="scss" scoped>
@import '../assets/scss/signIn.scss';

.signin {
  @extend %signin;
}
 
// set up dot style
.signin__ul {
  &__li {
    display: flex;
    align-items: center;
    &__dot {
      height: 3px;
      width: 3px;
      border-radius: 50%;
      background: #000000;
    }
  }
} 

</style>