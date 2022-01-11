<template>
    <div>
        <h1 class="display-4 text-center">Listado de Tareas</h1>
        <hr>
        <div class="col-lg-8 offset-lg-2">
            <div class="card mt-4">
                <div class="card-body">
                    <div class="input-group mb-3">
                        <input type="text" v-model="tarea"
                            class="form-control form-control-lg" placeholder="Agregar Tarea">
                        <div class="input-group-append">
                            <button v-on:click="agregarTarea()"
                                class="btn btn-success btn-lg">Agregar</button>
                        </div>
                    </div> 
                    <br>
                    <div class="text-center">
                        <div v-if="loading" class="spinner-border text-success" role="status">
                            <span class="visually-hidden">Cargando...</span>
                        </div>
                    </div>
                    <h5 v-if="this.listTareas.length == 0">No hay tareas para realizar</h5> 
                    <ul v-if="loading == false" class="list-group">
                        <li v-for="(tarea, index) of listTareas" :key="index" 
                            class="list-group-item d-flex justify-content-between">
                            <span v-on:click="editarTarea(tarea.id)" v-bind:class="{'text-success' : tarea.estado}">
                                <i v-bind:class="[tarea.estado ? 'fas fa-check-circle' : 'far fa-circle']"></i>

                            </span>
                            {{tarea.nombre}}
                            <span v-on:click="eliminarTarea(tarea.id)"
                                class="text-danger cursor">
                                <i class="fas fa-trash-alt"></i>
                            </span>
                        </li>
                    </ul>
                </div>
            </div>
        </div> 
    </div>
</template>

<script>
    import axios from "axios";
    const URL = "https://tareasrcc.azurewebsites.net/api/tareas/"
    export default {
        name: 'Tarea',
        data() {
            return {
                tarea: '',
                listTareas: [],
                loading: false
            }
        },
        methods: {
            agregarTarea() {
                const tarea = {
                    nombre: this.tarea,
                    estado: false
                }
                this.loading = true;
                axios.post(URL, tarea).then(response => {
                    console.log(response);
                    this.obtenerTareas();
                }).catch(error => {
                    console.error(error);
                }).finally(this.loading = false);
                this.tarea = '';
            },
            eliminarTarea(id){
                axios.delete(URL + id)
                .then(response => {
                    console.log(response);
                    this.obtenerTareas();
                }).catch(error => console.error(error));
            },
            editarTarea(id){
                this.loading = true;
                axios.put(URL + id)
                .then(response => {
                    console.log(response);
                    this.obtenerTareas();
                    
                }).catch(error => console.error(error))
                    .finally(this.loading = false);
            },
            obtenerTareas(){
                
                axios.get(URL).then(response => {
                    console.log(response);
                    this.listTareas = response.data;
                })
            }
        },
        created: function () {
            this.obtenerTareas();
        }
    }
</script>

<style scoped>
.cursor{
    cursor:pointer;
}
</style>