<template>
  <div id="app">
    <Table></Table>
  </div>
</template>

<script>
import Table from './components/Table'

export default {
  name: 'app',
  components: {
    'Table': Table
  }
}

</script>

<style>
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
	#app .borad ul:nth-of-type(3), #app .borad ul:nth-of-type(6){border-bottom: 1px solid #FF9800;}
	#app .borad ul li:nth-of-type(3), #app .borad ul li:nth-of-type(6){border-right: 1px solid #FF9800;}
	#app .borad li { float: left;}
	input { width: 40px; height: 40px; text-align: center;}
	.error { background-color: red; }
	#app .winText{ color: green; font-size: 16px; }
</style>
