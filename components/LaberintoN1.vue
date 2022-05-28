<template>
  <div id="hello" class="hello">
    <canvas id="lienzo" ref="canvas" class="cva" width="460" height="350"></canvas>
    <div class="img_f" id="img_f" v-bind:style="{ backgroundImage: `url(${require('@/assets/'+objN1.img)})` }" >
      <svg class="maping" id="svg" v-hammer:panstart="captureOn" v-hammer:pan="mo" v-hammer:panend="captureOff" >
      <!-- <svg class="maping" id="svg"  @touchstart="captureOn" @touchmove="movil"  @touchend="captureOff"  v-on:mousedown="captureOn" v-on:mouseup="captureOff" v-on:mousemove="mo"> -->
         <circle id="ini" v-bind:cx="objN1.puntos_inicio_final[0].x1" v-bind:cy="objN1.puntos_inicio_final[0].y1" r="15" fill="blue"  v-on:touchstart="capturar_clik('inicio',1)" v-on:mousedown="capturar_clik('inicio',1)" />
            <circle id="fin" v-bind:cx="objN1.puntos_inicio_final[1].x1" v-bind:cy="objN1.puntos_inicio_final[1].y1" r="15" fill="orange" v-on:touchend="capturar_clik('fin',1)"  v-on:mousemove="capturar_clik('fin',1)" />
            
            <line v-for="lineas in objN1.camino_correcto" :key="lineas.length" v-on:id="lineas.x1" v-bind:x1="lineas.x1" v-bind:y1="lineas.y1" v-bind:x2="lineas.x2" v-bind:y2="lineas.y2" stroke="black" id="pared" stroke-width="5" fill="rgb(121,0,121)"></line>
            <!-- <circle id="stop" display="none" v-bind:cx="this.startX" v-bind:cy="this.startY" r="12" fill="green"/> -->
            <image xlink:href="@/assets/boom.png" id="boom" display="none" width="70" height="70" v-bind:x="this.startX-33" v-bind:y="this.startY-33" v-on:click="tc()" />
            <circle id="stop" display="none" v-bind:cx="this.startX" v-bind:cy="this.startY
            " r="15"  stroke="red" stroke-width="5" fill="transparent" v-on:click="verifica()">STOP</circle> 
            <image xlink:href="@/assets/azul_B.png" display="block" id="gris" width="50" height="50" v-bind:x="this.startX" v-bind:y="this.startY-50" v-on:touchstart="capturar_clik('inicio',1)" v-on:mousedown="capturar_clik('inicio',1)" />         
            <image xlink:href="@/assets/azul.png" display="none" id="azul" width="50" height="50" v-bind:x="this.startX" v-bind:y="this.startY-50" />
          </svg>

        </div>
        <!-- hola -->
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
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
      rapidez:false,
      mostrar:0,
      control:0,
      objN1:{img:"abejaima.png",puntos_inicio_final:[{x1:0,y1:0},{x1:0,y1:0}]}
      
      // plantilla_actual: plantilla,
    }
  }
  ,
  methods: {

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
            // console.log("no dibujo"+out)
            
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
            
            // if(estado_respondio==false){
              //  for (let desbloquear =0; desbloquear < this.objN1.ruta_correcta.length; desbloquear++) {
              //  document.getElementById(this.objN1.ruta_correcta[desbloquear].x1+'ok').style.display='block'; 
              // console.log(this.objN1.ruta_correcta[desbloquear].x1)
      //         }
      // }
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
         if(this.colision==true | this.isDrawing==false){
           break;
         }

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
         
              document.getElementById("pantallaActividad").style.backgroundColor='#F6535DCC';
              
              setTimeout(() => {
                document.getElementById("pantallaActividad").style.backgroundColor='white';
               },500); 
                
              //  console.log(this.startX+" , "+this.startY)
               document.getElementById('boom').style.display='block';
           

         }
         if(px>=this.objN1.fintX-5 & px<=this.objN1.fintX & py>=this.objN1.fintY-15 & py<=this.objN1.fintY+15){
           this.capturar_clik('fin',1)
         }
         if(px>=this.objN1.control[0].x1-2 & px<=this.objN1.control[0].x2+2 & py>=this.objN1.control[0].y1 & py<=this.objN1.control[0].y2){
           this.control=1;
         }
        //  console.log("estoy evaluando aun")
        // console.log(this.objN1.control[0].x1)
      
    },
    tc(){ 
      //  if(this.coliciones_realizadas!=0){
      console.log("sigue dibujando ");
      this.colision=false
      this.isDrawing=true
      document.getElementById('boom').style.display='none';
      //  }
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
    //  console.log(this.msg)
       var random_llena= Math.ceil(Math.random()*3);
      // var random_llena=1;
      console.log(random_llena);
      if(random_llena==1){
      this.startX=70,
      this.startY=175,
      this.objN1={tipo:"N1",
      img:"abejaima.png",
      imgok:"abejaok.png",
      camino_correcto:[{x1:70,y1:15,x2:70,y2:155},{x1:70,y1:200,x2:70,y2:335},{x1:70,y1:15,x2:390,y2:15},{x1:70,y1:335,x2:390,y2:335},{x1:116,y1:15,x2:116,y2:110},{x1:116,y1:153,x2:116,y2:200},{x1:70,y1:244,x2:162,y2:244},{x1:115,y1:108,x2:161,y2:108},{x1:115,y1:154,x2:161,y2:154},{x1:161,y1:154,x2:161,y2:290},{x1:115,y1:290,x2:208,y2:290},{x1:161,y1:62,x2:207,y2:62},{x1:207,y1:15,x2:207,y2:154},{x1:207,y1:200,x2:207,y2:245},{x1:207,y1:108,x2:300,y2:108},{x1:252,y1:245,x2:252,y2:291},{x1:253,y1:108,x2:253,y2:200},{x1:207,y1:200,x2:254,y2:200},{x1:250,y1:62,x2:345,y2:62},{x1:298,y1:154,x2:345,y2:154},{x1:298,y1:154,x2:298,y2:245},{x1:250,y1:245,x2:299,y2:245},{x1:298,y1:290,x2:345,y2:290},{x1:345,y1:60,x2:345,y2:290},{x1:390,y1:14,x2:390,y2:155},{x1:345,y1:200,x2:390,y2:200},{x1:390,y1:200,x2:390,y2:335}],
      puntos_inicio_final:[{x1:70,y1:178},{x1:390,y1:178}],
      fintX:390,
      fintY:178,
      control:[{x1:346,y1:120,x2:387,y2:128}],
      ruta_correcta:[{x1:70, y1:180, x2:95, y2:180},{x1:95, y1:180, x2:95, y2:132},{x1:95, y1:132, x2:186, y2:132},{x1:186, y1:132, x2:186, y2:267}],
      }
      }if(random_llena==2){
        this.startX=70,
      this.startY=175,
        this.objN1= {tipo:"N1",
      img:"avispaima.png",
      imgok:"avispaok.png",
      camino_correcto:[{x1:72,y1:11,x2:72,y2:150},{x1:72,y1:200, x2:72,y2:344},{x1:72,y1:11,x2:404,y2:11},{x1:72,y1:344,x2:404,y2:344},{x1:404,y1:11,x2:404,y2:150},{x1:404,y1:200, x2:404,y2:344},{x1:72,y1:150, x2:118,y2:150},{x1:118,y1:56, x2:118,y2:295},{x1:118,y1:56,x2:167,y2:56},{x1:118,y1:201,x2:167,y2:201},{x1:118,y1:295,x2:220,y2:295},{x1:167,y1:107,x2:167,y2:201},{x1:167,y1:105,x2:215,y2:105},{x1:215,y1:55,x2:215,y2:105},{x1:165,y1:246,x2:263,y2:246},{x1:213,y1:150,x2:213,y2:246},{x1:213,y1:150,x2:264,y2:150},{x1:262,y1:100,x2:262,y2:150},{x1:262,y1:200,x2:262,y2:345},{x1:262,y1:295,x2:315,y2:295},{x1:262,y1:55,x2:360,y2:55},{x1:310,y1:55,x2:310,y2:200},{x1:310,y1:200,x2:359,y2:200},{x1:310,y1:247,x2:359,y2:247},{x1:310,y1:105,x2:405,y2:105},{x1:358,y1:152,x2:358,y2:200},{x1:358,y1:248,x2:358,y2:345}],
      puntos_inicio_final:[{x1:70,y1:175},{x1:400,y1:175}],
      fintX:400,
      fintY:175,
      control:[{x1:359,y1:198,x2:359,y2:246}]
      }
      }if(random_llena==3){
      this.startX=76,
      this.startY=85, 
      this.objN1= {tipo:"N1",
      img:"palomaima.png",
      imgok:"palomaok.png",
      camino_correcto:[{x1:76,y1:17,x2:76,y2:64},{x1:76,y1:107,x2:76,y2:335},{x1:76,y1:17,x2:395,y2:17},{x1:76,y1:335,x2:395,y2:335},{x1:395,y1:17,x2:395,y2:245},{x1:395,y1:287,x2:395,y2:337},{x1:75,y1:62,x2:120,y2:62},{x1:75,y1:110,x2:167,y2:110},{x1:75,y1:152,x2:120,y2:152},{x1:75,y1:242,x2:167,y2:242},{x1:120,y1:287,x2:167,y2:287},{x1:167,y1:287,x2:167,y2:335},{x1:167,y1:60,x2:167,y2:110},{x1:167,y1:150,x2:167,y2:200},{x1:120,y1:198,x2:213,y2:198},{x1:167,y1:60,x2:214,y2:60},{x1:213,y1:60,x2:213,y2:110},{x1:167,y1:152,x2:305,y2:152},{x1:213,y1:197,x2:213,y2:245},{x1:213,y1:245,x2:260,y2:245},{x1:258,y1:245,x2:258,y2:288},{x1:210,y1:288,x2:258,y2:288},{x1:258,y1:60,x2:258,y2:150},{x1:258,y1:105,x2:395,y2:105},{x1:258,y1:105,x2:395,y2:105},{x1:302,y1:17,x2:302,y2:63},{x1:350,y1:60,x2:350,y2:105},{x1:255,y1:200,x2:350,y2:200},{x1:302,y1:200,x2:302,y2:290},{x1:302,y1:290,x2:395,y2:290},{x1:350,y1:150,x2:350,y2:200},{x1:350,y1:243,x2:395,y2:243}],
      puntos_inicio_final:[{x1:76,y1:85},{x1:395,y1:265}],
      fintX:395,
      fintY:265,
      control:[{x1:304,y1:239,x2:348,y2:244}]
      }
      }
         
   },  
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
  width: 460px;
  height: 350px;
  left: calc(50% - 21%);

}
.cva{
  // outline: 2px solid blue;
  left: calc(50% - 20%);
  position: absolute;
}
.maping{
width: 460px;
height: 350px;
display: flex;
flex-direction: row;
justify-content: center;
}
</style>
