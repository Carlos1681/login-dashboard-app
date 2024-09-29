<script setup>
import { reactive, defineProps, computed, onMounted } from 'vue'
import axios from 'axios'
import { useRouter, useRoute } from 'vue-router'
import PulseLoader from 'vue-spinner/src/PulseLoader.vue'

const props = defineProps({
  totalPages: {
    type: Number,
    required: true
  }
})
const tableData = reactive({
  serviceData: [],
  nameSearch: '',
  statusFilter: '',
  isLoading: true
})

const currentPage = reactive({
  pageNum: 1
})

const route = useRoute()
const router = useRouter()

const fetchService = async (page) => {
  if (page < 1 || page > props.totalPages) return

  currentPage.pageNum = page

  const token = localStorage.getItem('accessToken')

  try {
    const response = await axios.get(`http://localhost:8000/dashboard/${page}`, {
      headers: {
        Authorization: `Bearer ${token}`
      }
    })
    const pageNumber = `page${page}`
    tableData.serviceData = response.data[pageNumber]
  } catch (error) {
    console.error('Erro ao obter dados de serviços:', error)
  } finally {
    tableData.isLoading = false
  }
}

const filteredServices = computed(() => {
  let result = tableData.serviceData

  if (tableData.statusFilter) {
    result = result.filter((servData) => servData.statusService === tableData.statusFilter)
  }

  if (tableData.nameSearch) {
    result = result.filter((servData) =>
      servData.name.toLowerCase().includes(tableData.nameSearch.toLowerCase())
    )
  }

  return result
})

const changePage = (page) => {
  if (page !== currentPage.pageNum) {
    fetchService(page)
  }
}

onMounted(() => {
  fetchService(parseInt(route.params.page) || 1)
})

const logout = () => {
  localStorage.removeItem('accessToken')
  router.push('/')
}
</script>

<template>
  <main class="d-flex justify-content-center align-items-center vh-100 bg-light">
    <section class="card shadow-lg p-4" style="max-width: 1000px; border-color: #4b2c85">
      <div class="card-body">
        <h1 class="card-title text-center mb-4" style="color: #4b2c85">Serviços Realizados</h1>

        <div class="d-flex justify-content-between mb-3">
          <input
            type="text"
            v-model="tableData.nameSearch"
            placeholder="Filtrar por nome"
            class="form-control w-50"
          />

          <select v-model="tableData.statusFilter" class="form-select w-50">
            <option value="">Todos os Status</option>
            <option value="in progress">Em Andamento</option>
            <option value="Concluded">Concluído</option>
          </select>
        </div>

        <div class="table-responsive" style="max-height: 300px; overflow-y: auto">
          <table class="table table-striped table-hover">
            <thead class="table-dark" style="background-color: #4b2c85">
              <tr>
                <th scope="col">Ordem de Serviço</th>
                <th scope="col">Nome</th>
                <th scope="col">Matrícula</th>
                <th scope="col">Serviço</th>
                <th scope="col">Status do Serviço</th>
              </tr>
            </thead>
            <div v-if="tableData.isLoading" class="text-center" style="margin-top: 50px">
              <PulseLoader />
            </div>
            <tbody>
              <tr v-for="servData in filteredServices" :key="servData.ordemServiceNumber">
                <td>{{ servData.ordemServiceNumber }}</td>
                <td>{{ servData.name }}</td>
                <td>{{ servData.registration }}</td>
                <td>{{ servData.service }}</td>
                <td>{{ servData.statusService }}</td>
              </tr>
            </tbody>
          </table>
        </div>

        <nav aria-label="Navegar pelas páginas">
          <ul class="pagination justify-content-center">
            <li class="page-item" :class="{ disabled: currentPage.pageNum === 1 }">
              <a class="page-link" href="#" @click.prevent="changePage(currentPage.pageNum - 1)">
                Anterior
              </a>
            </li>
            <li
              v-for="page in props.totalPages"
              :key="page"
              class="page-item"
              :class="{ active: page === currentPage.pageNum }"
            >
              <a class="page-link" href="#" @click.prevent="changePage(page)">{{ page }}</a>
            </li>
            <li class="page-item" :class="{ disabled: currentPage.value === props.totalPages }">
              <a class="page-link" href="#" @click.prevent="changePage(currentPage.pageNum + 1)">
                Próximo
              </a>
            </li>
          </ul>
        </nav>

        <div class="d-grid mt-4 justify-content-center">
          <button
            class="btn btn-primary"
            style="background-color: #22d87a; border-color: #22d87a; width: 150px"
            @click="logout"
          >
            Sair
          </button>
        </div>
      </div>
    </section>
  </main>
</template>

<style>
table {
  width: 100%;
  table-layout: auto;
}

thead th {
  text-align: center;
  word-wrap: break-word;
}

tbody td {
  text-align: center;
  word-wrap: break-word;
}

.table-striped tbody tr:nth-child(odd) {
  background-color: #f9f9f9;
}

.table-hover tbody tr:hover {
  background-color: #ececec;
}

.table-dark th {
  background-color: #4b2c85;
  color: white;
}

tbody {
  display: block;
  min-height: 300px;
  overflow-y: auto;
}

thead,
tbody tr {
  display: table;
  width: 100%;
  table-layout: fixed;
}
</style>
