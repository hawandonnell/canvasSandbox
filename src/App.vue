<script setup>
import pathToBlock from './assets/block.svg'
import { onMounted, reactive } from 'vue';
const paths = reactive([])
onMounted(() => {
  renderCanvas()
})

function hoverHandler (canvas) {
  canvas.style.cursor = 'pointer'
}

function renderCanvas () {
  const blockImage = new Image()
  blockImage.src = pathToBlock  
  blockImage.onload = () => {
    const canvas = document.getElementById('canvas')
    const context = canvas.getContext('2d')

    // Increase canvas quality
    const scale = window.devicePixelRatio || 2
    canvas.width = Math.floor(500 * scale)
    canvas.height = Math.floor(500 * scale)
    context.scale(scale, scale)
    // context.translate(50, 50)
    const path = new Path2D()
    path.rect(50, 50, 230, 150)
    context.fillStyle = 'red'
    context.drawImage(blockImage, 50, 50)
    context.stroke(path)
    paths.push({ path, handler: hoverHandler })
    canvas.addEventListener('mousemove', (event) => { 
      for (const path of paths) {
        if (context.isPointInPath(path.path, event.offsetX / scale, event.offsetY / scale)) {
          canvas.style.cursor = 'pointer'
          break
        } else {
          canvas.style.cursor = 'initial'
        }
      }
    })
    canvas.addEventListener('click', (event) => { 
      context.fillStyle = 'red'
      context.fillRect(event.offsetX / scale, event.offsetY / scale, 10, 10)
    })
  }
}

</script>

<template>
  <div>
    <canvas id="canvas" width="10000" height="10000"></canvas>
  </div>
</template>

<style scoped>
			.control-panel {
				position: absolute;
				top: 10px;
				left: 10px;
				display: flex;
				flex-flow: column;
				align-items: center;
			}
			.control-panel__item {
				/* width: 30px; */
				padding: 5px;
				/* height: 30px; */
				background-color: white;
				border: 2px solid #ddd;
				border-radius: 50%;
				display: flex;
				align-items: center;
				justify-content: center;
				position: relative;
			}
			.control-panel__item + .control-panel__item {
				margin-top: 15px;
			}
			.plus {
				background-color: rgb(61, 61, 255);
				padding: 20px;
			}
			.control-panel__item:hover {
				cursor: pointer;
			}
			/* .undo:hover .undo-tooltip {
				opacity: 1;
			} */
			.undo-tooltip {
				background-color: #737373;
				color: white;
				font-family: sans-serif;
				padding: 10px;
				font-size: 10px;
				position: absolute;
				opacity: 0;
				transition: opacity 0.5s;
				white-space: nowrap;
				top: 188px;
				right: -70px;
			}
</style>
