<template>
  <div id="table" class="borad">
    <p>难度级别:
      <select v-model="level">
        <option v-for="val in 10" :value="val">{{ val }}</option>
      </select>
      <button @click="init">重置</button>
      <button @click="autoSolve">自动解数独</button>
      <span class="winText" v-show="isWin">你赢了，你真棒！！！！{{timeCount}}</span>
    </p>
    <p class="winText" id="1"></p>
    <ul v-for="( row, rowIndex ) in numData">
      <li v-for="( cell, cellIndex ) in row">
        <input v-model="cell.num"
               :name="rowIndex + '-' + cellIndex"
               :value="cell.num"
               :disabled="!cell.enabled"
               :class= "{ 'error': cell.error }"
               @keyup.prevent="changeH"
               maxlength="1">
      </li>
    </ul>
  </div>
</template>
<style scoped>
  ul { margin: 0; padding: 0; }
	li { list-style: none; }
	button { cursor: pointer; }
	#app { font-size: 12px; font-family: '微软雅黑';display: inline-block;}
	/*请浮动*/
	ul { zoom: 1; }
	ul:after { content: ''; display: block; clear: both;}
	#app h2{ text-align: center; }
	/*格子*/
	#app .borad { border: 1px solid darkgrey; display: inline-block; }
	#app .borad ul:nth-of-type(3), #app .borad ul:nth-of-type(6){border-bottom: 1px solid #000000;}
	#app .borad ul li:nth-of-type(3), #app .borad ul li:nth-of-type(6){border-right: 1px solid #000000;}
	#app .borad li { float: left;}
	input { width: 40px; height: 40px; text-align: center;}
	.error { background-color: red; }
	#app .winText{ color: green; font-size: 16px; }
</style>
<script>
  var time = 0,t;
  export default{
        name: 'Table',
        data() {
          return {
            level: 1,
            numData: createTable(1),
            isWin: false,
            timeCount: 0
          }
        },
        methods: {
          init(){
            this.numData = createTable(this.level);
            this.isWin = false;
            this.timing();
          },
          timing(){
            document.getElementById("1").innerText = this.timeCount.toString();
            this.timeCount = this.timeCount + 1;
            console.log("2222");
            t=setInterval("this.timeCount++",1000);
          },
          changeH(e){
            var row = e.target.name.split('-')[0];
            var col = e.target.name.split('-')[1];
            var cell = this.numData[row][col];
            if(e.keyCode<58 && e.keyCode>48){
              if(!(checkRow(row,col,e.keyCode-48,this.numData)&&checkCol(row,col,e.keyCode-48,this.numData)
                &&checkGroup(row,col,e.keyCode-48,this.numData))){
                cell.error = true;
              }
              if(checkAll(this.numData)){
                this.isWin = true;
                clearTimeout(t);
              }
            }
            else if(e.keyCode == 8){
              cell.error = false;
            }
            else{
              e.returnValue = false;
            }
          },
          autoSolve(){
            var k = 0,row,col;
            var blank = [],index = 0;
            for(let i = 0;i<9;i++){
              for (let j = 0;j<9;j++){
                if(!this.numData[i][j].num) {
                  blank[index++] = {};
                  blank[index-1].row = i;
                  blank[index-1].col = j;
                }
              }
            }
            while (true){
              row = blank[k].row;
              col = blank[k].col;
              this.numData[row][col].num =
                this.numData[row][col].num?this.numData[row][col].num+1:1;
              if(!(checkRow(row,col,this.numData[row][col].num,this.numData)&&checkCol(row,col,this.numData[row][col].num,this.numData)
                &&checkGroup(row,col,this.numData[row][col].num,this.numData))){
                if(this.numData[row][col].num == 9){
                  this.numData[row][col].num = '';
                  k--;
                }
                console.log(k);
              }
              else{
                k++;
                if(k == index){
                  break;
                }
              }
            }
            if(checkAll(this.numData)){
              this.isWin = true;
              clearTimeout(t);
            }
          }
        }
    }
  function createTable(level) {
    var numData = [
      [1, 2, 3, 4, 5, 6, 7, 8, 9],
      [4, 5, 6, 7, 8, 9, 1, 2, 3],
      [7, 8, 9, 1, 2, 3, 4, 5, 6],
      [2, 3, 4, 5, 6, 7, 8, 9, 1],
      [5, 6, 7, 8, 9, 1, 2, 3, 4],
      [8, 9, 1, 2, 3, 4, 5, 6, 7],
      [3, 4, 5, 6, 7, 8, 9, 1, 2],
      [6, 7, 8, 9, 1, 2, 3, 4, 5],
      [9, 1, 2, 3, 4, 5, 6, 7, 8]
    ];
    var aObjData = [];
    for (let i = 0; i < 9; i++) {
      aObjData[i] = [];
      for (let j = 0; j < 9; j++) {
        aObjData[i][j] = {};
        aObjData[i][j].num = numData[i][j];
        aObjData[i][j].enabled = false;
        aObjData[i][j].error = false;
      }
    }
    for(let i = 0;i<20;i++){
      swapRow(Math.floor((Math.random()*8)),Math.floor((Math.random()*8)), aObjData);
    }
    for (let i = 0;i<9;i++){
      swapCol(Math.floor((Math.random()*8)), Math.floor((Math.random()*8)), aObjData);
    }
    for(let i = 0;i<9;i++){
      swapSymmetry(Math.floor((Math.random()*8))%2, aObjData);
    }
    for(let i = 0;i<9;i++){
      for (let j = 0;j<9;j++){
        if((Math.random()*18)<level+3){
          aObjData[i][j].num = '';
          aObjData[i][j].enabled = true;
        }
      }
    }
    return aObjData;
  }

  function swapRow(row1,row2,aObjData) {
    var temp;
    for(let i = 0;i<9;i++) {
      for(let j = 0;j<3;j++) {
        temp = aObjData[row1%3+j*3][i];
        aObjData[row1%3+3*j][i] = aObjData[row2%3+3*j][i];
        aObjData[row2%3+3*j][i] = temp;
      }
    }
  }
  function swapCol(col1, col2, aObjData) {
    var temp;
    for(let i = 0;i<9;i++){
      for (let j = 0;j<3;j++) {
        temp = aObjData[i][col1 % 3 + 3 * j];
        aObjData[i][col1 % 3 + 3 * j] = aObjData[i][col2 % 3 + 3 * j];
        aObjData[i][col2 % 3 + 3 * j] = temp;
      }
    }
  }
  function swapSymmetry(flag, aObjData){
    var temp;
    if(flag){
      for(let i = 0;i<9;i++){
        for(let j = 0;j<i;j++){
          temp = aObjData[i][j];
          aObjData[i][j] = aObjData[j][i];
          aObjData[j][i] = temp;
        }
      }
    }
    else {
      for(let i = 0;i<9;i++){
        for(let j = 0;j<9-i;j++){
          temp = aObjData[i][j];
          aObjData[i][j] = aObjData[9-j-1][9-i-1];
          aObjData[9-j-1][9-i-1] = temp;
        }
      }
    }
  }
  function checkRow(row, col,value, data) {
    var flag = true;
    for(let i = 0;i<9;i++){
      if(data[row][i].num == value && col!=i){
        flag = false;
        break;
      }
    }
    return flag;
  }
  function checkCol(row, col, value, data) {
    var flag = true;
    for (let i = 0;i<9;i++){
      if(data[i][col].num == value && row!=i){
        flag = false;
        break;
      }
    }
    return flag;
  }
  function checkGroup(row, col, value, data) {
    var flag = true;
    var indexi = Math.floor(row/3);
    var indexj = Math.floor(col/3);
    for(let i = indexi*3;i<=indexi*3+2;i++){
      for (let j = indexj*3;j<=indexj*3+2;j++){
        if(data[i][j].num == value && i!=row && j!=col){
          flag = false;
          break;
        }
      }
    }
    return flag;
  }
  function checkAll(data) {
    var flag = true;
    for(let i = 0;i<9;i++){
      for (let j = 0;j<9;j++){
        if(!data[i][j].num || data[i][j].error){
          flag = false;
          break;
        }
      }
    }
    return flag;
  }

//  function allTime(){
//    time = time + 1;
//    t=setTimeout("allTime()",1000);
//    console.log(time);
//  }
</script>
