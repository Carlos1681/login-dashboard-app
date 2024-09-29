<script setup>
import { reactive } from 'vue'
import axios from 'axios'
import { useRouter } from 'vue-router'
import { useToast } from 'vue-toastification'

const userInfo = reactive({
  name: '',
  password: ''
})

const router = useRouter()

const toast = useToast()

const handleLogin = async () => {
  if (userInfo.password.length < 4 || userInfo.name.length < 4) {
    toast.error('Nome e Senha devem ter mais de 4 digitos.')
    return
  }
  try {
    const response = await axios.post('http://localhost:8000/login', {
      name: userInfo.name,
      password: userInfo.password
    })
    const token = response.data.accessToken
    localStorage.setItem('accessToken', token)
    console.log(response.data.message)
    router.push('/dash')
    toast.success('Login efetuado com sucesso.')
  } catch (error) {
    console.error('Erro ao realizar login', error)
    toast.error('Nome ou Senha incorretos.')
  }
}
</script>

<template>
  <main class="d-flex justify-content-center align-items-center vh-100 bg-light">
    <section class="card shadow-lg p-4" style="max-width: 400px; border-color: #4b2c85">
      <div class="card-body">
        <h1 class="card-title text-center mb-4" style="color: #4b2c85">Login</h1>
        <form @submit.prevent="handleLogin">
          <div class="mb-3">
            <label for="name" class="form-label">Nome</label>
            <input
              type="text"
              v-model="userInfo.name"
              class="form-control"
              id="name"
              placeholder="Digite seu nome"
              required
            />
          </div>
          <div class="mb-3">
            <label for="password" class="form-label">Senha</label>
            <input
              type="password"
              v-model="userInfo.password"
              class="form-control"
              id="password"
              placeholder="Digite sua senha"
              required
            />
          </div>
          <div class="d-grid">
            <button
              type="submit"
              class="btn btn-primary"
              style="background-color: #22d87a; border-color: #22d87a"
            >
              Entrar
            </button>
          </div>
        </form>
      </div>
    </section>
  </main>
</template>
