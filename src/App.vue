<template>
<div>
  <div id="main">
    <div id="form">
        <form>
          <label for="gridWidth">width</label>
          <input v-model="gridWidth"  @input="drawGrid" ref="gridWidth" type="number" placeholder="10">
          <label for="gridHeight">height</label>
          <input v-model="gridHeight" @input="drawGrid" ref="gridHeight" id="gridHeight" type="number" placeholder="10">
        </form>
    </div>
    <div id="canv">
        <canvas ref="grid" id="grid" width="960" height="640"></canvas>
        <!-- <canvas ref="mouseLayer" id="mouseLayer" @mousemove="mouseCoords" width="960" height="640"></canvas> -->
        <canvas ref="tileMap" id="tileMap" @mousemove="mouseCoords" @mousedown="placeTile" @mouseup="placeTile" width="960" height="640"></canvas>
        
    </div>
    <div id="textarea">
      <textarea ref='content'></textarea>
    </div>
  </div>

  <div id='aha'>
    <img @click="clickValue=0" src="./assets/0.png">
    <img @click="clickValue=1" src="./assets/1.png">
    <img @click="clickValue=2" src="./assets/2.png">
    <img @click="clickValue=3" src="./assets/3.png">
    <img @click="clickValue=4" src="./assets/4.png">
    <img @click="clickValue=5" src="./assets/5.png">
    <img @click="clickValue=6" src="./assets/6.png">
    <img @click="clickValue=7" src="./assets/7.png">
    <!-- ID = {{this.clickValue}} -->
    mouseX: {{this.mouse.x}} mousey: {{this.mouse.y}}
  </div>
</div>
</template>

<script>

export default {
  name: 'mapeditor',
  data:function() {
    return{
      gridContext:{},
      tileMapContext:{},
      gridWidth:10,
      gridHeight:10,
      gridArray:[],
      mouse:{'x':0,'y':0},
      clickValue:0,
      colors:{0:'#ffffff',
              1:'#e93c3c',
              2:'#e98e3c',
              3:'#e9d23c',
              4:'#87e93c',
              5:'#3ce9df',
              6:'#3c51e9',
              7:'#ce3ce9'}
    }
  },

mounted(){
  this.gridContext = this.$refs.grid.getContext('2d');
  this.tileMapContext = this.$refs.tileMap.getContext('2d');
  this.mouseLayerContext = this.$refs.tileMap.getContext('2d');
  this.drawGrid()
},
methods:{
  placeTile:function(){
    const x=this.mouse.x
    const y=this.mouse.y
    if(x<this.gridWidth&&y<this.gridHeight){
      this.gridArray[y][x] = this.clickValue
      this.gridContext.fillStyle = this.colors[this.clickValue]
      this.gridContext.fillRect(x*32, y*32, 32, 32);
      this.$refs.content.value = JSON.stringify(this.gridArray)
      //ugly
      for(let i=0;i<this.gridHeight;i++){
        for(let j=0;j<this.gridWidth;j++){
          this.gridContext.beginPath();
          this.gridContext.rect(j*32,i*32, 32, 32);
          this.gridContext.stroke();
        }
      }
    }
  },

  drawGrid:function(){
    this.gridArray=[]
    const w = this.gridWidth
    const h = this.gridHeight
    this.gridContext.clearRect(0,0,960,640)
    for(let i=0;i<h;i++){
      this.gridArray.push([])
      for(let j=0;j<w;j++){
        this.gridArray[i].push(0)
        this.gridContext.beginPath();
        this.gridContext.rect(j*32,i*32, 32, 32);
        this.gridContext.stroke();
      }
    }
    this.$refs.content.value = JSON.stringify(this.gridArray)
  },

  mouseCoords:function(e){
    this.tileMapContext.clearRect(0,0,960,640)
    const rect = this.$refs.tileMap.getBoundingClientRect()
    let x = Math.floor((e.clientX-rect.left)/32)
    let y = Math.floor((e.clientY-rect.top)/32)
    this.mouse.x = x
    this.mouse.y = y
    if(x<this.gridWidth&&y<this.gridHeight){
      this.tileMapContext.fillStyle = this.colors[this.clickValue]
      this.tileMapContext.fillRect(x*32,y*32,32,32)
    }
    //console.log(x,y)
  }
}
}

</script>
<style>

#main{
  display: table;
}

#canv{
  width:960px;
  height:640px;
  display: table-cell;
}



#grid{
  image-rendering: pixelated;
  image-rendering: crisp-edges;
  position:absolute;
  z-index: 1;
  border: 1px solid black; 
  pointer-events:none; 
}

/* #mouseLayer{
  image-rendering: pixelated;
  image-rendering: crisp-edges;
  position:absolute;
  z-index: 1;
  border: 1px solid black; 
} */

#tileMap{
  image-rendering: pixelated;
  image-rendering: crisp-edges;
  position:absolute;
  z-index: 0;
  border: 1px solid black; 
}



#form{
  display: table-caption;
  max-width: 200px;
  padding-bottom: 10px;
  
}

#textarea{
  display: table-cell;
  padding-left: 10px;
}
textarea{
  height: 634px;
  width:  280px;
  resize: horizontal;
}

</style>
