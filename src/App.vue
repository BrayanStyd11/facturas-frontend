<template>
    <div v-if="invoiceToken !== null">
        <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
            <div class="container-fluid">
                <a class="navbar-brand" href="#">Inicio</a>
                <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
                    <span class="navbar-toggler-icon"></span>
                </button>
                <div class="collapse navbar-collapse" id="navbarSupportedContent" v-if="invoiceToken !== null">
                    <ul class="navbar-nav me-auto mb-2 mb-lg-0">
                        <li class="nav-item">
                            <a :class="activeComponent === 'products' ? 'nav-link active' : 'nav-link'" aria-current="page" href="#" v-on:click="changeComponent('products')">Productos</a>
                        </li>
                        <li class="nav-item">
                            <a :class="activeComponent === 'invoice' ? 'nav-link active' : 'nav-link'" href="#" v-on:click="changeComponent('invoice')">Facturas</a>
                        </li>                                        
                    </ul>    
                    <form class="d-flex">
                        <label></label>
                        <button class="btn btn-primary" v-on:click="(e)=>logout(e)">Cerrar Sesión</button>
                    </form>           
                </div>
            </div>        
        </nav>
        <div class="mt-5" v-if="activeComponent === 'invoice'">
            <Invoice :token="invoiceToken"/>
        </div>
        <div class="mt-5" v-if="activeComponent === 'products'">
            <Products :token="invoiceToken"/>
        </div>
    </div>

    <div v-else>
        <Login/>
    </div>
</template>
<script>
import Login from './components/Login.vue';
import Invoice from './components/Invoice.vue';
import Products from './components/Products.vue';

export default {
    name:'AppComponent',
    props:['user'],
    components:{
        Login,
        Invoice,
        Products,
    },
    data () {
        return {
            backendUrl: process.env.VUE_APP_BACKEND_URL,
            invoiceToken:null,
            activeComponent:'invoice'
        }
    },

    mounted(){
        this.getInvoiceToken();
    },
    methods:{
        getInvoiceToken(){
            let invoiceToken = localStorage.getItem('invoiceToken');
            if(invoiceToken !== null){
                this.invoiceToken = invoiceToken;  
            }            
        },        
        changeComponent(data){
            this.activeComponent = data;
        },
        logout(e){
            e.preventDefault();
            let headers = {
                Authorization: this.invoiceToken
            };

            this.$axios.get(`${this.backendUrl}/logout`,{headers}).then((response)=>{
                localStorage.removeItem('invoiceToken');
                this.$toastr.s(response.data.message);
                 setTimeout(() => window.location.reload(), 2000);                
            }).catch((error)=>{
                this.$toastr.e('Intenta de nuevo mas tarde');
                console.log(error);
            });
        }
    }
}
</script>