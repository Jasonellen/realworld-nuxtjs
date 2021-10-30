<template>
  <div class="settings-page">
    <div class="container page">
      <div class="row">

        <div class="col-md-6 offset-md-3 col-xs-12">
          <h1 class="text-xs-center">Your Settings</h1>

          <form @submit.prevent="submit">
            <fieldset>
                <fieldset class="form-group">
                  <input v-model="userUse.image" class="form-control" type="text" placeholder="URL of profile picture">
                </fieldset>
                <fieldset class="form-group">
                  <input v-model="userUse.username" class="form-control form-control-lg" type="text" placeholder="Your Name">
                </fieldset>
                <fieldset class="form-group">
                  <textarea v-model="userUse.bio" class="form-control form-control-lg" rows="8" placeholder="Short bio about you"></textarea>
                </fieldset>
                <fieldset class="form-group">
                  <input v-model="userUse.email" class="form-control form-control-lg" type="text" placeholder="Email">
                </fieldset>
                <fieldset class="form-group">
                  <input v-model="userUse.password" class="form-control form-control-lg" type="password" placeholder="Password">
                </fieldset>
                <button class="btn btn-lg btn-primary pull-xs-right">
                  Update Settings
                </button>
            </fieldset>
          </form>
          <button class="btn btn-outline-danger" @click="logout">
            Or click here to logout.
          </button>
        </div>

      </div>
    </div>
  </div>
</template>

<script>
import { updateUser} from "../../api/user";
import {mapState} from "vuex";

const Cookie = process.client ? require('js-cookie') : undefined
export default {
  middleware: 'authenticated',
  name: 'SettingsIndex',
  data(){
    return {
      userUse:{},
    }
  },
  computed: {
    ...mapState(['user']),
  },
  mounted() {
    this.userUse = {...this.user}
  },
  methods:{
    async submit(){
      const { data } = await updateUser({user:this.userUse})
      this.$store.commit('setUser', data.user)
      Cookie.set('user', JSON.stringify(data.user))
      alert("更新成功")
    },
    logout(){
      Cookie.set('user', '')
      location.href = '/'
    },
  }
}
</script>

<style>

</style>
