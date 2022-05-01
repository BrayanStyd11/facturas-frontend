<template>
    <div class="container">
        <div class="card">
            <div class="card-header">
                Facturas
            </div>            
            <div class="card-body">
                <div class="row">
                    <div class="col">
                        <button class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#exampleModal">
                            Crear Factura
                        </button>
                    </div>
                    <div class="col mb-3">                            
                        <input type="text" class="form-control" placeholder="Buscar" v-model="filter">
                    </div>
                </div>
                <table class="table table-striped table-bordered table-responsive table-hover">
                    <thead>
                        <tr>
                            <th>#</th>
                            <th>Vendedor</th>                            
                            <th>Comprador</th>                            
                            <th>Fecha de la compra</th>                       
                            <th>Total</th>
                            <th>Acciones</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(invoice, key) in filteredRows" :key="key">
                            <td>{{invoice.id}}</td>
                            <td>{{invoice.name_emitter}}</td>                            
                            <td>{{invoice.name_buyer}}</td>                            
                            <td>{{formatDate(invoice.date_hourly)}}</td>                                               
                            <td>{{invoice.total_value}}</td>
                            <td>
                                <button class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#detailModal" v-on:click="detail(invoice)">Detalle</button>
                                <button class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#exampleModal" v-on:click="editData(invoice)">Editar</button>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>  
        <!-- Modal -->
        <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="exampleModalLabel">{{id===null ? 'Creación de Factura': 'Actualización de Factura'}}</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">
                        <div class="form-group">
                            <label for="name_emitter">Vendedor</label>
                            <input v-model="invoice.name_emitter" type="text" name="name_emitter" id="name_emitter" class="form-control">
                        </div>

                        <div class="form-group">
                            <label for="NIT_emitter">NIT Vendedor</label>
                            <input v-model="invoice.NIT_emitter" type="text" name="NIT_emitter" id="NIT_emitter" class="form-control">
                        </div>

                        <div class="form-group">
                            <label for="name_buyer">Comprador</label>
                            <input v-model="invoice.name_buyer" type="text" name="name_buyer" id="name_buyer" class="form-control">
                        </div>

                        <div class="form-group">
                            <label for="NIT_buyer">NIT Comprador</label>
                            <input v-model="invoice.NIT_buyer" type="text" name="NIT_buyer" id="NIT_buyer" class="form-control">
                        </div>

                        <div class="form-group">
                            <label for="date_hourly">Fecha de la Compra</label>
                            <input v-model="invoice.date_hourly" type="date" name="date_hourly" id="date_hourly" class="form-control">
                        </div>

                        <div class="form-group">
                            <label for="before_IVA">Precio sin IVA</label>
                            <input v-model="invoice.before_IVA" type="number" name="before_IVA" id="before_IVA" class="form-control">
                        </div>

                        <div class="form-group">
                            <label for="IVA">IVA</label>
                            <input v-model="invoice.IVA" type="number" name="IVA" id="IVA" class="form-control">
                        </div>

                        <div class="form-group">
                            <label for="products">Productos</label>                            
                            <multiselect v-model="invoice.products" :multiple="true" :options="products" label="name" track-by="name"></multiselect>
                        </div>

                        <div class="form-group">
                            <label for="quantity">Cantidad</label>
                            <input v-model="invoice.quantity" type="number" name="quantity" id="quantity" class="form-control">
                        </div>

                        <div class="form-group">
                            <label for="total_value">Valor Total</label>
                            <input v-model="invoice.total_value" type="number" name="total_value" id="total_value" class="form-control">
                        </div>                        
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cerrar</button>
                        <button type="button" class="btn btn-primary" v-on:click="saveInvoice" data-bs-dismiss="modal">Guardar</button>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="detailModal" tabindex="-1" aria-labelledby="detailModalLabel" aria-hidden="true">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="detailModalLabel">Detalle de Factura</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">
                        <form>
                            <div class="row">
                                <div class="col">
                                    <label># Factura</label>
                                    <input type="text" class="form-control" disabled v-model="detailInvoice.id">
                                </div>
                                <div class="col">
                                    <label>Fecha y hora</label>
                                    <input type="text" class="form-control" disabled v-model="detailInvoice.date_hourly">
                                </div>
                            </div>

                            <div class="row">
                                <div class="col">
                                    <label>Vendedor</label>
                                    <input type="text" class="form-control" disabled v-model="detailInvoice.name_emitter">
                                </div>
                                <div class="col">
                                    <label>Nit Vendedor</label>
                                    <input type="text" class="form-control" disabled v-model="detailInvoice.NIT_emitter">
                                </div>
                            </div>

                            <div class="row">
                                <div class="col">
                                    <label>Comprador</label>
                                    <input type="text" class="form-control" disabled v-model="detailInvoice.name_buyer">
                                </div>
                                <div class="col">
                                    <label>Nit Comprador</label>
                                    <input type="text" class="form-control" disabled v-model="detailInvoice.NIT_buyer">
                                </div>
                            </div>

                            <div class="row">
                                <div class="col">
                                    <label>Valor sin Iva</label>
                                    <input type="text" class="form-control" disabled v-model="detailInvoice.before_IVA">
                                </div>
                                <div class="col">
                                    <label>IVA</label>
                                    <input type="text" class="form-control" disabled v-model="detailInvoice.IVA">
                                </div>
                            </div>

                            <div class="row">
                                <div class="col">
                                    <label>Valor a Pagar</label>
                                    <input type="text" class="form-control" disabled v-model="detailInvoice.total_value">
                                </div>                                
                            </div>
                            <div class="row">
                                <div class="col">
                                    <div class="col">
                                        <label>Productos</label>
                                        <table class="table table-striped table-bordered">
                                            <thead>
                                                <tr>
                                                    <th>Producto</th>
                                                    <th>Descripción</th>
                                                    <th>Valor Unitario</th>
                                                </tr>
                                            </thead>
                                            <tbody>
                                                <tr v-for="(product, key) in detailInvoice.invoices_products" :key="key">
                                                    <td>{{product.name}}</td>
                                                    <td>{{product.description}}</td>
                                                    <td>{{product.unit_value}}</td>
                                                </tr>
                                            </tbody>
                                        </table>
                                    </div>
                                </div>
                            </div>
                        </form>                                                
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cerrar</button>                        
                    </div>
                </div>
            </div>
        </div>  
    </div>
</template>
<script>
import Multiselect from 'vue-multiselect';
import moment from 'moment';
export default {
    /**
     * Se trae el componente para poder seleccionar varios productos y poder mostrarlos, se trae de igualforma 
     * una función la cual refresca el token en caso que expire
     */
    components:{
        Multiselect
    },
    name:'InvoiceComponent',
    props:['token','refreshToken'],
    data() {
        return{
            backendUrl: process.env.VUE_APP_BACKEND_URL,
            invoices:[],
            invoice:{},
            id:null,
            products:[],     
            filter:"",
            detailInvoice:{} 
        }
    },
    mounted() {          
        this.getData();
    },
    methods:{
        getData(){            
            this.getProducts();
            this.getInvoices();
        },
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
        getInvoices(){
            let headers = {
                Authorization: this.token
            };
            this.$axios.get(`${this.backendUrl}/invoices`,{headers}).then((response)=>{
                this.invoices = response.data.invoices;
            }).catch((error)=>{
                if(error.response.data.message === 'Token has expired'){
                    this.refreshToken();                    
                }
            })
        },
        detail(data){
            this.detailInvoice = data;
        },
        formatDate(dateString) {
            const date = moment(dateString);                
            return date.format('YYYY-MM-DD');
        },
        editData(data){
            /**
             * Se realiza la carga de la información a la variable invoice  para
             * mostrarla en el modal, tambien se toma en una variable aparte el id del 
             * objeto a editar
             */
            this.invoice = {
                IVA:data.IVA,
                NIT_buyer:data.NIT_buyer,
                NIT_emitter:data.NIT_emitter,
                before_IVA:data.before_IVA,                
                date_hourly: this.formatDate(data.date_hourly),
                products: data.invoices_products,
                name_buyer:data.name_buyer,
                name_emitter:data.name_emitter,
                quantity:data.quantity,
                total_value:data.total_value,
            }
            this.id = data.id;
        },
        /**
         * Mediante el id estando vacio o no se 
         * puede validar si el objeto se edita o se crea
         */
        saveInvoice(){
            let headers = {
                Authorization: this.token
            };
            if(this.id === null){                 
                this.$axios.post(`${this.backendUrl}/invoices`,this.invoice,{headers}).then((response)=>{
                    this.$toastr.s(response.data.message);
                    this.cleanData();
                }).catch((error)=>{
                    this.$toastr.e("Intente de nuevo mas tarde");
                    console.log(error)
                })
            }else{
                this.$axios.put(`${this.backendUrl}/invoices/${this.id}`,this.invoice,{headers}).then((response)=>{
                    this.$toastr.s(response.data.message);
                    this.cleanData();
                }).catch((error)=>{
                    this.$toastr.e("Intente de nuevo mas tarde");
                    console.log(error)
                })
            }
        },
        cleanData(){                                   
            this.invoice = {};
            this.id = null;
            this.getData();
        }
    },
    computed:{
        /**
         * Se realizan funciones en la propiedad computed 
         * para dar formato a la fecha y para buscar por 
         * todos los campose de la tabla
         */
        formattedDate() {
            return this.formatDate(this.date);
        },

        /**
         * Se realiza un filter con validacion if else para verificar la existencia de 
         * cada campo de la tabla
         */
        filteredRows() {
            if(!this.invoices.filter) return false;

            return this.invoices.filter((row) => {
                const id = row.id.toString().toLowerCase();
                const searchTerm = this.filter.toLowerCase();
                let id_invoice = id.includes(searchTerm);
                if(id_invoice === true){
                    return id.includes(searchTerm);
                }else {    
                    const name_emitter = row.name_emitter.toString().toLowerCase();
                    const searchTermName = this.filter.toLowerCase();
                                    
                    let name_e = name_emitter.includes(searchTermName);
                    if(name_e === true){
                        return name_emitter.includes(searchTermName);
                    }else{
                        const name_buyer = row.name_buyer.toString().toLowerCase();
                        const searchTermName = this.filter.toLowerCase();
                                        
                        let name_b = name_buyer.includes(searchTermName);
                        if(name_b === true){
                            return name_buyer.includes(searchTermName);
                        }else{
                            const date_hourly = this.formatDate(row.date_hourly).toString().toLowerCase();
                            const searchTermName = this.filter.toLowerCase();
                                            
                            let date_h = date_hourly.includes(searchTermName); 
                            if(date_h === true){
                                return date_hourly.includes(searchTermName); 
                            }else{
                                const total_value = row.total_value.toString().toLowerCase();
                                const searchTermName = this.filter.toLowerCase();
                                                    
                                let total_v = total_value.includes(searchTermName);
                                    
                                if(total_v === true){
                                    return total_value.includes(searchTermName);
                                }
                            }
                        }
                    }
                }   
            });
        },
    }
}
</script>
<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>