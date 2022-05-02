# facturas_front

## Requisitos
* Node Version 14.16.0
* vue/cli Version 5.0.4

## Instalación
```
npm install
```

## Configuración para uso con API
```
Cuando el servidor del API se encuentre en ejecución.

en caso de ser necesario, modificar la variable VUE_APP_BACKEND_URL ubicada en el archivo .env en la raiz del proyecto para reconocer la dirección url del api.

se deja la url http://localhost:8000/api por defecto ya que es donde el servidor del API laravel recibe las peticiones

dejar el prefijo /api en la url ya que las rutas se encuentran configuradas para realizar las peticiones mediante dicho prefijo
```
### Ejecución para modo desarrollo
```
npm run serve
```

### Comando de Compilación en producción
```
npm run build
```

### Comando para corrección de archivos
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
