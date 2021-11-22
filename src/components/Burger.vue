<template>

<div>

 <div class="box" style ="float:left;">
            <h3>{{ burger.name }}</h3>
            <img v-bind:src="burger.image" class="samesize">
            <ul>
                <li>{{burger.beskrivning}}</li>
                <li v-if="burger.pulver">Innehåller <span class="allergener"> pulver</span></li>
                <li v-if="burger.kattmynta">Innehåller <span class="allergener"> kattmynta</span></li>
            </ul>
            <br>

    <div class="editAmountButtons">


        <button v-on:click="minusBurger" type="submit"> -  </button>

        Antal: {{ amountOrdered }}

        <button v-on:click="addBurger" type="submit"> +   </button>


    </div>
  </div>
</div>

</template>

<script>
export default {
  name: 'Burger',
  props: {
    burger: Object
  },
  data: function () {
  return {
    amountOrdered: 0,
  }
},
methods:{
  minusBurger: function () {
    this.amountOrdered -= 1
    if (this.amountOrdered<1){
      this.amountOrdered=0;
    }

  },
  addBurger: function (){
    this.amountOrdered+= 1 ;
    this.$emit('orderedBurger', {
            name: this.burger.name,
            amount: this.amountOrdered
                                }
                                );
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
body {
   font-family: sans-serif;
   font-style: italic;
   font-size: 18px;
}

.allergener {
   /*text-transform: uppercase;*/
   font-weight: bold;
}

#assortement {
   color: #444;
   background-color:Lavender;
   margin: 10px 50px;
   border: 2px dashed #Lavender;
   border-radius: 25px;
   padding: 10px 50px;
}

#assortement div {
   padding: 4px;
}
 .wrapper {
      display: grid;
      grid-gap: 10px;
      grid-template-columns: 33% 33% 33%;
      background-color: #Lavender;
      color: #444;
  }

  .box {
      color: #Black;
      border-radius: 5px;
      padding: 20px;
      font-size: 100%;
  }

  .samesize{
  height: 200px
  }

.editAmountButtons{
  font-size: 20px;
}

</style>
