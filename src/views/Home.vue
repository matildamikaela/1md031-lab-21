<template>
  <div>
    <header class="header">
      <div>
        <img src= "https://images.fineartamerica.com/images/artworkimages/mediumlarge/1/four-happy-cats-website-banner-susan-schmitz.jpg" alt="Happy cats" title="Nöjda kunder">
      </div>
        <h1>Välkommen till katten Silvers spektakulära godiskiosk </h1>
    </header>

<main>

       <section id="assortement">
         <h2>Välj godis</h2>
        <p>  Här väljer du godis</p>



         <div class="wrapper">
                 <Burger v-for="burger in burgers"
                         v-bind:burger="burger"
                         v-bind:key="burger.name"
                         v-on:orderedBurger="addToOrder($event)"/>
          </div>
        </section>


       <section id="contact">
         <h2> Beställningsinformation</h2>
          <p>Fyll i nödvändig information nedan för att lägga din beställning</p>

          <h3>Leveransinformation</h3>

          <form>
            <p>
                <label for="firstname">Namn</label><br>
                <input type="text" id="firstname" v-model="fn" required="required" placeholder="För- och efternamn">
            </p>

            <p>
              <label for="email">E-postadress</label><br>
                <input type="email" id="email" v-model="em" required="required" placeholder="Email-adress">
            </p>

<p>Klicka för att sätta ut en prick vid önskad leveransadress</p>
            <!--MAP!!!!!!!!!!!!!!!-->
                    <div id="mapWrapper">
                        <div id="map" v-on:click="setLocation">
                          <p id= "T" v-bind:style="{left:location.x + 'px', top: location.y + 'px'}" >
                          T
                          </p>
                        </div>
                    </div>
          <!--<p>
            <label for="adress">Gata</label><br>
              <input type="text" id="adress" v-model="ad" required="required" placeholder="Gatunamn">
          </p>

          <p>
          <label for="adress">Gatunummer</label><br>
            <input type="number" id="adress" v-model="nr" required="required" placeholder="Välj nummer">
        </p>
-->
        <p>
           <label for="payment">Betalningsmetod</label><br>
           <select id="payment" v-model="bm">
               <option selected="selected" >Klia silver bakom öronen</option>
               <option>Faktura Klarna</option>
               <option>Kortbetalning</option>
               <option>Presentkort</option>
               <option>Cash money</option>
           </select>
        </p>

       <p>Kön:</p>
       <div>


          <input type="radio" id="alt1" v-model="gen" value="hona" >
          <label for="alt1">Hona</label> <br>

          <input type="radio" id="alt2" v-model="gen" value="hane" >
          <label for="alt2">Hane</label> <br>

          <input type="radio" id="alt3" v-model="gen" value="kastrerad" >
          <label for="alt3">Kastrerad/steriliserad</label>


        </div>

        </form>
      </section> <!--FLYTTADE från under slutet av button-->

        <button v-on:click="infoSubmission" type="submit">
          <img src="https://www.kindpng.com/picc/m/340-3402170_shop-now-png-transparent-shop-now-button-png.png" style="width: 150px" >
        </button>


</main>
    <hr>
    <footer>
       &copy; 2021 KitCat Inc.
    </footer>
  </div>
</template>





<script>
import Burger from '../components/Burger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'


const socket = io();

////Object Constructor Function:
/*function MenuItem(name, url, txt, pu, km) {
this.name=name;
this.image=url;
this.beskrivning=txt;
this.pulver=pu;
this.kattmynta=km;
}
*/

// Objects are then instantiated using the *new* keyword
/*let ArrayOfBurgers= [new MenuItem("Partymix", "https://cdn11.bigcommerce.com/s-ibzrultj27/products/2365/images/4405/latz-party-mix-orginal-2e__16219.1615631011.386.513.jpg?c=1" , "Lika goda till vardags som till fest!", false, false),
new MenuItem("Torkad firre", "https://www.kattkompaniet.nu/images/2.61863/kattgodis-torkad-smafisk.jpeg", "Fångade av katten Justus personligen", true, false ),
new MenuItem("Kattmynta", "https://img.tradera.net/medium/929/399718929_d89c253b-f565-4b42-a31f-721587dd49d2.jpg", "Odlad av gatukatter i Amsterdam", false, true)];

console.log(ArrayOfBurgers);
*/

export default {
  name: 'Home',
  components: {
    Burger
  },

  //Data object:
  data: function () {
    return {

      burgers: menu,
      fn:"",
      em:"",
      bm:"",
      gen:"",
      amountOrdered:"",
      orderedBurger:{Partymix:0, Torkad:0, Kattmynta:0},
      location: { x:0, y:0 },
      orderInfo:[],



    }
  },
  methods: {
    addToOrder: function (event) {
  this.orderedBurger[event.name] = event.amount;
},
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    addOrder: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: event.clientX - 10 - offset.x,
                                           y: event.clientY - 10 - offset.y },
                                /*orderItems: ["food", "food"]*/
                              }
                 );
    },
    infoSubmission: function (){

      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: this.location.x,
                                           y: this.location.y  },
                                orderItems: this.orderedBurger,
                                orderInfo: {name: this.fn, email:this.em, payment: this.bm, gender:this.gen}
                              },
                 );
      console.log(
        this.fn,
        this.em,
        this.bm,
        this.gen,
        this.amountOrdered,
        this.orderedBurger)
    },
    setLocation: function() {
      var offset= event.currentTarget.getBoundingClientRect();
      this.location.x=event.clientX-offset.left-10;
      this.location.y=event.clientY-offset.top-10;

    },
  },
}
</script>

<style>
@import 'https://fonts.googleapis.com/css?family=Pacifico|Dosis';
/* Your comment goes here */
body {
   font-family: sans-serif;
   font-style: italic;
   font-size: 18px;
}

.header {
   text-align: center;
   font-size: 1.5em;
   color: #85498c;
   margin: 10px 50px;
  /* height: ;*/
   overflow: hidden;
   padding: 10px;



}

.header div{
     opacity: .5;
     width: 100%;
     height: auto;

}

.header h1{
  position: absolute;
  margin-top: -2.5em;
  /*top: ;
	left: ;*/
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


#contact {
   color: black  ;
   margin: 10px 50px;
   border: 2px dashed black;
   border-radius: 25px;
   padding: 10px 50px;
}

button:hover {
   color: #b62f89;
   background-color:orange;
   cursor:copy;
 }

 button{
 margin: 0px 50px;
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

  #map {
    width: 1920px;
    height: 1078px;
    background: url("/img/polacks.jpg");
  }

  .samesize{
    width: 15em;
  }

  #mapWrapper{ /*containing wrapper*/
    overflow: scroll;
    position: relative;
    width: relative;
    height: 500px;
    margin: 0;
    padding: 0;
  }

  #T{
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width:20px;
    height:20px;

  }
</style>
