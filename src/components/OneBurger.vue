<template>
 <div class="BigBurgers">

  <h3> {{ burger.name }}</h3>
  <img v-bind:src= "burger.url" id="burgerImage">
  <div class="allergies">
    <ul>
      <p>
        <span v-if= "burger.lactose > false">
    Contains lactose
  </span>
      </p>
      <p><span v-if= "burger.gluten >false">
    Contains gluten
  </span></p>

    </ul>
    
  </div>
  <div class="controlAmmount">
    
  </div>
  <div id= "listnerbuttons"> 
    <span>{{ amountOrdered }}</span>
    <button type="button" v-on:click="increaseNumber"> 
                    +
            </button>
            
    <button type="button" v-on:click="decreaseNumber">
                    -
            </button>
  </div>
    

 </div>                
</template>

<script>
export default {
  
  name: 'OneBurger',
  props: {
    burger: Object,
    required: true
  },

  data: function () {
  return {
    amountOrdered: 0,
  }
},
methods:{
  increaseNumber: function (){
      this.amountOrdered ++;
      this.$emit('add-to-order', this.burger.name)
      console.log(this.burger.name)
      
  },
  decreaseNumber: function (){
    if( this.amountOrdered > 0){
      this.amountOrdered --;
      this.$emit('remove-from-order', this.burger.name)
      
    }
  }
}
  }

</script>


<style scoped>

</style>