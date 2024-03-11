<script setup>
import {ref, reactive, onMounted, watch} from "vue";
import {uid} from 'uid'
import Header from "./components/Header.vue";
import Formulario from "./components/Formulario.vue";
import Paciente from "./components/Paciente.vue";

const pacientes = ref([])

const paciente = reactive({
  id: null,
  nombre:'',
  propietario:'',
  email:'',
  alta:'',
  sintomas:''
})

watch(pacientes, () => {
  guardarLocalStorage()
}, {
  deep: true
})

onMounted(() => {
  const pacientesStorage = localStorage.getItem('itemsPacientes');
  if(pacientesStorage) {
    pacientes.value = JSON.parse(pacientesStorage)
  }
})

const guardarLocalStorage = () => {
  localStorage.setItem('itemsPacientes', JSON.stringify(pacientes.value))
}

const guardarPaciente = () => {
  if(paciente.id) {
    const index = pacientes.value.findIndex(p => p.id === paciente.id)
    pacientes.value[index] = {...paciente}
  } else {
    pacientes.value.push({
      ...paciente,
      id: uid()
    })
  }

  Object.assign(paciente, {
    nombre:'',
    propietario:'',
    email:'',
    alta:'',
    sintomas:''
  })
}

const editarPaciente = (id) => {
  const index = pacientes.value.findIndex(p => p.id === id)

  Object.assign(paciente, {
    nombre:pacientes.value[index].nombre,
    propietario:pacientes.value[index].propietario,
    email:pacientes.value[index].email,
    alta:pacientes.value[index].alta,
    sintomas:pacientes.value[index].sintomas,
    id:pacientes.value[index].id,
  })
}

const eliminarPaciente = (id) => {
  pacientes.value = pacientes.value.filter(paciente => paciente.id !== id)
}
</script>

<template>
  <div class="container mx-auto mt-20">
    <Header/>
    <div class="mt-12 md:flex">
      <Formulario
          v-model:nombre="paciente.nombre"
          v-model:propietario="paciente.propietario"
          v-model:email="paciente.email"
          v-model:alta="paciente.alta"
          v-model:sintomas="paciente.sintomas"
          :id="paciente.id"
          @guardar-paciente="guardarPaciente"
      />
      <div class="md:w-1/2 md:h-screen overflow-y-scroll">
        <h3 class="font-black text-3xl text-center">Administra tus pacientes</h3>

        <div v-if="pacientes.length > 0">
          <p class="text-lg mt-5 text-center mb-10">
            Información de
            <span class="text-indigo-600 font-bold">pacientes</span>
          </p>
          <Paciente
              v-for="paciente in pacientes"
              :paciente="paciente"
              @editar-paciente="editarPaciente"
              @eliminar-paciente="eliminarPaciente"
          />
        </div>
        <p v-else class="mt-10 text-2xl text-center mt-20">No hay pacientes</p>
      </div>
    </div>
  </div>
</template>
