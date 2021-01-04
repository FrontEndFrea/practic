<template>
  <div id="app">
    <div class="rocket-area">
      <img
        class="rocket"
        src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/t-1/rocket.svg"
        alt="Rocket ship"
        :style="moveShip"
      />
    </div>
    
    <div class="controls-area">
      <div>
        <h1>Control Panel</h1>
        
        <label>Up/Down:<input type="number" v-model="moveYAxis"/>km from ground </label>
        <input type="range" min="0" step="1" max="660" v-model='moveYAxis'/>
        
        <label>Left/Right: <input type="number" v-model="moveXAxis"/>km from centre</label>
        <input type="range" min="-350" step="1" max="350" v-model='moveXAxis'/>
        
        <label>Size: <input type="number" v-model="height"/>m</label>
        <input type="range" min="50" step="1" max="150" v-model='height'/>
        
        <label>Rotation: <input type="number" v-model="degreeTurned"/>deg</label>
        <input type="range" min="0" step="1" max="360" v-model='degreeTurned'/>
        
        
        
        <button v-if="unlaunched" @click="launchShip(); startCountdown();">launch</button>
        <button v-else @click='resetShip'>reset</button>

        <button v-if="unrolled" @click='rollShip'>Do a barrel roll</button>
        <button v-else @click='resetShip'>reset</button>
        </br>
        
        <button @click="backToStart">Back to the start</button>
        </br>
        </br>
        
        <p class="countdown" v-if="!unlaunched">Launch in: {{ countDown }}</p> 
      </div>
    </div>
    
  </div>
</template>


<script>
export default {
  data() {
    return {
      height: 100,
      moveXAxis: 0,
      moveYAxis: 0,
      degreeTurned: 0,
      shipAnimation: "",
      animationTime: "",
      launchDelay: "",
      countDown: "3",
      unlaunched: true,
      unrolled: true
    };
  },
  methods: {   
    launchShip() {
      this.shipAnimation = "blastOff";
      this.animationTime = "4s";
      this.launchDelay = "3s";
      this.unlaunched = false;
      console.log("countdown start = " + this.countDown);  
    },
    startCountdown() {
      if(this.countDown > 0) {
        console.log("here " + this.countDown);
        setTimeout(() => {
          this.countDown -= 1
          this.startCountdown()
        }, 1000)}
    },
    rollShip() {
      this.shipAnimation = "barrelroll";
      this.animationTime = "1s";
      this.unrolled = false;
    },
    resetShip() {
      this.shipAnimation = "";
      this.animationTime = "";
      if(this.unlaunched === false) {
        this.unlaunched = true;
        this.countDown = 3;
        this.launchDelay = "";
      }
      if(this.unrolled === false) {
        this.unrolled = true;
      }      
    },
    backToStart() {
      this.height = 100;
      this.moveXAxis = 0;
      this.moveYAxis = 0;
      this.degreeTurned = 0;
      this.countDown = 3;
      this.animationTime = "";
      this.shipAnimation = "";
      this.launchDelay = "";
      this.unrolled = true;
      this.unlaunched = true;
    }
  },
  computed: {
    moveShip() {
      return {
        height: this.height + "px",
        marginLeft: this.moveXAxis + "px",
        marginBottom: this.moveYAxis + "px",
        transform: "rotate(" + this.degreeTurned + "deg)",
        animationName: this.shipAnimation,
        animationDuration: this.animationTime,
        animationDelay: this.launchDelay
      }      
    }
  }
};
</script>



<style lang="scss">
@import url("https://fonts.googleapis.com/css?family=Roboto:400,400i,700");
* {
  box-sizing: border-box;
}
body {
  margin: 0;
  padding: 2rem;
  height: 100vh;
  font-family: Roboto, sans-serif;
}
  
  .countdown {
    color: red;
    font-size: 3rem;
  }
  
@keyframes blastOff {
  0%   {height: 100px; margin-left: 0px; margin-bottom: 0px;}
  25%  {height: 110px; margin-left: 80px; margin-bottom: 100px;}
  50%  {height: 120px; margin-left: -80px; margin-bottom: 200px;}
  100% {height: 130px; margin-left: 0px; margin-bottom: 600px;}
}
  
@keyframes barrelroll {
  from   {transform: rotate(0deg);}
  to {transform: rotate(359deg);}
}
  
.rocket {
  justify-self: center;
  align-self: end;
  height: 100px;
  margin-bottom: 0px;
}
  
#app {
  display: grid;
  height: 100%;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;
}
.rocket-area {
  background: #222;
  background-image: radial-gradient(
      2px 2px at 20px 30px,
      #eee,
      rgba(0, 0, 0, 0)
    ),
    radial-gradient(2px 2px at 40px 70px, #fff, rgba(0, 0, 0, 0)),
    radial-gradient(2px 2px at 50px 160px, #ddd, rgba(0, 0, 0, 0)),
    radial-gradient(2px 2px at 90px 40px, #fff, rgba(0, 0, 0, 0)),
    radial-gradient(2px 2px at 130px 80px, #fff, rgba(0, 0, 0, 0)),
    radial-gradient(2px 2px at 160px 120px, #ddd, rgba(0, 0, 0, 0));
  background-repeat: repeat;
  background-size: 200px 200px;
  border: 5px solid #ddd;
  box-shadow: inset 0 0 12px black;
  border-radius: 16px;
  display: grid;
}

.controls-area {
  background: #ffe082
    url(https://s3-us-west-2.amazonaws.com/s.cdpn.io/t-1/brushed-alum.png);
  border-radius: 8px;
  border: 5px solid #ffd042;
  box-shadow: inset 0 0 3px rgba(black, 0.1);
  padding: 1rem;
  display: grid;
  place-items: center;
  text-align: center;
  h1 {
    text-transform: uppercase;
    font-size: 1.2rem;
  }
  input {
    width: 100%;
  }
}
</style>
