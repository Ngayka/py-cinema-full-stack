<template>
  <div class="sign-in" v-if="active">
    <h1>Sign in to Cinema Shop</h1>
    <h2>Please enter your sign in details.
    <a href="#/sign-up">Sign up</a>
    here if you are not registered yet.</h2>
    <input-item
      label="Login"
      pattern="[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}"
      placeholder="Email"
      v-model="email"></input-item>
    <password-input v-model="password"></password-input>
    <action-button label="Sign in" @click="signIn"></action-button>
  </div>
</template>

<script>
import ActionButton from '../comps/ActionButton.vue';
import InputItem from '../comps/InputItem.vue';
import PasswordInput from '../comps/PasswordInput.vue';

export default {
  data: () => ({
    active: false,
    email: '',
    password: ''
  }),
  methods: {
    hashHandler () {
      this.active = Boolean(!location.hash.match('sign-up$'));
    },

    async signIn () {
      try {
        const { data } = await this.axios.post(`${import.meta.env.VITE_API_URL}/api/user/token/`, {
          email: this.email,
          password: this.password
        });

        const { access, refresh } = data;

        localStorage.setItem('access', access);
        localStorage.setItem('refresh', refresh);

        this.$emit('log-in');
      } catch (err) {
        console.error(err.response.data);
      }
    }
  },
  mounted () {
    window.addEventListener('hashchange', this.hashHandler);
    this.hashHandler();
  },
  beforeDestroy () {
    window.removeEventListener('hashchange', this.hashHandler);
  },
  components: {
    InputItem,
    PasswordInput,
    ActionButton
  }
};
</script>

<style scoped>
.sign-in {
  position: fixed;
  top: 50%;
  transform: translate(-50%, -50%);
  left: 50%;
  margin: auto;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 24px;
}

h1 {
  font-size: 50px;
  font-weight: 600;
  text-align: center;
  line-height: 60px;
}

h2 {
  font-size: 25px;
  text-align: center;
  margin-bottom: 16px;
  line-height: 30px;
}

a {
  text-decoration: underline;
  font-weight: 600;
  cursor: pointer;
  color: var(--main-font);
}
</style>
