<template>
    <div>

        <form @submit.prevent="editarempleado(empleado)" v-if="editarActivo">
            <h3>Editar Salones</h3>
            <input type="text" placeholder="Documento" class="form-control mb-2" v-model="empleado.doc">
            <input type="text" placeholder="Nombres" class="form-control mb-2" v-model="empleado.nombres">
            <input type="text" placeholder="Apellidos" class="form-control mb-2" v-model="empleado.apellidos">
            <input type="text" placeholder="Telefono" class="form-control mb-2" v-model="empleado.telefono">
            <input type="text" placeholder="Cargo" class="form-control mb-2" v-model="empleado.cargo">
            <input type="text" placeholder="Salario" class="form-control mb-2" v-model="empleado.salario">
            <button class="btn btn-warning" type="submit">Guardar Cambios</button>
        </form>

        <form @submit.prevent="agregar" v-else>
            <h3>Agregar Empleados</h3>
            <input type="text" placeholder="Documento" class="form-control mb-2" v-model="empleado.doc">
            <input type="text" placeholder="Nombres" class="form-control mb-2" v-model="empleado.nombres">
            <input type="text" placeholder="Apellidos" class="form-control mb-2" v-model="empleado.apellidos">
            <input type="text" placeholder="Telefono" class="form-control mb-2" v-model="empleado.telefono">
            <input type="text" placeholder="Cargo" class="form-control mb-2" v-model="empleado.cargo">
            <input type="text" placeholder="Salario" class="form-control mb-2" v-model="empleado.salario">
            <button class="btn btn-primary" type="submit">Insertar</button>
        </form>

        <hr class="mt-4">
        <h3>Lista de Empleados</h3>
        <ul class="list-group my-2">
            
            <li class="list-group-item" v-for="(item, index) in empleados" :key="index">
                <p>{{item.doc}}</p>
                <p>{{item.nombres}}</p>
                <p>{{item.apellidos}}</p>
                <p>{{item.telefono}}</p>
                <p>{{item.cargo}}</p>
                <p>{{item.salario}}</p>
                <button class="btn btn-danger btn-sm" @click="eliminarempleado(item, index)"> Eliminar </button>
                <button class="btn btn-warning btn-sm" @click="editarformulario(item, index)"> Editar </button>
            </li>

        </ul>
    </div>
</template>

<script>
export default {
    data(){
        return{
            empleados: [],
            empleado: {doc: '', nombres: '',apellidos: '', telefono: '',cargo: '', salario: ''},
            editarActivo: false
        }
    },
    created(){
        axios.get('/empleados').then( res => {
            this.empleados = res.data;
        })
    },
    methods:{
        editarformulario(item){
            this.editarActivo=true;
            this.empleado.doc = item.doc;
            this.empleado.nombres = item.nombres;
            this.empleado.apellidos = item.apellidos;
            this.empleado.telefono = item.telefono;
            this.empleado.cargo = item.campos;
            this.empleado.salario = item.salario;
            this.empleado.id = item.id;
            },
        editarempleado(item){
            const params = {doc: item.doc, nombres: item.nombres, apellidos: item.apellidos, telefono: item.telefono, cargo: item.cargo, salario: item.salario};
            axios.put(`/empleados/${item.id}`, params).then(res => {
                const index = this.empleados.findIndex(empleadoBuscar => empleadoBuscar.id === res.data.id)

                this.empleado[index] = res.data;

                axios.get('/empleados').then(res => {
                        this.empleados = params;
                    })
            })
        },
        agregar(){
            if(this.empleado.doc.trim() === '' || this.empleado.nombres.trim() === '' || this.empleado.apellidos.trim() === ''|| this.empleado.telefono.trim() === ''|| this.empleado.cargo.trim() === ''|| this.empleado.salario.trim() === ''){
                alert ('Los campos no pueden estar vacíos.');
                return;    
            }
            //console.log(this.empleado.doc, this.empleado.nombres);
            const params = {doc: this.empleado.doc, nombres: this.empleado.nombres, apellidos: this.empleado.apellidos, telefono: this.empleado.telefono, cargo: this.empleado.cargo, salario: this.empleado.salario }
            this.empleado.doc='';
            this.empleado.nombres='';
            this.empleado.apellidos='';
            this.empleado.telefono='';
            this.empleado.cargo='';
            this.empleado.salario='';
            
            axios.post('/empleados', params).then( res => {
                    this.empleados.push(res.data)
            })
         },
         eliminarempleado(item, index){
             axios.delete(`/empleados/${item.id}`).then(() =>{
                 this.empleados.splice(index,1);
             })
        }     
    }
}
</script>