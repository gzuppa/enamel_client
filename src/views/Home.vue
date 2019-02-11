<template>
  <el-container>
    <el-header>
      <navigation></navigation>
    </el-header>

    <el-main>
      <div class="container-center">
        <h2>Bienvenido a la plataforma de mantenimiento Meypar!</h2>
        <div>Ingresa tu correo electrónico para registrarte por primera ocasión</div>

        <div v-if="error" class="error">
          {{ error }}
        </div>

        <el-form ref="form" :model="form" @submit.native.prevent="capture">
          <el-form-item>
            <label>Email</label>
            <el-input v-model="form.email" placeholder="Email"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="capture">Registrarme en la plataforma</el-button>
          </el-form-item>
        </el-form>

        <div>
          <span>¿Ya estás registrado?</span>
          <router-link :to="{name: 'login'}" class="link">Log in</router-link>
        </div>

        <div v-if="submitted">
          <div>¡Muchas gracias!</div>
          <div>Por favor revisa tu bandeja de entrada (o spam) para confirmar tu cuenta</div>
        </div> 
      </div>

    </el-main>
  </el-container>

</template>

<script>
import { mapState } from 'vuex'
import { CaptureEmail } from '../constants/query.gql'
import { validateEmail } from '@/helpers/helpers'

export default {
  data() {
    return {
      submitted: false,
      error: false,
      form: {
        email: '',
      }
    }
  },
  computed: {
    ...mapState(['userId'])
  },
  methods: {
    async capture() {
      const {email} = this.form
      if (!email || !validateEmail(email)) {
        this.error = 'Ingresa un correo válido'
        return
      }
      this.$apollo.mutate({
        mutation: CaptureEmail,
        variables: {email}
      }).then(({data}) => {
        this.submitted = true
        this.error = false
        // this.id = data.captureEmail.id
      }).catch((error) => {
        if (error.graphQLErrors.length >= 1) {
          this.error = error.graphQLErrors[0].message            
        } else {
          this.error = 'Algo salió mal'
        }
        console.log(error)
      })

    },  
  }
}
</script>

<style scoped lang="scss">
.el-button {
  width: 100%;
}

.error {
  padding-top: 10px;
}

</style>