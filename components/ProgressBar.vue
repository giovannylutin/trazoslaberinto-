<template>
  <div id="hello" class="hello">
  
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
      // plantilla_actual: plantilla,
    }
  }
  ,
  methods: {
         
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
