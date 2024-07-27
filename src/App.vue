<script setup> 
    import {ref, reactive, onMounted, watch} from "vue"
    import {db} from "./data/guitarras"
    import Guitarra from "./components/Guitarra.vue"
    import Header from "./components/Header.vue"
    import Footer from "./components/Footer.vue"

    // const state = reactive({
    //   guitarras: []
    // })

    const guitarras = ref([]);
    const carrito = ref([]);
    const guitarra = ref({});

    // watch, funcion nativa de vue, escucha por cambios en carrito y cuando eso pasa actualiza "carrito" en localStorage (v68)
    watch(carrito, () => {
        guardarLocalStorage()
    }, {
        deep: true
    })

    onMounted(() => {
        guitarras.value = db
        // state.guitarras = db
        guitarra.value = db[3]
        const carritoStorage = localStorage.getItem("carrito")
        if(carritoStorage) {
            carrito.value = JSON.parse(carritoStorage)
        }
    })

    const guardarLocalStorage = () => {
        localStorage.setItem("carrito", JSON.stringify(carrito.value))
    }

    const agregarCarrito = (guitarra) => { // custom event
        const existeCarrito = carrito.value.findIndex(producto => producto.id == guitarra.id)
        if(existeCarrito >= 0) {
            if (carrito.value[existeCarrito].cantidad >= 5) return
            carrito.value[existeCarrito].cantidad++
        } else {
            guitarra.cantidad = 1
            carrito.value.push(guitarra)
        }
    }

    const decrementarCantidad = (id) => { // custom event
        const index = carrito.value.findIndex(producto => producto.id == id)
        if (carrito.value[index].cantidad <= 1) return
        carrito.value[index].cantidad--
    }
    const incrementarCantidad = (id) => { // custom event
        const index = carrito.value.findIndex(producto => producto.id == id)
        if (carrito.value[index].cantidad >= 5) return
        carrito.value[index].cantidad++
    }
    
    const eliminarProducto = (id) => { // custom event
        carrito.value = carrito.value.filter( producto => producto.id !== id);
    }
    
    const vaciarCarrito = () => { // custom event
        carrito.value = []
    }
  
</script>

<template>
    <Header
        :carrito="carrito"
        :guitarra="guitarra"
        @agregar-carrito="agregarCarrito"
        @decrementar-cantidad="decrementarCantidad"
        @incrementar-cantidad="incrementarCantidad"
        @eliminar-producto="eliminarProducto"
        @vaciar-carrito="vaciarCarrito"
    />
    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección - Actualizada</h2>

        <div class="row mt-5">
            <Guitarra 
                v-for="(guitarra, i) in guitarras"
                :key="i"
                :guitarra="guitarra"
                @agregar-carrito="agregarCarrito"
            />
            <!-- v-on:click="agregarCarrito" -->
        </div>
    </main>
    <Footer />
</template>
