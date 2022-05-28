<template>
  <div class="hello">
    <canvas id="lienzo" ref="canvas" class="cva" width="600" height="350"></canvas>
    <div class="img_f" id="img_f" v-bind:style="{ backgroundImage: `url(${require('@/assets/'+objN1.img)})` }" >
      <svg class="maping" id="svg" v-hammer:panstart="captureOn" v-hammer:pan="mo" v-hammer:panend="captureOff" >
            <!-- <svg class="maping" id="svg"  @touchstart="captureOn" @touchmove="movil"  @touchend="captureOff"  v-on:mousedown="captureOn" v-on:mouseup="captureOff" v-on:mousemove="mo"> -->
         <circle id="ini" v-bind:cx="objN1.puntos_inicio_final[0].x1" v-bind:cy="objN1.puntos_inicio_final[0].y1" r="15" fill="blue"  v-on:touchstart="capturar_clik('inicio',1)" v-on:mousedown="capturar_clik('inicio',1)" />
            <circle id="fin" v-bind:cx="objN1.puntos_inicio_final[1].x1" v-bind:cy="objN1.puntos_inicio_final[1].y1" r="15" fill="orange" v-on:touchend="capturar_clik('fin',1)"  v-on:mousemove="capturar_clik('fin',1)" />
            <circle id="stop" display="none" v-bind:cx="this.startX" v-bind:cy="this.startY
            " r="15"  stroke="red" stroke-width="5" fill="transparent">STOP</circle>          
           <!-- <circle id="restreo"  v-bind:cx="this.startX" v-bind:cy="this.startY" r="5" fill="green" /> -->
           
           <!-- <line v-bind:cx="objN1.control[0].x2" v-bind:cy="objN1.control[0].y2" r="15"  fill="red"/> -->
            <!-- <line v-for="lineas in objN1.control" :key="lineas.length" v-on:id="lineas.x1" v-bind:x1="lineas.x1" v-bind:y1="lineas.y1" v-bind:x2="lineas.x2" v-bind:y2="lineas.y2" stroke="red" id="pared" stroke-width="5" fill="rgb(121,0,121)" ></line> -->
            <line v-for="lineas in objN1.camino_correcto" :key="lineas.length" v-on:id="lineas.x1" v-bind:x1="lineas.x1" v-bind:y1="lineas.y1" v-bind:x2="lineas.x2" v-bind:y2="lineas.y2" stroke="black" id="pared" stroke-width="5" fill="rgb(121,0,121)" ></line>
            <image xlink:href="@/assets/boom.png" id="boom" display="none" width="50" height="50" v-bind:x="this.startX-25" v-bind:y="this.startY-25" v-on:click="tc()" />
            <image xlink:href="@/assets/azul_B.png" display="block" id="gris" width="50" height="50" v-bind:x="this.startX" v-bind:y="this.startY-50" v-on:touchstart="capturar_clik('inicio',1)" v-on:mousedown="capturar_clik('inicio',1)" />         
            <image xlink:href="@/assets/azul.png" display="none" id="azul" width="50" height="50" v-bind:x="this.startX" v-bind:y="this.startY-50" />
          </svg>

        </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: Object
  },
  data: function(){
    return{
      canvas:null,
      context:null,
      isDrawing:false,
      startX:0,
      startY:0,
      mi_ini_llave1:0,
      mi_fin_llave1:0,
      captureToggle: false,
      x: 0,
      y: 0,
      valores:0,
      llave: 0,
      llave_fin:0,
      llave_tiempo:0,
      colision:false,
      coliciones_realizadas:5,  
      valorparar:null,
      tiempoa:1000,
      avancebar:0,
      maxbar:0,
      llenado_bar:1,
      mostrar:0,
      control:0,
      objN1:{img:"abejaima.png",puntos_inicio_final:[{x1:0,y1:0},{x1:0,y1:0}]}
      // plantilla_actual: plantilla,
    }
  }
  ,
  methods: {
    movil:function(evtt){
      this.x = evtt.touches[0].pageX
      this.y = evtt.touches[0].pageY
      this.trazar(this.x,this.y)
      // console.log(this.x,this.y)
       
    },
    mo: function(e) {
      
      if (this.captureToggle==true & this.llave_tiempo==0 & this.coliciones_realizadas!=0) {
      
         var out = e.velocity<0?e.velocity*=-1:e.velocity;
        if(out<=0.5){
           var x= e.center.x;
           var y= e.center.y;
           this.trazar(x,y)
          //  console.log("si dibujo"+out)
          
        }else{
            this.stop(true)
            console.log("vas muy rapido flush ==3")
            
        }
     
     }
    },
    captureOn: function() {
      if(this.llave==1 & this.colision==false & this.llave_fin==0){
      this.captureToggle = true
      this.isDrawing=true
      document.getElementById('svg').style.cursor = 'none';
    
      }
    },
    captureOff: function() {
      this.captureToggle = false
      this.isDrawing=false;
      document.getElementById('svg').style.cursor = 'auto';
    },
    capturar_clik(idbuble,llave){
  
        if (idbuble=='inicio' & this.llave==0) {
          console.log("Inciaste")
          this.llave=llave
           document.getElementById('gris').style.display='none'
           document.getElementById('azul').style.display='block'
        }if(idbuble=='fin' & this.llave_fin==0){
          
          if(this.llave==1 & this.captureToggle == true & this.colision==false){
            var estado_respondio=false
             document.getElementById('gris').style.display='block'
             document.getElementById('azul').style.display='none'

            if( this.control>0){
              estado_respondio=true              
              document.getElementById("pantallaActividad").style.backgroundColor='#3FE495CC';
            }else{
              this.objN1.img=this.objN1.imgok;
              estado_respondio=false               
              document.getElementById("pantallaActividad").style.backgroundColor='#F6535DCC';
            }
            //  this.parar_tiempo()
              
              this.llave_fin=llave
              this.captureToggle = false
              this.isDrawing=false;

              // console.log(respuestas_correctas+","+respuestas_incorrectas)
              this.$emit("respuesta",estado_respondio);
                        
              // setTimeout(() => {
                // this.siguiente_iteracion();
                // this.responder();
              //  },1000);               
         
         }else{
            console.log("no puedes finalizar ya que no estas dibujando")
          }
          
        }
    },
    trazar(clientX,clientY){
      this.canvas =this.$refs.canvas
      this.context=this.canvas.getContext("2d")
      var rect = this.canvas.getBoundingClientRect();

      var x=clientX-rect.left;
      var y=clientY-rect.top;

      if(this.startX+25>=x & this.startX-25<=x & this.startY+25>=y & this.startY-25<=y){
      if(this.llave==1 & this.colision==false){
           
      for (let fila = 0; fila < this.objN1.camino_correcto.length; fila++) {
        this.evaluapunto(this.objN1.camino_correcto[fila].x1,this.objN1.camino_correcto[fila].x2,x,this.objN1.camino_correcto[fila].y1,this.objN1.camino_correcto[fila].y2,y)
      }  
      }  

      if(this.isDrawing){      
        // this.choque(x,y);
        
       
        this.context.beginPath();
        this.context.moveTo(this.startX,this.startY);
        this.context.lineTo(x,y);
        this.context.lineWidth =10;
        this.context.lineCap ='round';
        this.context.strokeStyle ="#4978D6";
        this.context.stroke();
        this.startX=x;
        this.startY=y; 
        
           
      }
 }
      
    },
    evaluapunto(vala,valb,px,valc,vald,py){
      if(px>=vala & px<=valb+2 & py>=valc & py<=vald+2){
  
             this.colision=true
             this.isDrawing=false  
             this.coliciones_realizadas=this.coliciones_realizadas-1;
                // console.log("coliciones: "+this.coliciones_realizadas)
         if(this.coliciones_realizadas<=0){
           this.objN1.img=this.objN1.imgok;
           document.getElementById("pantallaActividad").style.backgroundColor='#F6535DCC';
           this.$emit("respuesta",false);
            
         }else{
        // this. iteracion_config.juego_respuestas_correctas=0;
        // this. iteracion_config.juego_respuestas_incorrectas=1;
        // this.parar_tiempo()

               document.getElementById("pantallaActividad").style.backgroundColor='#F6535DCC';
              
              setTimeout(() => {
                document.getElementById("pantallaActividad").style.backgroundColor='white';
               },500); 
               }       
                console.log(this.startX+" , "+this.startY)
               document.getElementById('boom').style.display='block';
           

         }
         if(px>=this.objN1.fintX-5 & px<=this.objN1.fintX & py>=this.objN1.fintY-10 & py<=this.objN1.fintY+10){
           this.capturar_clik('fin',1)
         }
         if(px>=this.objN1.control[0].x1-2 & px<=this.objN1.control[0].x2+2 & py>=this.objN1.control[0].y1 & py<=this.objN1.control[0].y2){
           this.control=1;
          //  console.log("CONTROL")
         }
        // console.log(this.objN1.control[0].x1)
      
    },
    tc(){ 
       if(this.coliciones_realizadas!=0){
      console.log("sigue dibujando ");
      this.colision=false
      this.isDrawing=true
      document.getElementById('boom').style.display='none';
        }
    },
    stop(estado){
       if(estado==true){
         this.colision=true
            this.isDrawing=false  
               document.getElementById('stop').style.display='block';
               setTimeout(() => {
                this.verifica()
               },1000); 
       }
    },
    verifica(){
      this.colision=false
            this.isDrawing=true  
             document.getElementById('stop').style.display='none';
    },
  },
   mounted(){
       var random_llena= Math.ceil(Math.random()*3);
      // var random_llena=3
      console.log(random_llena);
      if(random_llena==1){
          this.startX=70,
          this.startY=189
          this.objN1={tipo:"N2",
          img:"palomaimaA.png",
          imgok:"palomaAok.png",
         camino_correcto:[{x1:68,y1:30,x2:68,y2:175},{x1:68,y1:203,x2:68,y2:320},{x1:66,y1:30,x2:538,y2:30},{x1:66,y1:320,x2:538,y2:320},{x1:538,y1:30,x2:538,y2:145},{x1:538,y1:174,x2:538,y2:320},{x1:68,y1:175,x2:108,y2:175},{x1:68,y1:262,x2:108,y2:262},{x1:108,y1:60,x2:108,y2:118},{x1:108,y1:203,x2:108,y2:233},{x1:105,y1:261,x2:105,y2:295},{x1:147,y1:30,x2:147,y2:60},{x1:110,y1:87,x2:182,y2:87},{x1:105,y1:146,x2:182,y2:146},{x1:110,y1:203,x2:222,y2:203},{x1:143,y1:290,x2:143,y2:319},{x1:143,y1:117,x2:143,y2:202},{x1:143,y1:232,x2:143,y2:260},{x1:141,y1:262,x2:185,y2:262},{x1:141,y1:291,x2:185,y2:291},{x1:185,y1:205,x2:185,y2:263},{x1:183,y1:58,x2:183,y2:149},{x1:185,y1:60,x2:225,y2:60},{x1:223,y1:60,x2:223,y2:90},{x1:185,y1:117,x2:420,y2:117},{x1:185,y1:174,x2:224,y2:174},{x1:222,y1:147,x2:222,y2:174},{x1:220,y1:147,x2:380,y2:147},{x1:222,y1:230,x2:222,y2:262},{x1:222,y1:289,x2:222,y2:320},{x1:222,y1:233,x2:263,y2:233},{x1:265,y1:60,x2:265,y2:115},{x1:265,y1:149,x2:265,y2:235},{x1:265,y1:261,x2:265,y2:320},{x1:265,y1:202,x2:302,y2:202},{x1:265,y1:262,x2:302,y2:262},{x1:302,y1:30,x2:302,y2:88},{x1:302,y1:175,x2:302,y2:205},{x1:300,y1:88,x2:382,y2:88},{x1:339,y1:174,x2:423,y2:174},{x1:300,y1:232,x2:498,y2:232},{x1:337,y1:60,x2:423,y2:60},{x1:300,y1:290,x2:382,y2:290},{x1:342,y1:176,x2:342,y2:290},{x1:382,y1:60,x2:382,y2:87},{x1:382,y1:146,x2:382,y2:173},{x1:380,y1:205,x2:460,y2:205},{x1:420,y1:88,x2:500,y2:88},{x1:420,y1:145,x2:540,y2:145},{x1:423,y1:90,x2:423,y2:145},{x1:380,y1:260,x2:423,y2:260},{x1:420,y1:262,x2:420,y2:320},{x1:460,y1:60,x2:460,y2:118},{x1:460,y1:148,x2:460,y2:206},{x1:460,y1:260,x2:460,y2:290},{x1:460,y1:289,x2:500,y2:289},{x1:500,y1:60,x2:500,y2:89},{x1:500,y1:120,x2:500,y2:144},{x1:500,y1:175,x2:500,y2:288},{x1:500,y1:175,x2:540,y2:175}],
         puntos_inicio_final:[{x1:70,y1:189},{x1:534,y1:160}],
         fintX:534,
         fintY:160,
         control:[{x1:458,y1:199,x2:501,y2:208}]
          }
        }
        if(random_llena==2) {
        this.startX=77,
        this.startY=191
        this.objN1={tipo:"N2",
        img:"nubeima.png",
        imgok:"nubeok.png",
        camino_correcto:[{x1:77,y1:18,x2:77,y2:173},{x1:77,y1:205,x2:77,y2:332},{x1:75,y1:18,x2:505,y2:18},{x1:75,y1:330,x2:505,y2:330},{x1:75,y1:330,x2:505,y2:330},{x1:503,y1:18,x2:503,y2:143},{x1:503,y1:176,x2:503,y2:330},{x1:75,y1:173,x2:113,y2:173},{x1:75,y1:206,x2:184,y2:206},{x1:112,y1:50,x2:112,y2:175},{x1:112,y1:205,x2:112,y2:238},{x1:112,y1:271,x2:112,y2:301},{x1:110,y1:50,x2:148,y2:50},{x1:110,y1:112,x2:290,y2:112},{x1:110,y1:270,x2:183,y2:270},{x1:148,y1:48,x2:148,y2:82},{x1:148,y1:143,x2:148,y2:205},{x1:148,y1:236,x2:148,y2:300},{x1:185,y1:203,x2:185,y2:273},{x1:148,y1:145,x2:220,y2:145},{x1:185,y1:20,x2:185,y2:80},{x1:181,y1:80,x2:220,y2:80},{x1:183,y1:175,x2:254,y2:175},{x1:183,y1:237,x2:396,y2:237},{x1:183,y1:300,x2:255,y2:300},{x1:217,y1:48,x2:291,y2:48},{x1:220,y1:177,x2:220,y2:208},{x1:218,y1:270,x2:218,y2:300},{x1:256,y1:50,x2:256,y2:177},{x1:220,y1:205,x2:362,y2:205},{x1:215,y1:271,x2:290,y2:271},{x1:292,y1:143,x2:292,y2:175},{x1:292,y1:270,x2:292,y2:330},{x1:289,y1:79,x2:329,y2:79},{x1:289,y1:145,x2:329,y2:145},{x1:328,y1:20,x2:328,y2:81},{x1:328,y1:110,x2:328,y2:205},{x1:328,y1:236,x2:328,y2:300},{x1:328,y1:113,x2:361,y2:113},{x1:362,y1:20,x2:362,y2:50},{x1:362,y1:80,x2:362,y2:115},{x1:362,y1:175,x2:362,y2:207},{x1:362,y1:268,x2:362,y2:300},{x1:360,y1:144,x2:466,y2:144},{x1:360,y1:175,x2:400,y2:175},{x1:398,y1:207,x2:468,y2:207},{x1:361,y1:298,x2:396,y2:298},{x1:395,y1:48,x2:430,y2:48},{x1:397,y1:80,x2:397,y2:146},{x1:397,y1:205,x2:397,y2:300},{x1:397,y1:82,x2:431,y2:82},{x1:432,y1:20,x2:432,y2:51},{x1:432,y1:80,x2:432,y2:112},{x1:432,y1:146,x2:432,y2:238},{x1:432,y1:298,x2:432,y2:330},{x1:400,y1:267,x2:466,y2:267},{x1:469,y1:49,x2:469,y2:80},{x1:469,y1:112,x2:469,y2:146},{x1:469,y1:236,x2:469,y2:300},{x1:466,y1:175,x2:502,y2:175},{x1:467,y1:80,x2:502,y2:80}],
        puntos_inicio_final:[{x1:77,y1:191},{x1:498,y1:158}],
        fintX:498,
        fintY:158,
        control:[{x1:470,y1:83,x2:470,y2:115}]
          }
        }
        if(random_llena==3){
          this.startX=63,
          this.startY=175
          this.objN1={tipo:"N2",
          img:"conejoimaa.png",
          imgok:"conejook.png",
         camino_correcto:[{x1:63,y1:17,x2:63,y2:157},{x1:63,y1:194,x2:63,y2:333},{x1:63,y1:20,x2:537,y2:20},{x1:63,y1:330,x2:537,y2:330},{x1:535,y1:17,x2:535,y2:157},{x1:535,y1:194,x2:535,y2:333},{x1:65,y1:123,x2:150,y2:123},{x1:65,y1:155,x2:150,y2:155},{x1:65,y1:228,x2:105,y2:228},{x1:65,y1:295,x2:105,y2:295},{x1:108,y1:23,x2:108,y2:87},{x1:108,y1:193,x2:108,y2:231},{x1:106,y1:86,x2:148,y2:86},{x1:106,y1:262,x2:148,y2:262},{x1:148,y1:155,x2:148,y2:297},{x1:149,y1:193,x2:193,y2:193},{x1:145,y1:297,x2:193,y2:297},{x1:148,y1:51,x2:235,y2:51},{x1:195,y1:53,x2:195,y2:158},{x1:195,y1:226,x2:195,y2:262},{x1:197,y1:121,x2:238,y2:121},{x1:197,y1:260,x2:238,y2:260},{x1:236,y1:23,x2:236,y2:53},{x1:236,y1:120,x2:236,y2:228},{x1:236,y1:259,x2:236,y2:329},{x1:232,y1:87,x2:277,y2:87},{x1:234,y1:228,x2:320,y2:228},{x1:238,y1:299,x2:322,y2:299},{x1:277,y1:52,x2:277,y2:193},{x1:277,y1:227,x2:277,y2:262},{x1:279,y1:55,x2:363,y2:55},{x1:279,y1:155,x2:363,y2:155},{x1:322,y1:88,x2:322,y2:122},{x1:322,y1:191,x2:322,y2:231},{x1:320,y1:86,x2:493,y2:86},{x1:364,y1:122,x2:448,y2:122},{x1:324,y1:195,x2:407,y2:195},{x1:321,y1:260,x2:407,y2:260},{x1:366,y1:121,x2:366,y2:158},{x1:366,y1:227,x2:366,y2:330},{x1:406,y1:50,x2:406,y2:85},{x1:406,y1:156,x2:406,y2:198},{x1:406,y1:259,x2:406,y2:297},{x1:404,y1:50,x2:454,y2:50},{x1:406,y1:227,x2:452,y2:227},{x1:406,y1:296,x2:492,y2:296},{x1:449,y1:121,x2:449,y2:157},{x1:449,y1:192,x2:449,y2:262},{x1:451,y1:154,x2:493,y2:154},{x1:451,y1:195,x2:537,y2:195},{x1:494,y1:227,x2:494,y2:299},{x1:491,y1:51,x2:535,y2:51},{x1:491,y1:123,x2:535,y2:123},{x1:491,y1:84,x2:491,y2:126},{x1:491,y1:153,x2:491,y2:198}],
         puntos_inicio_final:[{x1:63,y1:175},{x1:530,y1:175}],
         fintX:530,
         fintY:175,
         control:[{x1:448,y1:120,x2:490,y2:125}]
          }
        }
   }
  
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.img_f{
  // outline: 2px solid magenta;
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
 left: calc(28% - 5%);
  margin-left: 0%;
  position: absolute;
  // outline: 2px solid blue;
}
.maping{
width: 600px;
height: 350px;
display: flex;
flex-direction: row;
justify-content: center;
// cursor: -webkit-grab; cursor: grab;

  
    // cursor: url(@/assets/azul.png), auto;
  
  

}
</style>
