
<template>

  <header >
      <div id="headertext" style="position: relative;" >
         <p> <h1>Welcome to Big Burger Online</h1></p>
      </div>
    
    </header>  
    <main>

    <section class="BurgerSelect">
      <div class="wrapper">
        <Burger v-for="burger in burgers":key="burger.name" :burger="burger" @add-to-order="addToOrder($event)"@remove-from-order="removeFromOrder($event)" />
        </div>
      
    </section>
  
 <section class="CustomerInfo" >
      <h3>Customer Information</h3>
    <form>

<div  style="display: inline-block">
      <h4 style="text-indent: 50px;">Provide your delivery information</h4>
   <p>
         <label for="fullname">Full Name</label><br>
         <input type="text" id="fullname" v-model="fullName" required="required" placeholder="Full name">
     </p>
     <p>
      <label for="email">Email</label><br>
         <input type="email" id="email" v-model="email" required="required" placeholder="E-mail address">
        </p>    

        <p>
      <label for="phoneNr">Telephone Number</label><br>
         <input type="phoneNumber" id="phoneNumber" v-model="telephoneNumber" required="required" placeholder="Phone number">
        </p>    
<p>
   <label>Gender:</label><br>
   <input type="radio" id="male" v-model="gender" value="Male">
   <label for="male">Male</label><br>
   
   <input type="radio" id="female" v-model="gender" value="Female">
   <label for="female">Female</label><br>
   
   <input type="radio" id="other" v-model="gender" value="Other">
   <label for="other">Other</label><br>
   
   <input type="radio" id="noAnswer" v-model="gender" value="Do not wish to enter">
   <label for="noAnswer">Do not wish to enter</label>
   </p>
</div>

<div>
  <h4 style="text-indent: 50px;">Payment information</h4>
         
         <p>
            <label for="PaymentMethod ">Choose payment method</label>
            <select id="PaymentMethod" v-model="paymentMethod" >
                <option selected="selected">Swish  </option>
                <option>Card</option>
                <option>Klarna</option>
                <option>Faktura</option>
               
            </select>
         </p>
         
    </div>
        <div class="mapContainer">
            <div id="map" v-on:click="setLocation">
            <div v-bind:style="{left: location.x + 'px', top: location.y + 'px'}" id="target">T</div>
        </div>
    </div>
        
    </form>
   </section>
         <p>
            <button type="button" v-on:click="addOrder">
                    Submit
            </button>
          

         </p>

  </main>
  
    <hr>
    <footer>
       Daniels Burgers &copy;	
    </footer>

</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io("localhost:3000");
function MenuItem(name, URL, kCal, ifGluten, ifLactose) {
    this.name= name;
    this.url= URL;
    this.calories= kCal
    this.containsGluten= ifGluten;
    this.containsLactose= ifLactose

}
let burgersArray= [new MenuItem("Big Burger", "public\img\BigBurgers.jpg" , 420, true, true),
          new MenuItem("Vegan Burger", "public\img\VeganBurger.png", 420, true, false),
          new MenuItem("Original Burger", "public\img\OriginalBurger.png" , 420, true, true)]

export default {

  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu,
      orderedBurger: {},
      fullName: "",
      email: "", 
      telephoneNumber: "",
      gender: "",
      paymentMethod: "",  
      location: { x: 0,  y: 0},

      
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    setLocation: function(event){
        var offset = {
            x: event.currentTarget.getBoundingClientRect().left,
            y: event.currentTarget.getBoundingClientRect().top,
        };

            this.location.x= event.clientX - offset.x;
            this.location.y=  event.clientY  - offset.y;
            console.log("Location set to"+ this.location);
    },   
    addOrder: function () {
        if (Object.keys(this.orderedBurger).length === 0) {
        console.log("No burgers added to the order.");
        return; // Exit if no burgers are in the order
        }

      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: this.location.x, y: this.location.y},
                                orderItems: this.orderedBurger,
                                CustomerInfo: { name: this.fullName,
                                email: this.email,
                                phone: this.telephoneNumber,
                                gender: this.gender,
                                payment: this.paymentMethod,
                                }
                                   
                              }
                 );
                console.log("Order Sent", {
                    orderItems: this.orderedBurger
                })
                
    },
    addToOrder: function(burgerName){
        console.log("in add to")
        if (!this.orderedBurger[burgerName]) {
        this.orderedBurger[burgerName] = 1; 
    } else {
        this.orderedBurger[burgerName]++; 
    }
    console.log(this.orderedBurger)
        
    },
    removeFromOrder: function (burgerName) {
    if (this.orderedBurger[burgerName]) {
        this.orderedBurger[burgerName]--; // Decrement the count if it exists
        if (this.orderedBurger[burgerName] <= 0) {
            delete this.orderedBurger[burgerName]; // Remove the key if count reaches 0
        }
        console.log("Removed " + burgerName + " from order. Current count: " + (this.orderedBurger[burgerName] || 0));
    } else {
        console.log(burgerName + " is not in the order.");
    }
},
    }
}

    
  
</script>

<style>
 @import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');

@import 'https://fonts.googleapis.com/css?family=Pacifico|Dosis';
body {
    font-size: 12pt;
}


button{
    margin-left: 200px;
    margin-right: 200px;
    border-style: inset;
    border-radius: 100px;
}
button:hover {
    background-color: #ff0000; /* Default blue background */
            color: white; /* Default white text */
            border: none;
            padding: 10px 20px;
            font-size: 16px;
            cursor: crosshair;
            transition: background-color 0.5s ease;
 }

 p{
  color: black;
 }

h1 {
    font-family: 'Times New Roman';
    font-size: 40pt;
}

h2 {
    font-family: 'Times New Roman';
    font-size: 36pt;
}
h3{
    font-family: 'Times New Roman';
    font-size: 36pt;
}

h4{
    font-family: Arial, Helvetica, sans-serif;
    font-size: 30pt;
}
main, header, footer, nav ul {
    max-width: 80rem;
    margin: 0 auto 0 auto;
}
main {
    background-color: rgb(255, 255, 255);
}

/* nav ul li {
    display: inline-block;
    background-color: grey;
    padding: 1em;
    margin: 1em;
} */

.CustomerInfo{
    color: #000000;
    background-color: rgb(255, 255, 255);
    border-style: inset;
    margin-top: 10px;
    padding: 10px;
    
}
.BurgerSelect{
    color: #950b0b;
    background-color: rgb(0, 0, 0);
    margin-top: 10px;
    padding: 10px;
    border-style: dashed;
    border-color: aliceblue;
    border-spacing: 10px;
    
    
    
}
#Gluten{
    text-transform: uppercase;
    color: #5737e5;
}
#Egg{
    text-transform: uppercase;
    color: #48e537;
}
#Meat{
    text-transform: uppercase;
    color: #320893;
}
#Cheese{
    text-transform: uppercase;
    color: #3218c3;
}
#SoyProtein{
    text-transform: uppercase;
    color: #48e537;
}
header {
  
    background-image: url(C:\Users\danie\VSCODE\Lab_projects\Lab1_mk2\public\img\Vy_over_Uppsala.jpg);

    overflow: hidden;
    width: 100%;
    height: auto;
    opacity: 1;}

header h1 {
    width:40rem;
    color: #ffffff;
    margin: 0 auto;
    text-align: center;
}

nav ul {
    display: grid;
    grid-template-columns: repeat(auto-fill, 9.25em);
    gap: 1em;
    padding: 0;
}

nav li {
    display: block;
    background-color: grey;
    padding: 1em;
}

.Very-good {
    color: green;
}

.Master {
    color: green;
    font-weight: bold;
}

.wrapper {
    display: grid;
    grid-gap: 10px; 
    grid-template-columns: repeat(3, 1fr);
    grid-template-rows: auto; 
    background-color: #f8f9fa; 
    color: #444;
    padding: 20px; 
    border-radius: 10px; 
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1); 
}

.box {
    background-color: #000207;

    border-radius: 5px; 
    padding: 20px;
    font-size: 150%;
    text-align: center; 
    display: flex; 
    justify-content: center;
    align-items: center;
}

.a {
    grid-column: 1; 
    grid-row: 1 / span 2; }

.b {
    grid-column: 2; 
    grid-row: 1 / span 2;
}

.c {
    grid-column: 3;
    grid-row: 1 / span 2;
}

#burgerImage{
  max-width: 400px;
  height: auto;
}
.mapContainer{
    width: 600px;
    height: 300;
    overflow: scroll;
    border: #000207;
}
#map{
    width: 1920px;
    height: 1078px;
    background: url('/img/polacks.jpg');
    background-size: cover;
    position: relative;
}
#target{
    position: absolute;
    background-color: #000000;
    color: #ffffff;
    width: 20px;
    height: 20px;
    font-weight: bold
}
</style>