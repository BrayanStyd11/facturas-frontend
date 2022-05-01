<template>
    <div class="container">
        <div class="card">
            <div class="card-header">
                Productos
            </div>            
            <div class="card-body">
                <div class="row">
                    <div class="col">
                        <button class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#exampleModal">
                            Crear Producto
                        </button>
                    </div>
                    <div class="col mb-3">                            
                        <input type="text" class="form-control" placeholder="Buscar" v-model="filter">
                    </div>
                </div>
                <table class="table table-striped table-bordered">
                    <thead>
                        <tr>
                            <th>Nombre</th>
                            <th>Descripción</th>
                            <th>Valor Unitario</th>
                            <th>Acción</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(product, key) in filteredRows" :key="key">
                            <td>{{product.name}}</td>
                            <td>{{product.description}}</td>
                            <td>{{product.unit_value}}</td>
                            <td>
                                <button class="btn btn-primary" data-bs-toggle="modal" v-on:click="editProduct(product)" data-bs-target="#exampleModal">Editar</button>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
        <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="exampleModalLabel">{{id===null ? 'Creación de Producto': 'Actualización de Producto'}}</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">
                        <div class="form-group">
                            <label for="name">Nombre</label>
                            <input v-model="product.name" type="text" name="name" id="name" class="form-control">
                        </div>

                        <div class="form-group">
                            <label for="NIT_emitter">Descripción</label>
                            <input v-model="product.description" type="text" name="NIT_emitter" id="NIT_emitter" class="form-control">
                        </div>

                        <div class="form-group">
                            <label for="name_buyer">Valor Unitario</label>
                            <input v-model="product.unit_value" type="text" name="name_buyer" id="name_buyer" class="form-control">
                        </div>                        
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cerrar</button>
                        <button type="button" class="btn btn-primary" v-on:click="saveProduct" data-bs-dismiss="modal">Guardar</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
export default{
    name:'ProductsComponent',
    props:['token','refreshToken'],
    data() {
        return{
            backendUrl: process.env.VUE_APP_BACKEND_URL,
            products:[],
            product:{},
            id:null,
            filter:'',
        }    
    },
    mounted(){        
        this.getProducts();
    },
    methods:{
        getProducts(){
            let headers = {
                Authorization: this.token
            };
            this.$axios.get(`${this.backendUrl}/products`,{headers}).then((response)=>{
                this.products = response.data.products;
            }).catch((error)=>{
                if(error.response.data.message === 'Token has expired'){
                    this.refreshToken();                    
                }
            })
        },
        editProduct(data){
            this.product = data;
            this.id = data.id;
        },
        saveProduct(){
            if(this.id === null){
                let headers = {
                    Authorization: this.token
                };
                this.$axios.post(`${this.backendUrl}/products`, this.product,{headers}).then((response)=>{
                    this.$toastr.s(response.data.message);
                    this.cleanData();
                }).catch((error)=>{
                    if(error.response.data.message === 'Token has expired'){
                        this.refreshToken();                    
                    }
                })
            }else{
                let headers = {
                    Authorization: this.token
                };
                this.$axios.put(`${this.backendUrl}/products/${this.id}`, this.product,{headers}).then((response)=>{
                    this.$toastr.s(response.data.message);
                    this.cleanData();
                }).catch((error)=>{
                    if(error.response.data.message === 'Token has expired'){
                        this.refreshToken();                    
                    }
                })
            }
        },
        cleanData(){
            this.getProducts();
            this.product = {};
            this.id = null;
        },
    },
    computed:{
        filteredRows(){
            if(!this.products.filter) return false;

            return this.products.filter((row) => {
                const name = row.name.toString().toLowerCase();
                const searchTerm = this.filter.toLowerCase();
                let name_invoice = name.includes(searchTerm);
                if(name_invoice === true){
                    return name.includes(searchTerm);
                }else {    
                    const description = row.description.toString().toLowerCase();
                    const searchTermName = this.filter.toLowerCase();
                                    
                    let desc = description.includes(searchTermName);
                    if(desc === true){
                        return description.includes(searchTermName);
                    }else{
                        const unit_value = row.unit_value.toString().toLowerCase();
                        const searchTermName = this.filter.toLowerCase();
                                        
                        let unit_val = unit_value.includes(searchTermName);
                        if(unit_val === true){
                            return unit_value.includes(searchTermName);
                        }
                    }
                }
            })
        }
    }
}
</script>
