<template>

  <div id="Actividad_Main">
    <div
      class="pantalla"
      id="pantallaActividad"
      v-if="pantalla_actual == 'pantallaActividad'"
    >
      <div class="tiempo_bar">
      <div class="progress progresotime">
  <div class="progress-bar" role="progressbar" v-bind:style="'width:'+llenado_bar+'%'" aria-valuemin="0" aria-valuemax="100"></div>
</div>
    </div>
<!-- <div class="contenedorr"> -->
<div v-if="this.mostrar==3">
  <nivel3 @respuesta="resultadosobtenidos"></nivel3>
</div>
<div v-if="this.mostrar==2">
  <nivel2 @respuesta="resultadosobtenidos"></nivel2>
</div>
<div v-if="this.mostrar==1">
   <nivel1 @respuesta="resultadosobtenidos"></nivel1>     
</div>

    <!-- </div> -->
    <!-- finaliza -->
    </div>
  </div>
</template>

<script>

/* eslint-disable */
/* global plantilla, _, elementos_palabras, jQuery, ConsolaUtilidad, _kod */
/* eslint-enable */
import nivel3 from '@/components/LaberintoN3.vue'
import nivel2 from '@/components/LaberintoN2.vue'
import nivel1 from '@/components/LaberintoN1.vue'
export default {
  
  name: "App",
  components: {
    nivel3,
    nivel2,
    nivel1
    // ...
  },
  data: function () {
    return {    
      valorparar:null,
      tiempo_mm: 1000/180000,
      tiempoa:10,
      avancebar:0,
      maxbar:100,
      llenado_bar:0,
      milisegundos:0,
      segundos:0,
      minutos:0,
      mostrar:0,
      control:0,
    //  ejercicio_debug:true,
     objN1:{},
      /**
       * La plantilla con la que inicial el ejercicio.
       * Almacenar en la instancia para evitar que se modifique durante la ejecucion.
       */
      plantilla_actual: plantilla,
      /**
       * Configuracion de la iteración
       */
      iteracion_config: {
        /**
         * La iteracion actual
         */
        iteracion_actual: 0,
        /**
         * El maximo de iteraciones
         */
        iteracion_max: 1,
        /**
         * El total de respuestas correctas durante el ejercicio
         */
        juego_respuestas_correctas: 0,
        /**
         * El total de respuestas incorrectas durante el ejercicio
         */
        juego_respuestas_incorrectas: 0,
        /**
         * Para modalidad 1 y 3, necesito saber si es mayor, menor o random
         */
      },
      /**
       * La pantalla actual que debe activarse y mostrarse
       */
      pantalla_actual: null, //"pantallaActividad",
      /**
       * Tiempo para esperar antes de cambiar de iteración
       */
      pausa_siguiente_iteracion: 2 * 1000,
      /**
       * Bloquear o no el click
       */
      permitir_click: false,
      /**
       * El tiempo en ms cuando el usuario hace click en el boton iniciar de las instrucciones
       */
      tiempo_inicio: 0,
      /**
       * Progressbar para cuando se muestra la pantalla actividad
       * @const
       * @type {String} - `component_id` para ProgressBar
       */
      barra_pantalla_actividad: "barra_pantalla_actividad",
      /**
       * Referencia a setTimeout en metodo responder()
       */
      clock_pausa_siguiente_iteracion: 0,
      tiempo_responder_iteracion: 30 * 1000,
    }; //fin return data
  },
  computed: {
    
    /**
     * El contenido del ejercicio usando elementos_palabras
     */
    contenido: function () {
      return "todo";
    },
  
 
  },
  methods: {
    resultadosobtenidos(informacion){
      this.parar_tiempo()  
      if(informacion){
         this. iteracion_config.juego_respuestas_correctas=1;
         this. iteracion_config.juego_respuestas_incorrectas=0;
      }else{
         this. iteracion_config.juego_respuestas_correctas=0;
         this. iteracion_config.juego_respuestas_incorrectas=1;
      }
          
      setTimeout(() => {
                // this.siguiente_iteracion();
                this.responder();
               },1000); 
      // console.log(info);
    },
    /**
     * Envia el resultado del ejercicio a la ConsolaEjercicios, se llama en `siguienteIteracion()`
     */
    finEjercicio: function () {
      const maxElementosCorrectos =
        this.iteracion_config.juego_respuestas_correctas +
        this.iteracion_config.juego_respuestas_incorrectas;
      const en_blanco = Math.max(
        0,
        maxElementosCorrectos -
          (this.iteracion_config.juego_respuestas_correctas +
            this.iteracion_config.juego_respuestas_incorrectas)
      );
      //var d = new Date();
      jQuery("#Actividad_Main").stop(true, true);
      return jQuery("body").trigger({
        type: "Consola.Ejercicio.Terminado",
        resultado: {
          correcto: this.iteracion_config.juego_respuestas_correctas,
          incorrecto: this.iteracion_config.juego_respuestas_incorrectas,
          blanco: en_blanco,
          tiempo: Math.round(
            (new Date().getTime() - this.tiempo_inicio) / 1000
          ),
          total: maxElementosCorrectos,
          nivel: Math.min(
            this.iteracion_config.nivel_actual,
            this.iteracion_config.nivel_max
          ),
        },
      });
    },
    /**
     * Maneja el estado de las iteraciones y llama a `finEjercicio`
     * cuando se han usado todas las iteraciones
     */
    siguiente_iteracion: function () {
      this.iteracion_config.iteracion_actual++;
      clearTimeout(this.clock_pausa_siguiente_iteracion);
      if (
        this.iteracion_config.iteracion_actual >
        this.iteracion_config.iteracion_max
      ) {
        return this.finEjercicio();
      }
      this.dibujar(plantilla);
      // this.iniciando();
      this.pantalla_actual = "";
      return setTimeout(this.pantalla_actividad, 1000);
    },
    /**
     * Muestra una lectura o preguntas. Segun el ejercicio.
     */
    pantalla_actividad: function () {
      this.pantalla_actual = "pantallaActividad";
      // if(ejercicio_debug) {
      //  this.$root.$emit('progress-bar-duration', this.barra_pantalla_actividad, 90 * 1000);
      //  } else { 
      //  this.$root.$emit('progress-bar-start', this.barra_pantalla_actividad);
      //  } 
      this.permitir_click = true;
    },
    /**
     * Evalua las respuestas del usuario
     */
    responder: function () {
      if (!this.permitir_click) {
        return;
      }
      //  this.$root.$emit('progress-bar-stop', this.barra_pantalla_actividad);
        this.permitir_click = false;
        this.clock_pausa_siguiente_iteracion = setTimeout(
        this.siguiente_iteracion,
        this.pausa_siguiente_iteracion
      );
    },
    dibujar(plantillaver){

      if(plantillaver=="preTrazosLaberinto3"){
         this.mostrar=3
         
      }
      if(plantillaver=="preTrazosLaberinto2"){
        this.mostrar=2  
             
      }
      if(plantillaver=="preTrazosLaberinto1"){
      this.mostrar=1
  
      }  
      this.maxbar=((this.min+1)*60)
      this. iniciando();      
    },
    iniciando(){
      this.valorparar = setInterval(
        this.iniciar_tiempo
        
      ,this.tiempoa);
    },
    iniciar_tiempo(){
    
   this.milisegundos++;
   this.llenado_bar=this.llenado_bar+this.tiempo_mm;

  //  console.log(this.llenado_bar);
    if(this.milisegundos==100){
      this.milisegundos=0;
      this.segundos++;
     
    }

            if(this.segundos===181){
              this.llave_tiempo=1
              this. iteracion_config.juego_respuestas_correctas=0;
              this. iteracion_config.juego_respuestas_incorrectas=1;

              this.parar_tiempo()
              document.getElementById("pantallaActividad").style.backgroundColor='#F6535DCC';

               setTimeout(() => {
                this.responder();
               },1000);  
            }
            
    },
    parar_tiempo(){
      clearInterval(this.valorparar);
    },
  },
  mounted: function () {
    jQuery(document).ready(() => {
      jQuery("body").bind("Consola.Ejercicio.Iniciado", () => {
        this.tiempo_inicio = new Date().getTime();
        setTimeout(this.siguiente_iteracion, 1000);
      });
      //<% if(ejercicio_debug) { %>
      window.app = this;
      if (jQuery('#btnEmpezarEjercicio').length !== 0) {
        jQuery('#btnEmpezarEjercicio').click();
        
      } else {
        ConsolaUtilidad.funciones.SaltarInstrucciones();
      }
      //<% } %>
    });
  },
};
</script>

<style lang="scss" scoped>

//@import "./css/style.scss";
#Actividad_Main {
  // position: fixed;
  .pantalla {
    display: block;
  }
  #pantallaActividad{
    border-radius: 10px;
    
  }
  .tiempo_bar{
    display: flex;
    justify-content: center;
  }
  .progresotime{
    // margin-left: calc(50% - 25%);
    margin-top:10px ;
    width: 78%;
    height: 14px;
     border-radius: 20px;
     background: #ffd6ad;; 
  }
  .progress-bar{
  background-color:orange;
  }
// }
h5{
  margin-bottom: 0px;
  margin-top: 0px;
}

// .contenedorr{
//   outline: 2px solid red;
//   // position: relative;
//   border-radius: 10px;
//   width: 100%;
//   //  top: calc(30% - 10%);
//   // left: calc(33% - 0%); 
//   //  left: calc(33% - 5%);
//    display: flex; 
//   align-items: center;
 
// }
.img_f{
  // outline: 2px solid magenta;
  position: relative;
  background-repeat: no-repeat;
  background-position: center;
  background-size: contain;
  width: 460px;
  // width: 600px;
  height: 350px;
  left: calc(50% - 21%);
  // top: calc(30% - 10%);
  // left: calc(33% - 0%);
  // left: calc(33% - 5%);
  // margin: auto; 
}
.img_f2{
  outline: 2px solid magenta;
  position: relative;
  background-repeat: no-repeat;
  background-position: center;
  background-size: contain;
  // width: 460px;
  width: 600px;
  height: 350px;
  // top: calc(30% - 10%);
  // // left: calc(33% - 0%);
  left: calc(26% - 5%);
  // margin: auto; 
}
.cva{
  left: calc(50% - 20%);
  // margin-left: 16%;
  position: absolute;
  // outline: 2px solid blue;
}
.cva2{
  left: calc(28% - 5%);
  margin-left: 0%;
  position: absolute;
  outline: 2px solid blue;
}

.maping{
width: 460px;
// width: 600px;
height: 350px;
display: flex;
flex-direction: row;
justify-content: center;
}
.maping2{

// width: 460px;
width: 600px;
height: 350px;
display: flex;
flex-direction: row;
justify-content: center;
}

.op{
     
      display: flex;
      align-items: center; 
      
    width: 35.40px;
    height: 35.40px;
}
// @media screen and (max-width: 1024px) {
//   .contenedorr{
//    top: calc(30% - 10%);
//   left: calc(35% - 5%);
// }
// .img_f{
 
// top: calc(30% - 10%);
// left: calc(35% - 5%);

// }
  
// }

}
</style>
