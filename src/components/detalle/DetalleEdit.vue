<script setup lang="ts">
import { onMounted, ref } from 'vue'
import http from '@/plugins/axios'
import router from '@/router'
import type { Pedido } from '@/models/pedido'
import type { Cliente } from '@/models/cliente'

var pedido = ref<Pedido[]>([])
async function getPedidos() {
  pedido.value = await http.get('pedido').then((response) => response.data)
}

onMounted(() => {
  getPedidos()
})

var cliente = ref<Cliente[]>([])
async function getCliente() {
  cliente.value = await http.get('clientes').then((response) => response.data)
}

onMounted(() => {
  getCliente()
})

const props = defineProps<{
  ENDPOINT_API: string
}>()

const ENDPOINT = props.ENDPOINT_API ?? ''
const direccionEstado = ref('')
const puntuacion = ref('')
const credibilidad = ref('')
const amabilidad = ref('')
const idCliente = ref('')
const idPedido = ref('')
const id = router.currentRoute.value.params['id']

async function editarDetalle() {
  await http
    .patch(`${ENDPOINT}/${id}`, {
      direccionEstado: direccionEstado.value,
      puntuacion: puntuacion.value,
      credibilidad: credibilidad.value,
      amabilidad: amabilidad.value,
      idCliente: idCliente.value,
      idPedido: idPedido.value
    })
    .then(() => router.push('/detalles'))
}

async function getDetalle() {
  await http.get(`${ENDPOINT}/${id}`).then((response) => {
    ;(direccionEstado.value = response.data.direccionEstado),
      (puntuacion.value = response.data.puntuacion),
      (credibilidad.value = response.data.credibilidad),
      (amabilidad.value = response.data.amabilidad),
      (idCliente.value = response.data.idCliente),
      (idPedido.value = response.data.idPedido)
  })
}

function goBack() {
  router.go(-1)
}

onMounted(() => {
  getDetalle()
})
</script>

<template>
  <br /><br /><br />
  <div class="container">
    <nav aria-label="breadcrumb">
      <ol class="breadcrumb">
        <li class="breadcrumb-item"><RouterLink to="/">Inicio</RouterLink></li>
        <li class="breadcrumb-item">
          <RouterLink to="/detalles">Detalles</RouterLink>
        </li>
        <li class="breadcrumb-item active" aria-current="page" style="color: black">
          Editar Detalle
        </li>
      </ol>
    </nav>

    <div class="row">
      <h2>Editar Detalles del Pedido</h2>
    </div>

    <div class="row">
      <form @submit.prevent="editarDetalle">
        <div class="form-floating mb-3">
          <input
            type="text"
            class="form-control"
            v-model="direccionEstado"
            placeholder="DireccionEstado"
            required
          />
          <label for="direccionEstado">Estado de la Dirección</label>
        </div>

        <div class="form-floating mb-3">
          <input
            type="text"
            class="form-control"
            v-model="puntuacion"
            placeholder="Puntuacion"
            required
          />
          <label for="puntuacion">Puntuación</label>
        </div>

        <div class="form-floating mb-3">
          <input
            type="text"
            class="form-control"
            v-model="credibilidad"
            placeholder="Credibilidad"
            required
          />
          <label for="credibilidad">Credibilidad</label>
        </div>

        <div class="form-floating mb-3">
          <input
            type="text"
            class="form-control"
            v-model="amabilidad"
            placeholder="Amabilidad"
            required
          />
          <label for="amabilidad">Amabilidad</label>
        </div>

        <div class="form-floating mb-3">
          <select v-model="idCliente" class="form-select">
            <option v-for="clientes in cliente" :value="clientes.id">
              {{ clientes.nombreCliente }}
            </option>
          </select>
          <label for="clientes">Nombre del Cliente</label>
        </div>

        <div class="form-floating mb-3">
          <select v-model="idPedido" class="form-select">
            <option v-for="pedidos in pedido" :value="pedidos.id">
              {{ pedidos.fechaPedido }}
            </option>
          </select>
          <label for="pedidos">Fecha del Pedido</label>
        </div>

        <div class="text-center mt-3">
          <button type="submit" class="btn btn-primary btn-lg">
            <font-awesome-icon icon="fa-solid fa-floppy-disk" /> Guardar Detalle
          </button>
        </div>
      </form>
    </div>
    <div class="text-left">
      <button class="btn btn-link" @click="goBack">Volver</button>
    </div>
  </div>
</template>

<style></style>
