<template>
  <div id='app'>
    <div id='login' v-if='!logon' >
      <loginForm
        v-bind:firebase="firebase"
        v-on:ingresoCorrecto='ingresoCorrecto($event)'>
      </loginForm>
    </div>
    <div id='inicio' class="container mx-auto p-2" v-else>
      
      <!-- Contenido de las listas de gasto -->
      <div class="container">
        <div class="row bg-dark">
          <div class="col-10 text-white text-center my-auto">
            <h1 class="mb-0">Lista de Gastos</h1>
          </div>
          <div class="col-2 bg-dark my-auto">
            <span
              v-bind:id="'agregar'"
              v-bind:class="['btn', 'btn-primary', boton]"
              style="font-size: 10px"
              v-on:click="butonPlus($event)"
            ></span>
          </div>
        </div>
      </div>

      <div v-if="despliegue" key="despliegue" class="container border bg-light rounded text-primary lead p-2 my-2">
        <div class="h3 bg-info text-dark my-0">Agregar Gasto</div>
        
        <div class="row justify-content-md-center my-2">
          <div class="col-sm-10 col-md-4 mx-auto my-auto">Nombre del gasto</div>
          <div class="col-sm-8 col-md-4 mx-auto my-auto">
            <input v-model="nombreGasto" class="text-primary" type="text" style="width:100%"/>
          </div>
        </div>
        <div class="row justify-content-md-center my-2">
          <div class="col-sm-10 col-md-4 mx-auto my-auto">Monto del gasto</div>
          <div class="col-sm-8 col-md-4 mx-auto my-auto">
            <input v-model="montoGasto" class="text-primary" type="text" style="width:100%"/>
          </div>
        </div>
        <div class="row justify-content-md-center my-2">
          <div class="col-sm-10 col-md-4 mx-auto my-auto">Tipo de gasto</div>
          <div class="col-sm-8 col-md-4 mx-auto my-auto">
            <input v-model="tipoGasto" class="text-primary" type="text" style="width:100%"/>
          </div>
        </div>
        <div class="row justify-content-md-center my-3">
          <div v-if="actualizar" class="col-6 col-md-3 mx-auto">
            <div id="actualizar" class="btn btn-info" v-on:click="manejarClick($event)" style="width:100%">Actualizar</div>
          </div>
          <div v-else class="col-6 col-md-3 mx-auto">
            <div id="agregar" class="btn btn-primary" v-on:click="manejarClick($event)" style="width:100%">Agregar</div>
          </div>
        </div>
      </div>

      <div class="container text-center my-3">
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
    },
    butonPlus: function(event) {
      if (event.target.id === "agregar") {
        this.boton = "fa fa-minus";
        document.getElementById("agregar").setAttribute("id", "actualizar");
        this.despliegue = true;
      } else if (event.target.id === "actualizar") {
        this.boton = "fa fa-plus";
        document.getElementById("actualizar").setAttribute("id", "agregar");
        this.despliegue = false;
        this.actualizar = false;
      }
      this.montoGasto = "";
      this.nombreGasto = "";
      this.tipoGasto = "";
    },
    manejarClick: function(evento) {
      if (evento.target.id === "agregar") {
        // Se agregara los nuevos gastos
        const gastoData = {
          monto: this.montoGasto,
          nombre: this.nombreGasto,
          tipo: this.tipoGasto
        };
        //this.totalGasto += parseFloat(this.montoGasto);
        this.coleccion
          .add(gastoData)
          .then(docReference => {
            this.gastos.unshift({
              id: docReference.id,
              monto: gastoData.monto,
              nombre: gastoData.nombre,
              tipo: gastoData.tipo
            });
          })
          .catch(Errro => {
            alert("No se pudo agregar el libro al sistema. Error: " + Errro.message);
          });
        this.montoGasto = "";
        this.nombreGasto = "";
        this.tipoGasto = "";
        this.despliegue = false;
        this.boton = "fa fa-plus";
        document.getElementById("actualizar").setAttribute("id", "agregar");
      }
      if (evento.target.id === "actualizar") {
        // Actualizando los gastos en firebase
        var Ref = this.db
          .collection("/usuarios/" + this.idUsuario + "/gastos")
          .doc(this.idEditar);
        Ref.update({
          MontoGasto: this.montoGasto,
          NombreGasto: this.nombreGasto,
          TipoGasto: this.tipoGasto
        });

        // actualizando los valores de la lista
        //this.totalGasto -= this.montoAnt;
        this.gastos[
          this.gastos.indexOf(
            this.gastos.find(element => element.id === this.idEditar)
          )
        ].nombre = this.nombreGasto;
        this.gastos[
          this.gastos.indexOf(
            this.gastos.find(element => element.id === this.idEditar)
          )
        ].tipo = this.tipoGasto;
        this.gastos[
          this.gastos.indexOf(
            this.gastos.find(element => element.id === this.idEditar)
          )
        ].monto = this.montoGasto;
        //this.totalGasto += parseFloat(this.montoGasto);

        this.montoGasto = "";
        this.nombreGasto = "";
        this.tipoGasto = "";
        this.despliegue = false;
        this.actualizar = false;
        this.boton = "fa fa-plus";
        document.getElementById("actualizar").setAttribute("id", "agregar");
      }
    },
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
#inicio{
  border: 2px solid blue;
}
</style>