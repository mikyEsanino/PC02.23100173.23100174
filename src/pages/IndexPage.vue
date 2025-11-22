<template>
  <q-page class="digimon-page">
    <div class="row no-wrap full-height">
      <!-- 🟦 LADO IZQUIERDO: PANEL DE BÚSQUEDA -->
      <div class="col-12 col-md-4 search-side q-pa-md">
        <q-card class="search-card q-pa-md">
          <q-card-section>
            <div class="text-h6 text-primary text-weight-bold q-mb-xs">
              Explorador Digimon
            </div>
            <div class="text-caption text-grey-7">
              Filtra por nombre o nivel y explora tus Digimon favoritos.
            </div>
          </q-card-section>

          <q-separator inset />

          <q-card-section class="q-gutter-md">

            <!-- Buscar por nombre -->
            <q-input
              v-model="filtroNombre"
              outlined
              dense
              label="Buscar por nombre"
              placeholder="Ej: Agumon, Gabumon..."
              :clearable="true"
            >
              <template #prepend>
                <q-icon name="search" />
              </template>
            </q-input>

            <!-- Filtrar por nivel -->
            <q-select
              v-model="filtroNivel"
              :options="nivelesOptions"
              outlined
              dense
              label="Filtrar por nivel"
              emit-value
              map-options
              clearable
            >
              <template #prepend>
                <q-icon name="filter_list" />
              </template>
            </q-select>

            <div class="column q-gutter-sm">
              <q-btn
                label="Buscar"
                class="btn-primary full-width"
                @click="buscar"
              />
              <q-btn
                label="Ver todos"
                class="btn-secondary full-width"
                @click="verTodos"
              />
              <q-btn
                flat
                label="Limpiar filtros"
                class="btn-clear full-width"
                @click="limpiarFiltros"
              />
            </div>
          </q-card-section>
        </q-card>
      </div>

      <!-- 🟨 LADO DERECHO: IMAGEN + CARDS -->
      <div class="col-12 col-md-8 hero-side">
        <div class="hero-overlay q-pa-md">

          <!-- Loading -->
          <div v-if="cargando" class="flex flex-center q-mt-md">
            <q-spinner-dots color="yellow-7" size="2rem" />
            <span class="q-ml-sm text-white">Cargando Digimon...</span>
          </div>

          <!-- Cards solo cuando hay resultados -->
          <div v-if="mostrarResultados && !cargando" class="row q-col-gutter-md">
            <div
              v-for="digimon in digimonsFiltrados"
              :key="digimon.name"
              class="col-12 col-sm-6 col-md-4"
            >
              <q-card class="digi-card">
                <q-img
                  :src="digimon.img"
                  :alt="digimon.name"
                  ratio="1"
                  class="digi-card-img"
                />
                <q-card-section class="text-center">
                  <div class="text-subtitle1 text-primary text-weight-bold">
                    {{ digimon.name }}
                  </div>
                  <div class="text-caption text-grey-7">
                    Nivel: {{ digimon.level }}
                  </div>
                </q-card-section>
              </q-card>
            </div>

            <div
              v-if="digimonsFiltrados.length === 0"
              class="col-12 text-center text-white q-mt-lg"
            >
              No se encontraron Digimon con esos filtros.
            </div>
          </div>

          <!-- Si aún no buscó, no mostramos nada: solo la imagen limpia -->
        </div>
      </div>
    </div>
  </q-page>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import axios from 'axios'

const todosDigimons = ref([])
const digimonsFiltrados = ref([])
const cargando = ref(false)

const filtroNombre = ref('')
const filtroNivel = ref(null)
const nivelesOptions = ref([])

const mostrarResultados = ref(false)

onMounted(async () => {
  try {
    cargando.value = true
    const res = await axios.get('https://digimon-api.vercel.app/api/digimon')
    todosDigimons.value = res.data || []

    // niveles únicos -> opciones del select
    const niveles = Array.from(new Set(todosDigimons.value.map(d => d.level)))
    nivelesOptions.value = niveles.map(n => ({ label: n, value: n }))
  } catch (e) {
    console.error('Error cargando Digimon', e)
  } finally {
    cargando.value = false
  }
})

const digimonsFiltradosComputed = computed(() => {
  const nombre = filtroNombre.value.trim().toLowerCase()
  const nivel = filtroNivel.value

  return todosDigimons.value.filter(d => {
    const coincideNombre = nombre
      ? d.name.toLowerCase().includes(nombre)
      : true
    const coincideNivel = nivel ? d.level === nivel : true
    return coincideNombre && coincideNivel
  })
})

function buscar () {
  digimonsFiltrados.value = digimonsFiltradosComputed.value
  mostrarResultados.value = true
}

function verTodos () {
  digimonsFiltrados.value = todosDigimons.value
  mostrarResultados.value = true
}

function limpiarFiltros () {
  filtroNombre.value = ''
  filtroNivel.value = null
  digimonsFiltrados.value = []
  mostrarResultados.value = false
}
</script>

<style scoped>
/* Toda la pantalla */
.digimon-page {
  min-height: 100vh;
  background: #ffffff; /* sin negro */
}

/* Forzamos que la fila ocupe el alto completo */
.row.no-wrap.full-height {
  min-height: 100vh;
}

/* 🟦 LADO IZQUIERDO COMPLETO */
.search-side {
  background: #ffffff;
  display: flex;
  align-items: stretch;
  justify-content: stretch;
}

/* El panel de búsqueda ocupa toda la columna izquierda */
.search-card {
  width: 100%;
  height: 100%;
  max-width: none;
  background: rgba(255, 255, 255, 0.85);        /* cajas / inputs */
  border-radius: 20px;
  border: 2px solid #6C63FF;                    /* borde azul/violeta */
  color: #1B3B7A;                               /* texto principal */
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.12);   /* sombra suave */
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
}

/* Inputs */
.search-card .q-field__control {
  background: rgba(255, 255, 255, 0.95);
  border-radius: 10px;
  border: 1px solid #6C63FF;
}
.search-card .q-field__native,
.search-card .q-field__label {
  color: #1B3B7A;
}

/* Botones */
.btn-primary {
  background: #FFCA3A;  /* Amarillo Digimon */
  color: #1B3B7A;
  font-weight: 700;
  border-radius: 10px;
}
.btn-primary:hover {
  background: #FF9F1C;  /* Naranja suave */
}

.btn-secondary {
  background: #ffffff;
  color: #1B3B7A;
  font-weight: 600;
  border-radius: 10px;
  border: 1px solid #FFCA3A;
}
.btn-secondary:hover {
  background: #FFF3C4;
}

.btn-clear {
  color: #555555;
}

/* 🟨 LADO DERECHO: IMAGEN DIGIMON */
.hero-side {
  position: relative;
  min-height: 100vh;
  background: url('../assets/digimon-bg.jpg') center / cover no-repeat;
}

/* Contenedor para cards (no tapa la imagen si no hay resultados) */
.hero-overlay {
  position: relative;
  width: 100%;
  height: 100%;
  background: transparent;
  overflow-y: auto;
}

/* Cards de Digimon */
.digi-card {
  background: #ffffff;                          /* fondo blanco + sombra */
  border-radius: 18px;
  box-shadow: 0 4px 14px rgba(0, 0, 0, 0.2);
  transition: transform 0.18s ease, box-shadow 0.18s ease;
}
.digi-card-img {
  background: #f0f4ff;
}
.digi-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 10px 24px rgba(0, 0, 0, 0.3);
}
</style>
