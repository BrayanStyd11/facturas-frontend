<template>
    <div class="container" v-if="invoiceToken === null">
        <div class="card" v-if="register === false">
            <div class="card-header">
                Incia sesión en Facturas
            </div>
            <div class="card-body">
                <div class="form-group">
                    <label for="email">Correo</label>
                    <input class="form-control" v-model="email" type="email" name="email" />
                </div>

                <div class="form-group mb-3">
                    <label for="password">Contraseña</label>
                    <input class="form-control" v-model="password" type="password" name="password" />
                </div>

                <div class="form-group col-md-3">                    
                    <button class="btn btn-outline-primary" v-on:click="login">Ingresar</button>
                    <button id="button" class="btn btn-outline-success" v-on:click="changeRegister">Registrate</button>                    
                </div>
            </div>
        </div>
        <div v-else>
            <div class="card-header">
                Registrate en Facturas
            </div>
            <div class="card-body">
                <div class="form-group mb-5">
                    <label for="name">Nombres</label>
                    <input class="form-control" v-model="name" type="text" name="name" />
                </div>

                <div class="form-group mb-2">
                    <label for="email">Correo</label>
                    <input class="form-control" v-model="email" type="email" name="email" />
                </div>

                <div class="form-group mb-3">
                    <label for="password">Contraseña</label>
                    <input class="form-control" v-model="password" type="password" name="password" />
                </div>

                <div class="form-group col-md-3">                    
                    <button class="btn btn-outline-primary" v-on:click="registerUser">Registrarme</button>
                    <button id="button" class="btn btn-outline-success" v-on:click="changeRegister">Volver</button>                    
                </div>
            </div>
        </div>
    </div>
    <div v-else-if="invoiceToken !==null">
        <App :token="invoiceToken" :refreshToken="refreshToken"></App>
    </div>
</template>

<script>
import App from '../App.vue'
export default {
    name: 'LoginComponent',
    components: {
        App
    },
    data() { 
        return{            
            backendUrl: process.env.VUE_APP_BACKEND_URL,
            email:null,
            password:null,
            user:{},

            register:false,
            name:null,

            invoiceToken:null
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
        refreshToken(){
            let headers = {
                Authorization: this.invoiceToken
            };
            this.$axios.get(`${this.backendUrl}/refresh`,{headers}).then((response)=>{
                localStorage.setItem('invoiceToken', `Bearer ${response.data.tokenAuthorization.token}`);    
                this.getInvoiceToken();
                window.location.reload()
            })
        },
        changeRegister() {
            this.register = !this.register;
        },
        login(){            
            try {
                this.$axios.post(`${this.backendUrl}/login`,{email:this.email, password:this.password}).then((response)=>{                    
                    localStorage.setItem('invoiceToken', `Bearer ${response.data.tokenAuthorization.token}`);
                    this.user = response.data.user;     
                    window.location.reload();
                }).catch((error)=>{
                    this.$toastr.e("Usuario y/o Contraseña erroneos");
                    console.log(error);
                })   
            } catch (error) {
                this.$toastr.e("Intenta de Nuevo mas tarde");
                console.log(error)
            }                                 
        },

        registerUser(){            
            try {
                this.$axios.post(`${this.backendUrl}/register`,{name:this.name, email:this.email, password:this.password}).then((response)=>{
                    this.user = response.data.user                        
                    localStorage.setItem('invoiceToken', `Bearer ${response.data.tokenAuthorization.token}`);
                    window.location.reload();
                }).catch((error)=>{
                    this.$toastr.e("Revisa todos los campos");
                    console.log(error);
                })   
            } catch (error) {
                this.$toastr.e("Intenta de Nuevo mas tarde");
                console.log(error)
            }    
        }
    }
}
</script>
<style >
    #button{
        margin-left: 20px;
    }
</style>