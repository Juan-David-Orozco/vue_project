<template>
  <div id='app'>
    <div id='login' v-if='!logon' >
      <loginForm
        v-bind:firebase="firebase"
        v-on:ingresoCorrecto='ingresoCorrecto($event)'>
      </loginForm>
    </div>
    <div id='inicio' class="container m-2 p-2" v-else>
    <!-- Contenido de las listas de gasto -->
      <div class="row">
        <div class="col-10 bg-dark text-white text-center my-1">
          <h1>Lista de Gastos</h1>
        </div>
        <div class="col-2 bg-dark my-1">
          <span
            v-bind:id="'agregar'"
            v-bind:class="['mr-1', 'btn', 'btn-primary', boton]"
            style="font-size: 10px; margin-top: 15px"
          ></span>
        </div>
      </div>

      <div class="container text-center mt-4">
        <div class="row lead border rounded bg-dark text-white py-1">
          <div class="col-3">Nombre del gasto</div>
          <div class="col-3">Monto del gasto</div>
          <div class="col-3">Tipo de gasto</div>
        </div>
        <!-- lista de los gastos -->
        <gastosComponente
          v-for="(gasto,index) in gastos"
          v-bind:gasto="gasto"
          v-bind:id="gasto.id"
          v-bind:indice="index"
          v-bind:key="index"
        ></gastosComponente>
      </div>

    </div>
  </div>
</template>

<script>
import firebase from "firebase";
import "firebase/firestore";
import loginForm from "./components/Form.vue";
import gastosComponente from "./components/GastosComponente.vue";

export default {
  name:'app',
    data: function() {
    return {
      boton: "fa fa-plus",
      gastos: [],
      filtrados: [],
      nombreGasto: "",
      tipoGasto: "",
      montoGasto: "",
      coleccion: {},
      logon: false,
      firebase: "",
      idUsuario: "",
      db: "",
      totalGasto: 0.0,
      actualizar: false,
      despliegue: false,
      idEditar: "",
      montoAnt: 0.0,
      indice: 0,
      filter: "filtro",
      mostrarHogar: "",
      mostrarTrabajo: "",
      mostrarCarros: "",
      cantidad: 0.0
    };
  },
  components: {
    loginForm,
    gastosComponente,
  },
    beforeMount: function () {
    const firebaseConfig = {
      apiKey: "AIzaSyBR1umMRC-2dLGd2FQJ29rXzDtboO52sck",
      authDomain: "fir-firestore-b058c.firebaseapp.com",
      projectId: "fir-firestore-b058c",
      storageBucket: "fir-firestore-b058c.appspot.com",
      messagingSenderId: "66465685071",
      appId: "1:66465685071:web:40a878f476d85eeb77543a"
    };
    firebase.initializeApp(firebaseConfig);
    this.db = firebase.firestore();
    const settings = {timestampsInSnapshots: true};
    this.db.settings(settings);
    this.firebase = firebase
    console.log(this.db)
    console.log(this.firebase)
  },
  methods: {
    ingresoCorrecto: function(usuario) {
      console.log("User: " + usuario);
      this.idUsuario = usuario;
      this.logon = true;
      this.coleccion = this.db.collection("/usuarios/" + usuario + "/gastos");
      this.coleccion.get().then(gastos => {
        gastos.forEach(gasto => {
          this.gastos.push({
            id: gasto.id,
            monto: gasto.data().monto,
            nombre: gasto.data().nombre,
            tipo: gasto.data().tipo
          });
          //this.totalGasto += parseFloat(gasto.data().MontoGasto);
        });
      });
      console.log(this.gastos)
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>