<template>
  <div class="hello">
    <canvas id="lienzo" ref="canvas" class="cva" width="800" height="420"></canvas>
    <div class="img_f" id="img_f" v-bind:style="{ backgroundImage: `url(${require('@/assets/'+objN1.img)})` }" >
      <!-- <svg class="maping" id="svg"  @touchstart="captureOn" @touchmove="movil"  @touchend="captureOff"  v-on:mousedown="captureOn" v-on:mouseup="captureOff" v-on:mousemove="mo"> -->
        <svg class="maping" id="svg" v-hammer:panstart="captureOn" v-hammer:pan="mo" v-hammer:panend="captureOff" >
            <circle id="ini" v-bind:cx="objN1.puntos_inicio_final[0].x1" v-bind:cy="objN1.puntos_inicio_final[0].y1" r="12" fill="blue"  v-on:touchstart="capturar_clik('inicio',1)" v-on:mousedown="capturar_clik('inicio',1)" />
            <circle id="fin" v-bind:cx="objN1.puntos_inicio_final[1].x1" v-bind:cy="objN1.puntos_inicio_final[1].y1" r="12" fill="orange" v-on:touchend="capturar_clik('fin',1)"  v-on:mousemove="capturar_clik('fin',1)" />
           <circle id="stop" display="none" v-bind:cx="this.startX" v-bind:cy="this.startY
            " r="15"  stroke="red" stroke-width="5" fill="transparent"></circle>          
           <!-- <line v-bind:cx="objN1.control[0].x2" v-bind:cy="objN1.control[0].y2" r="15"  fill="red"/> -->
            <!-- <line v-for="lineas in objN1.control" :key="lineas.length" v-on:id="lineas.x1" v-bind:x1="lineas.x1" v-bind:y1="lineas.y1" v-bind:x2="lineas.x2" v-bind:y2="lineas.y2" stroke="blue" id="pared" stroke-width="5" fill="rgb(121,0,121)" ></line> -->
            
            <line v-for="lineas in objN1.camino_correcto" :key="lineas.length" v-on:id="lineas.x1" v-bind:x1="lineas.x1" v-bind:y1="lineas.y1" v-bind:x2="lineas.x2" v-bind:y2="lineas.y2" stroke="black" id="pared" stroke-width="4" fill="rgb(121,0,121)" ></line>
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
      coliciones_realizadas:3,  
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
             if (this.captureToggle==true & this.llave_tiempo==0 & this.coliciones_realizadas!=0) {
         var out = e.velocity<0?e.velocity*=-1:e.velocity;
        if(out<=0.5){
           var x= e.center.x;
           var y= e.center.y;
           this.trazar(x,y)
          //  console.log("si dibujo"+out)
         
          
        }else{
            this.stop(true)
            console.log("no dibujo"+out)
            
        }
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
      //  console.log(x+" , "+y)
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
               
               document.getElementById("pantallaActividad").style.backgroundColor='#F6535DCC';
              
              setTimeout(() => {
                document.getElementById("pantallaActividad").style.backgroundColor='white';
               },500); 
               }       
                console.log(this.startX+" , "+this.startY)
               document.getElementById('boom').style.display='block';
         }
         if(px>=this.objN1.fintX-5 & px<=this.objN1.fintX & py>=this.objN1.fintY-5 & py<=this.objN1.fintY+5){
           this.capturar_clik('fin',1)
         }
         if(px>=this.objN1.control[0].x1-2 & px<=this.objN1.control[0].x2+2 & py>=this.objN1.control[0].y1 & py<=this.objN1.control[0].y2){
           this.control=1;
         }
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
       var random_llena= Math.ceil(Math.random()*2);
      // var random_llena=2
      console.log(random_llena);
      if(random_llena==1){
          this.startX=84,
          this.startY=225
          this.objN1={tipo:"N3",
          img:"paloma3A.png",
          imgok:"paloma3ok.png",
         camino_correcto:[{x1:84,y1:20,x2:84,y2:207},{x1:83,y1:20,x2:748,y2:20},{x1:747,y1:20,x2:747,y2:175},{x1:83,y1:399,x2:748,y2:399},{x1:84,y1:241,x2:84,y2:399},{x1:747,y1:209,x2:747,y2:399},{x1:83,y1:48,x2:124,y2:48},{x1:83,y1:243,x2:125,y2:243},{x1:83,y1:336,x2:193,y2:336},{x1:123,y1:81,x2:123,y2:208},{x1:123,y1:245,x2:123,y2:273},{x1:123,y1:303,x2:123,y2:336},{x1:123,y1:369,x2:123,y2:399},{x1:123,y1:83,x2:160,y2:83},{x1:123,y1:146,x2:160,y2:146},{x1:123,y1:207,x2:160,y2:207},{x1:123,y1:273,x2:160,y2:273},{x1:158,y1:49,x2:158,y2:83},{x1:158,y1:114,x2:158,y2:149},{x1:158,y1:204,x2:158,y2:240},{x1:158,y1:272,x2:158,y2:303},{x1:158,y1:334,x2:158,y2:368},{x1:159,y1:117,x2:233,y2:117},{x1:159,y1:177,x2:306,y2:177},{x1:159,y1:241,x2:233,y2:241},{x1:195,y1:271,x2:343,y2:271},{x1:157,y1:305,x2:307,y2:305},{x1:193,y1:20,x2:193,y2:82},{x1:193,y1:145,x2:193,y2:210},{x1:193,y1:242,x2:193,y2:274},{x1:193,y1:369,x2:193,y2:400},{x1:232,y1:49,x2:232,y2:145},{x1:232,y1:178,x2:232,y2:210},{x1:232,y1:306,x2:232,y2:337},{x1:232,y1:369,x2:232,y2:401},{x1:268,y1:20,x2:268,y2:50},{x1:232,y1:81,x2:306,y2:81},{x1:233,y1:144,x2:379,y2:144},{x1:268,y1:210,x2:268,y2:241},{x1:268,y1:336,x2:268,y2:367},{x1:305,y1:51,x2:305,y2:81},{x1:268,y1:114,x2:416,y2:114},{x1:305,y1:177,x2:305,y2:210},{x1:305,y1:240,x2:305,y2:269},{x1:270,y1:363,x2:270,y2:363},{x1:305,y1:50,x2:344,y2:50},{x1:270,y1:210,x2:381,y2:210},{x1:270,y1:368,x2:306,y2:368},{x1:344,y1:22,x2:344,y2:52},{x1:344,y1:82,x2:344,y2:113},{x1:344,y1:145,x2:344,y2:178},{x1:344,y1:209,x2:344,y2:241},{x1:344,y1:305,x2:344,y2:368},{x1:305,y1:336,x2:342,y2:336},{x1:303,y1:367,x2:303,y2:401},{x1:341,y1:82,x2:381,y2:82},{x1:343,y1:240,x2:414,y2:240},{x1:380,y1:50,x2:380,y2:83},{x1:380,y1:51,x2:563,y2:51},{x1:414,y1:116,x2:414,y2:176},{x1:378,y1:176,x2:378,y2:209},{x1:376,y1:243,x2:376,y2:303},{x1:344,y1:305,x2:453,y2:305},{x1:344,y1:367,x2:380,y2:367},{x1:378,y1:176,x2:453,y2:176},{x1:416,y1:51,x2:416,y2:81},{x1:416,y1:209,x2:416,y2:242},{x1:416,y1:337,x2:416,y2:401},{x1:453,y1:82,x2:453,y2:147},{x1:415,y1:211,x2:454,y2:211},{x1:453,y1:240,x2:489,y2:240},{x1:417,y1:275,x2:454,y2:275},{x1:451,y1:272,x2:451,y2:304},{x1:380,y1:337,x2:526,y2:337},{x1:453,y1:339,x2:453,y2:369},{x1:454,y1:144,x2:490,y2:144},{x1:489,y1:52,x2:489,y2:81},{x1:489,y1:145,x2:489,y2:336},{x1:489,y1:367,x2:489,y2:401},{x1:490,y1:80,x2:527,y2:80},{x1:490,y1:114,x2:564,y2:114},{x1:490,y1:176,x2:600,y2:176},{x1:524,y1:210,x2:600,y2:210},{x1:490,y1:273,x2:600,y2:273},{x1:524,y1:304,x2:562,y2:304},{x1:527,y1:145,x2:527,y2:175},{x1:527,y1:212,x2:527,y2:242},{x1:524,y1:338,x2:524,y2:368},{x1:561,y1:53,x2:561,y2:147},{x1:561,y1:241,x2:561,y2:303},{x1:561,y1:369,x2:561,y2:401},{x1:560,y1:80,x2:637,y2:80},{x1:560,y1:144,x2:637,y2:144},{x1:598,y1:177,x2:598,y2:242},{x1:598,y1:239,x2:636,y2:239},{x1:598,y1:304,x2:636,y2:304},{x1:563,y1:336,x2:710,y2:336},{x1:600,y1:113,x2:600,y2:143},{x1:600,y1:368,x2:600,y2:401},{x1:600,y1:50,x2:673,y2:50},{x1:634,y1:81,x2:634,y2:114},{x1:634,y1:146,x2:634,y2:210},{x1:634,y1:241,x2:634,y2:305},{x1:634,y1:339,x2:634,y2:367},{x1:633,y1:112,x2:673,y2:112},{x1:633,y1:206,x2:673,y2:206},{x1:672,y1:48,x2:672,y2:83},{x1:670,y1:111,x2:670,y2:145},{x1:670,y1:206,x2:670,y2:242},{x1:670,y1:273,x2:670,y2:336},{x1:672,y1:80,x2:711,y2:80},{x1:672,y1:142,x2:710,y2:142},{x1:672,y1:176,x2:710,y2:176},{x1:672,y1:240,x2:710,y2:240},{x1:672,y1:275,x2:710,y2:275},{x1:708,y1:82,x2:708,y2:113},{x1:708,y1:142,x2:708,y2:209},{x1:708,y1:243,x2:708,y2:273},{x1:709,y1:50,x2:748,y2:50},{x1:709,y1:110,x2:748,y2:110},{x1:709,y1:209,x2:748,y2:209},{x1:709,y1:305,x2:748,y2:305},{x1:673,y1:367,x2:748,y2:367}],
         puntos_inicio_final:[{x1:84,y1:225},{x1:744,y1:194}],
         fintX:744,
         fintY:194,
         control:[{x1:710,y1:109,x2:710,y2:145}]
          }
        }
        if (random_llena==2) {
       this.startX=55,
        this.startY=93
        this.objN1={tipo:"N2",
        img:"abeja3.png",
        imgok:"abeja3ok.png",
        camino_correcto:[{x1:8,y1:2,x2:794,y2:2},{x1:9,y1:3,x2:9,y2:413},{x1:793,y1:3,x2:793,y2:413},{x1:8,y1:413,x2:795,y2:413},{x1:8,y1:93,x2:42,y2:93},{x1:39,y1:95,x2:39,y2:208},{x1:39,y1:233,x2:39,y2:345},{x1:40,y1:205,x2:106,y2:205},{x1:40,y1:232,x2:141,y2:232},{x1:75,y1:93,x2:75,y2:177},{x1:75,y1:254,x2:75,y2:322},{x1:38,y1:343,x2:336,y2:343},{x1:8,y1:366,x2:139,y2:366},{x1:40,y1:390,x2:139,y2:390},{x1:77,y1:256,x2:172,y2:256},{x1:75,y1:96,x2:205,y2:96},{x1:104,y1:116,x2:141,y2:116},{x1:107,y1:141,x2:139,y2:141},{x1:75,y1:161,x2:106,y2:161},{x1:138,y1:118,x2:138,y2:143},{x1:106,y1:140,x2:106,y2:164},{x1:106,y1:187,x2:106,y2:206},{x1:106,y1:276,x2:106,y2:320},{x1:104,y1:188,x2:141,y2:188},{x1:138,y1:190,x2:138,y2:233},{x1:74,y1:321,x2:369,y2:321},{x1:139,y1:162,x2:173,y2:162},{x1:172,y1:2,x2:172,y2:255},{x1:172,y1:340,x2:172,y2:392},{x1:172,y1:47,x2:205,y2:47},{x1:172,y1:139,x2:205,y2:139},{x1:139,y1:278,x2:206,y2:278},{x1:139,y1:301,x2:272,y2:301},{x1:138,y1:366,x2:138,y2:390},{x1:140,y1:279,x2:140,y2:301},{x1:204,y1:26,x2:238,y2:26},{x1:204,y1:71,x2:238,y2:71},{x1:204,y1:117,x2:238,y2:117},{x1:204,y1:162,x2:238,y2:162},{x1:204,y1:185,x2:204,y2:252},{x1:204,y1:368,x2:204,y2:415},{x1:237,y1:27,x2:237,y2:300},{x1:204,y1:26,x2:238,y2:26},{x1:204,y1:252,x2:303,y2:252},{x1:208,y1:369,x2:304,y2:369},{x1:271,y1:24,x2:271,y2:160},{x1:238,y1:206,x2:436,y2:206},{x1:269,y1:230,x2:304,y2:230},{x1:269,y1:230,x2:303,y2:230},{x1:238,y1:391,x2:468,y2:391},{x1:273,y1:277,x2:303,y2:277},{x1:269,y1:26,x2:368,y2:26},{x1:269,y1:70,x2:405,y2:70},{x1:269,y1:116,x2:435,y2:116},{x1:303,y1:230,x2:303,y2:318},{x1:303,y1:46,x2:467,y2:46},{x1:303,y1:92,x2:467,y2:92},{x1:303,y1:140,x2:467,y2:140},{x1:303,y1:160,x2:467,y2:160},{x1:337,y1:207,x2:337,y2:298},{x1:335,y1:344,x2:335,y2:390},{x1:370,y1:231,x2:370,y2:366},{x1:403,y1:207,x2:403,y2:207},{x1:401,y1:206,x2:401,y2:345},{x1:434,y1:206,x2:434,y2:345},{x1:370,y1:365,x2:435,y2:365},{x1:400,y1:22,x2:400,y2:47},{x1:435,y1:3,x2:435,y2:45},{x1:435,y1:69,x2:435,y2:95},{x1:466,y1:25,x2:466,y2:161},{x1:468,y1:185,x2:468,y2:365},{x1:500,y1:25,x2:500,y2:184},{x1:500,y1:208,x2:500,y2:276},{x1:500,y1:321,x2:500,y2:414},{x1:534,y1:208,x2:534,y2:276},{x1:534,y1:299,x2:534,y2:389},{x1:533,y1:94,x2:533,y2:161},{x1:503,y1:25,x2:633,y2:25},{x1:503,y1:71,x2:599,y2:71},{x1:534,y1:48,x2:599,y2:48},{x1:435,y1:185,x2:699,y2:185},{x1:468,y1:298,x2:567,y2:298},{x1:533,y1:96,x2:762,y2:96},{x1:598,y1:50,x2:598,y2:70},{x1:565,y1:118,x2:565,y2:160},{x1:565,y1:184,x2:565,y2:252},{x1:565,y1:298,x2:565,y2:412},{x1:631,y1:26,x2:631,y2:69},{x1:631,y1:26,x2:631,y2:69},{x1:666,y1:3,x2:666,y2:46},{x1:500,y1:276,x2:795,y2:276},{x1:566,y1:116,x2:765,y2:116},{x1:564,y1:162,x2:663,y2:162},{x1:598,y1:138,x2:730,y2:138},{x1:564,y1:251,x2:665,y2:251},{x1:664,y1:69,x2:731,y2:70},{x1:728,y1:47,x2:795,y2:47},{x1:730,y1:48,x2:730,y2:70},{x1:665,y1:138,x2:665,y2:165},{x1:763,y1:115,x2:763,y2:229},{x1:698,y1:162,x2:698,y2:182},{x1:698,y1:232,x2:698,y2:279},{x1:730,y1:161,x2:765,y2:161},{x1:731,y1:163,x2:731,y2:207},{x1:601,y1:231,x2:700,y2:231},{x1:601,y1:207,x2:667,y2:207},{x1:602,y1:205,x2:602,y2:232},{x1:730,y1:229,x2:730,y2:253},{x1:728,y1:255,x2:765,y2:255},{x1:274,y1:184,x2:405,y2:184},{x1:700,y1:24,x2:793,y2:24}],
        puntos_inicio_final:[{x1:55,y1:93},{x1:569,y1:288}],
        fintX:569,
        fintY:288,
        control:[{x1:466,y1:238,x2:501,y2:243}]
          }
          }if(random_llena==3){
          this.startX=63,
          this.startY=175
          this.objN1={tipo:"N2",
          img:"conejoimaa.png",
          imgok:"conejook.png",
         camino_correcto:[{x1:63,y1:17,x2:63,y2:157},{x1:63,y1:194,x2:63,y2:333},{x1:63,y1:20,x2:537,y2:20},{x1:63,y1:330,x2:537,y2:330},{x1:535,y1:17,x2:535,y2:157},{x1:535,y1:194,x2:535,y2:333},{x1:65,y1:123,x2:150,y2:123},{x1:65,y1:155,x2:150,y2:155},{x1:65,y1:228,x2:105,y2:228},{x1:65,y1:295,x2:105,y2:295},{x1:108,y1:23,x2:108,y2:87},{x1:108,y1:193,x2:108,y2:231},{x1:106,y1:86,x2:148,y2:86},{x1:106,y1:262,x2:148,y2:262},{x1:148,y1:155,x2:148,y2:297},{x1:149,y1:193,x2:193,y2:193},{x1:145,y1:297,x2:193,y2:297},{x1:148,y1:51,x2:235,y2:51},{x1:195,y1:53,x2:195,y2:158},{x1:195,y1:226,x2:195,y2:262},{x1:197,y1:121,x2:238,y2:121},{x1:197,y1:260,x2:238,y2:260},{x1:236,y1:23,x2:236,y2:53},{x1:236,y1:120,x2:236,y2:228},{x1:236,y1:259,x2:236,y2:329},{x1:232,y1:87,x2:277,y2:87},{x1:234,y1:228,x2:320,y2:228},{x1:238,y1:299,x2:322,y2:299},{x1:277,y1:52,x2:277,y2:193},{x1:277,y1:227,x2:277,y2:262},{x1:279,y1:55,x2:363,y2:55},{x1:279,y1:155,x2:363,y2:155},{x1:322,y1:88,x2:322,y2:122},{x1:322,y1:191,x2:322,y2:231},{x1:320,y1:86,x2:493,y2:86},{x1:364,y1:122,x2:448,y2:122},{x1:324,y1:195,x2:407,y2:195},{x1:321,y1:260,x2:407,y2:260},{x1:366,y1:121,x2:366,y2:158},{x1:366,y1:227,x2:366,y2:330},{x1:406,y1:50,x2:406,y2:85},{x1:406,y1:156,x2:406,y2:198},{x1:406,y1:259,x2:406,y2:297},{x1:404,y1:50,x2:454,y2:50},{x1:406,y1:227,x2:452,y2:227},{x1:406,y1:296,x2:492,y2:296},{x1:449,y1:121,x2:449,y2:157},{x1:449,y1:192,x2:449,y2:262},{x1:451,y1:154,x2:493,y2:154},{x1:451,y1:195,x2:537,y2:195},{x1:494,y1:227,x2:494,y2:299},{x1:491,y1:51,x2:535,y2:51},{x1:491,y1:123,x2:535,y2:123},{x1:491,y1:84,x2:491,y2:126},{x1:491,y1:153,x2:491,y2:198}],
         puntos_inicio_final:[{x1:63,y1:175},{x1:534,y1:175}],
         fintX:521,
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
  width: 800px;
  height: 420px;
  // top: calc(30% - 10%);
  // // left: calc(33% - 0%);
  left: calc(28% - 15%);
  // margin: auto; 
}
.cva{
 left: calc(28% - 13%);
  margin-left: 0%;
  position: absolute;
  // outline: 2px solid blue;
}
.maping{
width: 800px;
height: 420px;
display: flex;
flex-direction: row;
justify-content: center;
}
@media screen and (max-width: 1024px) {
.cva{
  left: 10%;
}
.img_f{
 
left:7%;

}
  
}

</style>
