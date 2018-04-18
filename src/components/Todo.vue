<template>
  <div class="todo">
    <h1 class="text-center">Weight Loss Records</h1>








<div class="container">
  
  <div class="box">
  
  <h1>Weight Loss Percentage Calculator</h1>
  
  <div class="row">
    <div class=" col-xs-offset-2 col-xs-8 col-sm-offset-2 col-sm-4">
      <label for="starting">Starting Weight</label>
      <div class="input-group">
      <input class="form-control" id="starting" type="num" v-model="start" /><span class="input-group-addon">lbs</span>
      </div>
    </div>
    
     <div class=" col-xs-offset-2 col-xs-8 col-sm-offset-0 col-sm-4">
      <label for="current">Current Weight</label>
      <div class="input-group">
      <input class="form-control"id="current" v-model="current" type="num" /><span class="input-group-addon">lbs</span>
        
      </div>
    </div>
  </div>
  
    <div class="row">
      <div class="col-xs-offset-3 col-xs-6">
      
          <p>Total percent body weight lost: <span id="result" >{{result}}</span></p>
       
      </div>
    </div>
  
  
    <div>
        <button id ="calc" v-on:click="computeLoss()" class="btn btn-lg">Calculate</button>
        <button  id="reset" class="btn btn-lg">Reset</button>
    </div>
    

        
    
  
 
</div><!-- class="box" -->
</div>









<div class="centerMe">
    <form v-on:submit.prevent="addItem">
      <input class= "recordInput" type="text" v-model="text">
      <button class="recordButton" type="submit">Record Weight Loss</button>
    </form>
    <div class="controls">
      <button v-on:click="showAll()">Show All</button>
      <button v-on:click="showActive()">Show Unselected</button>
      <button v-on:click="showCompleted()">Show Selected</button>
      <button v-on:click="deleteCompleted()">Delete Selected</button>
    </div>
    <ul>
      <li v-for="item in filteredItems" draggable="true" v-on:dragstart="dragItem(item)" v-on:dragover.prevent v-on:drop="dropItem(item)">
	<input type="checkbox" v-model="item.completed" v-on:click="completeItem(item)" /><label v-bind:class="{ completed: item.completed }">{{ item.text }}</label><button v-on:click="deleteItem(item)" class="delete">X</button>
      </li>
    </ul>
  </div>
  </div>
</template>

<script>
 export default {
   name: 'Todo',
   data () {
     return {
       text: '',
       show: 'all',
       drag: {},
       start: '',
       current: '',
       result: ''
     }
   },
   computed: {
   items: function() {
       return this.$store.getters.items;
    },
     activeItems: function() {
       return this.items.filter(function(item) {
	 return !item.completed;
       });
     },
     filteredItems: function() {
       if (this.show === 'active')
	 return this.items.filter(function(item) {
	   return !item.completed;
	 });
       if (this.show === 'completed')
	 return this.items.filter(function(item) {
	   return item.completed;
	 });
       return this.items;
     },
   },
   created: function() {
     this.getItems();
   },
   methods: {
   computeLoss: function(){
  var startWeight = this.start;
  var currentWeight = this.current;
  console.log(startWeight, currentWeight);
  
  var displayResult = ((startWeight - currentWeight)/startWeight) * 100;
  
  this.result = Math.round(100*displayResult)/100 + "%";
  
},
     getItems: function() {
     this.$store.dispatch('getItems');
     },
     addItem: function() {
this.$store.dispatch('addItem',{
         text: this.text,
         completed: false
     });
     },
     completeItem: function(item) {
this.$store.dispatch('updateItem',{
id: item.id,
	 text: item.text,
	 completed: !item.completed,
	 orderChange: false,
       });
     },
     deleteItem: function(item) {
       this.$store.dispatch('deleteItem', {id: item.id
       });
     },
     showAll: function() {
       this.show = 'all';
     },
     showActive: function() {
       this.show = 'active';
     },
     showCompleted: function() {
       this.show = 'completed';
     },
     deleteCompleted: function() {
       this.items.forEach(item => {
	 if (item.completed)
	   this.deleteItem(item)
       });
     },
     dragItem: function(item) {
       this.drag = item;
     },
     dropItem: function(item) {
 this.$store.dispatch('updateItem',{
 id: this.drag.id,
	 text: this.drag.text,
	 completed: this.drag.completed,
	 orderChange: true,
	 orderTarget: item.id
       
       });
     },
   }
 }
</script>

<style scoped>


h1 {
  color:seagreen;
  font-weight: 300;
}
label {
  font-size: 1.5em;
  font-weight:300;
}
p {
  margin-top: 20px;
}

span {
  font-size: 1.5em;
  font-weight: 500;
  color: seagreen;
}
.box {
  text-align:center;
  border: 2px solid seagreen;
  border-radius: 3px;
  margin-top: 50px;
  padding:20px;
  background: #f1f1f1;
}
#calc {
  margin-top:30px;
  background-color:seagreen;
  color:white;
  outline:none;
  
}
#reset {
  position:relative;
  top:15px;
  width:110px;
  background-color: #d1d1d1;
  outline:none;
}

.recordButton {
  padding: 10px;
  border-radius: 5px;
  font-weight: bold;
  color: white;
  background: seagreen;
}

.recordInput {
  border-radius: 5px;
  padding: 5px;
  outline: none;
  border: 1px solid #ccc;
}



 ul {
     list-style: none;
 }

 li {
     background: #f3f3f3;
     width: 500px;
     min-height: 30px;
     padding: 10px;
     margin-bottom: 10px;
     font-size: 1em;
     display: flex;
     align-items: center;
 }

 .delete {
     display: none;
     margin-left: auto;
 }

 li:hover .delete {
     display: block;
 }

 label {
     width: 400px;
 }

 .completed {
     text-decoration: line-through;
 }

 /* Form */


 input[type=checkbox] {
     transform: scale(1.5);
     margin-right: 10px;
 }

 input[type=text] {
     font-size: 1em;
 }

 .controls button {
    font-size: 1em;
    padding: 5px;
    border-radius: 3px;
    font-weight: bold;
    outline: none;

 }
 .controls button:hover {
  background-color: seagreen;
  color: white;
 }
 .centerMe {
display: block;
margin-left: 30%;
margin-top: 40px;
 }
 .controls {
     display: block;
     margin: 20px auto;
 }
</style>