<script setup>
import { ref, watch } from 'vue'

const text = ref('')
const tarea_txt= ref('');
const prioridad= ref('');

// const lista_tareas = ref [
// const hideCompleted = ref(false)
const mostrarSoloPendientes = ref(false)
const lista_tareas = ref([
        {'id':0, 'tarea': 'Ejemplo de tarea 1', 'prioridad': 'N', done: true},
        {'id':1, 'tarea': 'Ejemplo de tarea 2', 'prioridad': 'I', done: true},
        {'id':2, 'tarea': 'Ejemplo de tarea 3', 'prioridad': 'M', done: false},]);

const lista_tareas_original = ref([
        {'id':0, 'tarea': 'Ejemplo de tarea 1', 'prioridad': 'N', done: true},
        {'id':1, 'tarea': 'Ejemplo de tarea 2', 'prioridad': 'I', done: true},
        {'id':2, 'tarea': 'Ejemplo de tarea 3', 'prioridad': 'M', done: false},]);

const search_text= ref('');
const error_msg= ref('');

function onInput(e) {
  text.value = e.target.value
};

watch(search_text, (newSearchText) => { FiltrarTareasPorNombreMasEstado()} )
watch(mostrarSoloPendientes, (mostrarSoloPendientes) => FiltrarTareasPorNombreMasEstado())

function FiltrarTareasPorNombre(searchText) {
      if (searchText.trim() === '') {
        lista_tareas.value = [...lista_tareas_original.value];
      } else {
        lista_tareas.value = lista_tareas_original.value.filter(unaTarea => {
          return unaTarea.tarea.toLowerCase().indexOf(searchText.toLowerCase()) >= 0;
        });
      }
    };

  function FiltrarTareasPorNombreMasEstado() {
      let filtroNombreTarea = search_text.value;
      let filtrarSoloPendientes = mostrarSoloPendientes.value; 

      if (filtroNombreTarea.trim() === '') {
        lista_tareas.value = [...lista_tareas_original.value];
      } else {
        lista_tareas.value = lista_tareas_original.value.filter(unaTarea => {
          return unaTarea.tarea.toLowerCase().indexOf(filtroNombreTarea.toLowerCase()) >= 0;
        });
      }

      if (filtrarSoloPendientes) {
        lista_tareas.value = lista_tareas.value.filter(unaTarea => { return unaTarea.done == false;});
      }
    };    

function AgregarTareas(){
  if (tarea_txt.value.trim() === ''|| prioridad.value.trim() === '') {
    error_msg.value = 'Debe ingresar tarea y prioridad';
    return;
  }
  const tarea = {
    id : lista_tareas.value.length,
    tarea : tarea_txt.value,
    prioridad : prioridad.value
  };
  lista_tareas.value.push(tarea);
  lista_tareas_original.value.push(tarea);
  tarea_txt.value = '';
  prioridad.value = '';
  error_msg.value = '';
}

function EliminarTarea(tarea_id){
  const index = lista_tareas.value.findIndex(e => e.id === tarea_id);
  lista_tareas.value.splice(index, 1);
  lista_tareas_original.value.splice(index, 1);
}
// const mostrarActivos = computed(() => {
//    return hideCompleted.value
//     ? lista_tareas_original.value.filter((t) => !t.done)
//      : lista_tareas_original.value
// })



</script>

<template>
  <body>
    
  </body>
  <div>
       <section class="hero is-success">
            <div class="hero-body">
                <div class="container">
                    <p class="title">Mis tareas</p>
                    <p class="subtitle">Organizador de tareas diarias</p>
                    <h6>version Vue Js 3</h6>
                </div>
            </div>
         </section>
                <div class="columns is-mobile" style="padding: 2%;" >
          <div class="column" id="columna1">
            <section class="hero">
              <div class="hero-body">
                  <div class="container">
                      <label class="label">Registra tu tarea</label>
                      <div class="field">
                        <label class="label">Tarea</label>
                        <div class="control">
                          <input class="input" type="text" 
                                 placeholder="ingrese tarea"
                                 v-model="tarea_txt">   
                        </div>
                      </div>
                      <div>
                        <p v-if="error_msg" style="color: red;">{{ error_msg }}</p>
                      </div>
                      <div class="field">
                        <label class="label">Prioridad</label>
                        <div class="control">
                          <div class="select">
                            <select v-model="prioridad">
                              <option value="N">Normal</option>
                              <option value="I">Importante</option>
                              <option value="M">Muy importante</option>
                            </select>
                          </div>
                        </div>
                      </div>
                      <div class="buttons">
                        <button @click="AgregarTareas()" class="button is-success is-responsive is-small">Agregar</button>
                      </div>
                  </div>
              </div>
          </section>
          </div>
          <div class="column" id="columna2">
            <section class="hero">
              <div class="hero-body">
                  <div class="container">
                    <label class="label">Lista de tareas</label>
                  </div>
                  <div class="field">
                    <div class="control">
                      <input class="input" type="text" 
                      v-model="search_text"
                      placeholder="Filtrar tarea">
                    </div>
                  </div>
                  <div class="field">
                    <div class="control">
                      <label class="checkbox">
                        <input type="checkbox"  v-model="mostrarSoloPendientes">
                           Mostrar solo tareas pendientes
                      </label>
                    </div>
                  </div>
                  <table class="table is-responsive">
                    <thead>
                        <th>Completada</th>
                        <th>Tarea</th>
                        <th>Prioridad</th>
                        <th>Acciones</th>
                    </thead>
                    <tbody>
                      <tr v-for="i in lista_tareas" :key="i">
                        <td>
                          <div class="field">
                            <div class="control">
                              <label class="checkbox">
                                <input type="checkbox" v-model="i.done">
                              </label>
                            </div>
                          </div>
                        </td>
                        <td><span :class="{ done: i.done }">{{ i.tarea }}</span></td>
                        <td>
                          <span v-if="i.prioridad === 'N'" class="tag is-info">Normal</span>
                          <span v-else-if="i.prioridad === 'I'" class="tag is-warning">Importante</span>
                          <span v-else class="tag is-danger">Muy importante</span>
                        </td>
                        <td>
                          <button @click="EliminarTarea(i.id)" class="button is-danger is-dark is-small">Eliminar</button>
                        </td>
                      </tr>
                    </tbody>
                  </table>
              </div>
          </section>
          </div>
        </div>
  </div>
</template>

<style>
.done {
  text-decoration: line-through;
}

/* header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
} */
</style>
