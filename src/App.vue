<script setup>
import pathToBlock from './assets/block.svg'
import pathToCardActions from './assets/cardActions.svg'
import { onMounted } from 'vue';


onMounted(() => {
	const canvas = document.getElementById("canvas");
			const ctx = canvas.getContext("2d");
			const undoBtn = document.querySelector(".undo");
			const redoBtn = document.querySelector(".redo");
			const zoomIn = document.querySelector(".zoom-in");
			const zoomOut = document.querySelector('.zoom-out')
			const addBtn = document.querySelector('.plus')
			// zoomIn.addEventListener('click', () => {
			// 	ctx.scale(1.2, 1.2)
			// 	const keys = ['width', 'height']
			// 	for (const item of canvasItems) {
			// 		if (item.type === 'card') {
			// 			for (const key of keys) {
			// 				if (item[key]) {
			// 					item[key] += item[key] * .5
			// 				}
			// 			}
			// 		}
			// 	}
			// 	// initCardCircles()
			// 	zoomValue = 1.2
			// 	console.log('canvasItems', canvasItems)
			// 	draw()
			// })
			// zoomOut.addEventListener('click', () => {
			// 	ctx.scale(0.8, 0.8)
			// 	// const keys = ['width', 'height']
			// 	// for (const item of canvasItems.filter(item => item.type === 'card')) {
			// 	// 	for (const key of keys) {
			// 	// 		if (item[key]) {
			// 	// 			item[key] = item[key] * .8
			// 	// 		}
			// 	// 	}
			// 	// }
			// 	// // initCardCircles()
			// 	zoomValue = .8
			// 	// draw()
			// })
			// zoomOut.addEventListener('click', () => {
			// 	ctx.scale()
			// })
			// let requestAnimationFrameID = null;
			// const ratio = window.devicePixelRatio || 2;
			// canvas.width = 1000 * ratio;
			// canvas.height = 1000 * ratio;
			// ctx.scale(1000 * ratio, 1000 * ratio);
			const blockImage = new Image();
            const cardActions = new Image()
            let cardActionsLoaded = false
			blockImage.src = pathToBlock;
            cardActions.src = pathToCardActions
            cardActions.onload = () => {
                cardActionsLoaded = true
            }
			function initCardCircles (card) {
				if (card) {
					canvasItems.push({
						cardId: card.id,
						type: "cardCircle",
						circleType: "entry",
						zIndex: 2,
						cardId: card.id,
						x: card.x,
						y: card.y + 45,
						xRadius: 10,
						yRadius: 10,
						width: 20,
						height: 20,
					});
					canvasItems.push({
						cardId: card.id,
						type: "cardCircle",
						circleType: "out",
						zIndex: 2,
						cardId: card.id,
						x: card.x + card.width,
						y: card.y + card.height * 0.85,
						xRadius: 10,
						yRadius: 10,
						width: 20,
						height: 20,
					});
				} else {
					const cards = canvasItems.filter((item) => item.type === "card");
				for (const card of cards) {
					// cardCircleEntry.x = dragObj.x - 20;
					// 	cardCircleEntry.y = dragObj.y + 30;

					// cardCircleOut.x = dragObj.x + dragObj.width;
					// 	cardCircleOut.y = dragObj.y + 100;
					canvasItems.push({
						cardId: card.id,
						type: "cardCircle",
						circleType: "entry",
						zIndex: 2,
						cardId: card.id,
						x: card.x,
						y: card.y + 45,
						xRadius: 10,
						yRadius: 10,
						width: 20,
						height: 20,
					});
					canvasItems.push({
						cardId: card.id,
						type: "cardCircle",
						circleType: "out",
						zIndex: 2,
						cardId: card.id,
						x: card.x + card.width,
						y: card.y + card.height * 0.85,
						xRadius: 10,
						yRadius: 10,
						width: 20,
						height: 20,
					});
				}
				}
				draw();
			}
			blockImage.onload = () => {
				initCardCircles()
			};
			const cardCircles = [];
			let canvasItems = [
				{
					type: "card",
					id: 1,
					x: 400,
					y: 200,
					width: 230,
					height: 178,
					isDraggable: true,
					zIndex: 1,
				},
				{
					type: "card",
					id: 2,
					x: 50,
					y: 50,
					width: 230,
					height: 178,
					isDraggable: true,
					zIndex: 1,
				}
			];
			function undo() {
				for (let i = canvasItems.length - 1; i >= 0; i--) {
					if (canvasItems[i].added) {
						undoItems = [...undoItems, ...canvasItems.splice(i, 1)];
						break;
					}
				}
				draw();
			}
			let undoItems = [];
			function redo() {
				if (undoItems.length) {
					canvasItems.push(undoItems[undoItems.length - 1]);
					undoItems.pop();
					draw();
				}
			}
			undoBtn.addEventListener("click", undo);
			undoBtn.addEventListener("mouseover", () => {
				document.querySelector(".undo-tooltip").style.opacity = 1;
			});
			undoBtn.addEventListener("mouseout", () => {
				document.querySelector(".undo-tooltip").style.opacity = 0;
			});
			redoBtn.addEventListener("click", redo);
			let cardLastId = 2
			addBtn.addEventListener('click', () => {
				cardLastId += 1
				const card = {
					type: "card",
					id: cardLastId,
					x: canvasItems.filter(item => item.type === 'card').sort((a, b) => b.x - a.x)[0].x + 300,
					y: 200,
					width: 230,
					height: 178,
					isDraggable: true,
					zIndex: 1,
				}
				canvasItems.push(card)
				initCardCircles(card)
			})
			function draw() {
				ctx.clearRect(0, 0, canvas.width, canvas.height);
				canvasItems
					.sort((a, b) => a.zIndex - b.zIndex)
					.forEach((item) => {
						switch (item.type) {
							case "cardCircle":
								ctx.fillStyle = "gray";
								ctx.globalAlpha = 0.5;
								ctx.beginPath();
								ctx.ellipse(
									item.x,
									item.y,
									item.xRadius,
									item.yRadius,
									0,
									0,
									2 * Math.PI
								);
								ctx.fill();
								ctx.globalAlpha = 1;
								break;
							case "line":
								ctx.strokeStyle = "#828282";
								ctx.beginPath();
								ctx.moveTo(item.initX, item.initY);
								// ctx.lineTo(item.lineX, item.lineY);
								// P3 + (P0 - P3) * 0.33
								// ctx.bezierCurveTo(
								// 	item.initX + (item.lineX - item.initX) * 0.33,
								// 	item.initY + (item.lineY - item.initY) * 0.33,
								// 	item.lineX + (item.initX - item.lineX) * 0.33,
								// 	item.lineY + (item.initY - item.lineY) * 0.33,
								// 	item.lineX,
								// 	item.lineY
								// );
								const distance = Math.sqrt(
									(item.lineX - item.initX) ** 2 +
										(item.lineY - item.initY) ** 2
								);
								ctx.bezierCurveTo(
									item.initX + distance / 2,
									item.initY,
									item.lineX - distance / 2,
									item.lineY,
									item.lineX,
									item.lineY
								);
								ctx.stroke();
							case "card":
								ctx.drawImage(blockImage, item.x, item.y);
							// const entryCircle = new Path2D();
							// const outCircle = new Path2D();
							// ctx.fillStyle = "gray";
							// entryCircle.ellipse(
							// 	item.x,
							// 	item.y + 30,
							// 	7,
							// 	7,
							// 	0,
							// 	0,
							// 	2 * Math.PI
							// );
							// outCircle.ellipse(
							// 	item.x + item.width,
							// 	item.y + item.height - 30,
							// 	7,
							// 	7,
							// 	0,
							// 	0,
							// 	2 * Math.PI
							// );
							// cardCircles.push({
							// 	type: "entry",
							// 	x: item.x,
							// 	y: item.y + 30,
							// 	path: entryCircle,
							// 	cardId: item.id,
							// });
							// cardCircles.push({
							// 	type: "out",
							// 	x: item.x + item.width,
							// 	y: item.y + item.height - 30,
							// 	path: outCircle,
							// 	cardId: item.id,
							// });
							// ctx.fill(entryCircle);
							// ctx.fill(outCircle);
						}
					});
			}
			let isLineDrawing = false;
			let isDragging = false;
			let dragObj = null;
			let hoverObj = null;
			let offset = { x: 0, y: 0 };
			let circleOffset = { x: 0, y: 0 };
			let zoomValue = 1
			function getMousePos(canvas, e) {
				const mousePos = canvas.getBoundingClientRect();
				return {
					x: e.clientX - mousePos.left,
					y: e.clientY - mousePos.top,
				};
			}

			function hover() {
                if (cardActionsLoaded) {
                    ctx.drawImage(cardActions, hoverObj?.x + (hoverObj.width / 2 - 50), hoverObj?.y - 50)
                }
			}
			// hoverObjType = null;
			function onMouseMove(e) {
				const mousePos = getMousePos(canvas, e);
				hoverObj = canvasItems.find((obj) => {
					if (obj.type === "cardCircle") {
						return (
							mousePos.x >= obj.x - 10 &&
							mousePos.x <= obj.x + obj.width - 10 &&
							mousePos.y >= obj.y - 10 &&
							mousePos.y <= obj.y + obj.height - 10
						);
					} else {
						return (
							mousePos.x >= obj.x &&
							mousePos.x <= obj.x + obj.width &&
							mousePos.y >= obj.y &&
							mousePos.y <= obj.y + obj.height
						);
					}
				});
				if (hoverObj && !isDragging && !isLineDrawing && !isScrolling) {
					canvas.style.cursor = "pointer";
					if (hoverObj.type === "card") {
						hover();
					}
					// else if (hoverObj.type === "cardCircle") {
					// 	let start = null;
					// 	const circleAnimation = (timestamp) => {
					// 		if (!start) start = timestamp / 100;
					// 		if (hoverObj) {
					// 			hoverObj.xRadius = start / 2;
					// 			hoverObj.yRadius = start / 2;
					// 			hoverObj.width = start;
					// 			hoverObj.height = start;
					// 		}
					// 		if (start !== 50 && hoverObj) {
					// 			requestAnimationFrame(circleAnimation);
					// 		}
					// 	};
					// 	requestAnimationFrame(circleAnimation);
					// 	hoverObjType = hoverObj.type;
					// }
				} else if (!isScrolling) {
					// This piece of code is update function that keeps screen up-to-date. This code executes every time mouseover event triggers.
					canvas.style.cursor = "initial";
					ctx.fillStyle = "initial";

					// start = null;
					// cancelAnimationFrame(requestAnimationFrameID);
					ctx.clearRect(0, 0, canvas.width, canvas.height);
					draw();
				}
				// ctx.clearRect(0, 0, canvas.width, canvas.height);
				// draw();
				if (isDragging) {
					if (dragObj) {
						canvas.style.cursor = "grab";
					} else {
						canvas.style.cursor = "initial";
					}
					const mousePos = getMousePos(canvas, e);
					dragObj.x = mousePos.x - offset.x;
					dragObj.y = mousePos.y - offset.y;
					const cardCircleEntry = canvasItems.find(
						(item) =>
							item.type === "cardCircle" &&
							item.circleType === "entry" &&
							item.cardId === dragObj.id
					);
					// Calculate card's entry/out circles
					const cardCircleOut = canvasItems.find(
						(item) =>
							item.type === "cardCircle" &&
							item.circleType === "out" &&
							item.cardId === dragObj.id
					);
					if (cardCircleEntry) {
						cardCircleEntry.x = dragObj.x;
						cardCircleEntry.y = dragObj.y + 45;
					}
					if (cardCircleOut) {
						cardCircleOut.x = dragObj.x + dragObj.width;
						cardCircleOut.y = dragObj.y + dragObj.height * 0.85;
					}
					// Calculate card's lines
					const lines = canvasItems.filter(
						(item) =>
							item.type === "line" &&
							(item.entryCircleId === dragObj.id ||
								item.outCircleId === dragObj.id)
					);
					for (const line of lines) {
						if (line.entryCircleId === dragObj.id) {
							const outCircle = canvasItems.find(
								(item) =>
									item.circleType === "out" && item.cardId === line.outCircleId
							);
							line.lineX = cardCircleEntry.x;
							line.lineY = cardCircleEntry.y;

							line.initX = outCircle.x;
							line.initY = outCircle.y;
						} else if (line.outCircleId === dragObj.id) {
							const entryCircle = canvasItems.find(
								(item) =>
									item.circleType === "entry" &&
									item.cardId === line.entryCircleId
							);
							line.initX = cardCircleOut.x;
							line.initY = cardCircleOut.y;

							line.lineX = entryCircle.x;
							line.lineY = entryCircle.y;
						}
					}
				}
				if (isLineDrawing) {
					ctx.strokeStyle = "#828282";
					ctx.beginPath();
					ctx.moveTo(circleOffset.x, circleOffset.y);
					const distance = Math.sqrt(
						(mousePos.x - circleOffset.x) ** 2 +
							(mousePos.y - circleOffset.y) ** 2
					);
					ctx.bezierCurveTo(
						circleOffset.x + distance / 2,
						circleOffset.y,
						mousePos.x - distance / 2,
						mousePos.y,
						mousePos.x,
						mousePos.y
					);
					ctx.stroke();
				}
				if (isScrolling) {
					const mousePos = getMousePos(canvas, e)
					window.scrollBy(-(mousePos.x - offset.x), -(mousePos.y - offset.y))
				}
			}

			function onMouseUp(e) {
				isDragging = false;
				// if (isScrolling) {
				// 	const mousePos = getMousePos(canvas, e)
				// 	window.scrollTo(-(mousePos.x - offset.x), -(mousePos.y - offset.y))
				// 	offset.x = -(mousePos.x - offset.x)
				// 	offset.y = -(mousePos.y - offset.y)
				// 	isScrolling = false
				// }
				isScrolling = false
				if (isLineDrawing) {
					const mousePos = getMousePos(canvas, e);
					const selectedCircle = canvasItems.find((obj) => {
						if (
							obj.type === "cardCircle" &&
							obj.circleType === "entry" &&
							obj.cardId !== dragObj.cardId
						) {
							return (
								mousePos.x >= obj.x - 10 &&
								mousePos.x <= obj.x + obj.width - 10 &&
								mousePos.y >= obj.y - 10 &&
								mousePos.y <= obj.y + obj.height - 10
							);
						}
					});
					if (selectedCircle) {
						canvasItems.push({
							type: "line",
							added: true,
							entryCircleId: selectedCircle.cardId,
							outCircleId: dragObj.cardId,
							initX: dragObj.x,
							initY: dragObj.y,
							lineX: selectedCircle.x,
							lineY: selectedCircle.y,
						});
					}
					// dragObj = canvasItems.find((obj) => {
					// 	return (
					// 		obj.type === "lineEntry" &&
					// 		mousePos.x >= obj.x &&
					// 		mousePos.x <= obj.x + obj.width &&
					// 		mousePos.y >= obj.y &&
					// 		mousePos.y <= obj.y + obj.height
					// 	);
					// });
					// if (dragObj) {
					// 	canvasItems.push({
					// 		type: "line",
					// 		added: true,
					// 		initX: offset.x,
					// 		initY: offset.y,
					// 		lineX: mousePos.x,
					// 		lineY: mousePos.y,
					// 	});
					// }
					// isLineDrawing = false;
				}
				isLineDrawing = false;
				canvas.style.cursor = "initial";
				dragObj = null;
				draw();
			}
			let isScrolling = false
			function onMouseDown(e) {
				const mousePos = getMousePos(canvas, e);
				dragObj = canvasItems
					.sort((a, b) => b.zIndex - a.zIndex)
					.find((obj) => {
						if (obj.type === "cardCircle") {
							return (
								mousePos.x >= obj.x - 10 &&
								mousePos.x <= obj.x + obj.width - 10 &&
								mousePos.y >= obj.y - 10 &&
								mousePos.y <= obj.y + obj.height - 10
							);
						} else {
							return (
								mousePos.x >= obj.x &&
								mousePos.x <= obj.x + obj.width &&
								mousePos.y >= obj.y &&
								mousePos.y <= obj.y + obj.height
							);
						}
					});
				if (dragObj) {
					if (dragObj.type === "card") {
						canvas.style.cursor = "grab";
						isDragging = true;
						offset.x = mousePos.x - dragObj.x;
						offset.y = mousePos.y - dragObj.y;
						const cardEntryCircle = canvasItems.find(
							(item) =>
								item.type === "cardCircle" &&
								item.circleType === "entry" &&
								item.cardId === dragObj.id
						);
						if (cardEntryCircle) {
							circleOffset.x = cardEntryCircle.x;
							circleOffset.y = cardEntryCircle.y;
						}
					} else if (
						dragObj.type === "cardCircle" &&
						dragObj.circleType === "out" &&
						!canvasItems.filter(
							(item) =>
								item.type === "line" && item.outCircleId === dragObj.cardId
						).length
					) {
						isLineDrawing = true;
						circleOffset.x = mousePos.x;
						circleOffset.y = mousePos.y;
					}
				} else {
					const mousePos = getMousePos(canvas, e)
					canvas.style.cursor = "grab";
					offset.x = mousePos.x
					offset.y = mousePos.y
					isScrolling = true
				}
				// for (const pathWrapper of cardCircles) {
				// 	if (
				// 		pathWrapper.type === "out" &&
				// 		ctx.isPointInPath(pathWrapper.path, mousePos.x, mousePos.y)
				// 	) {
				// 		circleOffset.x = pathWrapper.x;
				// 		circleOffset.y = pathWrapper.y;
				// 		isLineDrawing = true;
				// 		isDragging = false;
				// 	}
				// }
			}

			canvas.addEventListener("mousemove", onMouseMove);
			canvas.addEventListener("mousedown", onMouseDown);
			canvas.addEventListener("mouseup", onMouseUp);
			document.addEventListener("keydown", (e) => {
				if (e.ctrlKey && e.code === "KeyZ") {
					undo();
				}
			});
})



</script>

<template>
  <div>
    <canvas id="canvas" width="10000" height="10000"></canvas>
	<div class="control-panel">
			<div class="control-panel__item plus">
				<svg
					width="24"
					height="24"
					viewBox="0 0 24 24"
					fill="none"
					xmlns="http://www.w3.org/2000/svg"
				>
					<path
						d="M20.6667 10H14V3.33334C14 2.8029 13.7893 2.2942 13.4142 1.91912C13.0392 1.54405 12.5304 1.33334 12 1.33334C11.4696 1.33334 10.9609 1.54405 10.5858 1.91912C10.2107 2.2942 10 2.8029 10 3.33334V10H3.33334C2.80291 10 2.2942 10.2107 1.91913 10.5858C1.54406 10.9609 1.33334 11.4696 1.33334 12C1.33334 12.5304 1.54406 13.0391 1.91913 13.4142C2.2942 13.7893 2.80291 14 3.33334 14H10V20.6667C10 21.1971 10.2107 21.7058 10.5858 22.0809C10.9609 22.456 11.4696 22.6667 12 22.6667C12.5304 22.6667 13.0392 22.456 13.4142 22.0809C13.7893 21.7058 14 21.1971 14 20.6667V14H20.6667C21.1971 14 21.7058 13.7893 22.0809 13.4142C22.456 13.0391 22.6667 12.5304 22.6667 12C22.6667 11.4696 22.456 10.9609 22.0809 10.5858C21.7058 10.2107 21.1971 10 20.6667 10Z"
						fill="white"
					/>
				</svg>
			</div>
			<div class="control-panel__item zoom-in">
				<svg
					width="24"
					height="24"
					viewBox="0 0 24 24"
					fill="none"
					xmlns="http://www.w3.org/2000/svg"
				>
					<path
						d="M15.5 14L20.5 19L19 20.5L14 15.5V14.71L13.73 14.43C12.554 15.4439 11.0527 16.0011 9.5 16C7.77609 16 6.12279 15.3152 4.90381 14.0962C3.68482 12.8772 3 11.2239 3 9.5C3 7.77609 3.68482 6.12279 4.90381 4.90381C6.12279 3.68482 7.77609 3 9.5 3C11.2239 3 12.8772 3.68482 14.0962 4.90381C15.3152 6.12279 16 7.77609 16 9.5C16 11.11 15.41 12.59 14.43 13.73L14.71 14H15.5ZM9.5 14C12 14 14 12 14 9.5C14 7 12 5 9.5 5C7 5 5 7 5 9.5C5 12 7 14 9.5 14ZM12 10H10V12H9V10H7V9H9V7H10V9H12V10Z"
						fill="black"
					/>
				</svg>
			</div>
			<div class="control-panel__item zoom-out">
				<svg
					width="24"
					height="24"
					viewBox="0 0 24 24"
					fill="none"
					xmlns="http://www.w3.org/2000/svg"
				>
					<path
						d="M15.5 14H14.71L14.43 13.73C15.4439 12.554 16.0011 11.0527 16 9.5C16 7.77609 15.3152 6.12279 14.0962 4.90381C12.8772 3.68482 11.2239 3 9.5 3C7.77609 3 6.12279 3.68482 4.90381 4.90381C3.68482 6.12279 3 7.77609 3 9.5C3 11.2239 3.68482 12.8772 4.90381 14.0962C6.12279 15.3152 7.77609 16 9.5 16C11.11 16 12.59 15.41 13.73 14.43L14 14.71V15.5L19 20.5L20.5 19L15.5 14ZM9.5 14C7 14 5 12 5 9.5C5 7 7 5 9.5 5C12 5 14 7 14 9.5C14 12 12 14 9.5 14ZM7 9H12V10H7V9Z"
						fill="black"
					/>
				</svg>
			</div>
			<div class="control-panel__item undo">
				<svg
					width="24"
					height="24"
					viewBox="0 0 24 24"
					fill="none"
					xmlns="http://www.w3.org/2000/svg"
				>
					<path
						d="M7 19V17H14.1C15.15 17 16.0627 16.6667 16.838 16C17.6133 15.3333 18.0007 14.5 18 13.5C18 12.5 17.6123 11.6667 16.837 11C16.0617 10.3333 15.1493 10 14.1 10H7.8L10.4 12.6L9 14L4 9L9 4L10.4 5.4L7.8 8H14.1C15.7167 8 17.1043 8.525 18.263 9.575C19.4217 10.625 20.0007 11.9333 20 13.5C20 15.0667 19.4207 16.375 18.262 17.425C17.1033 18.475 15.716 19 14.1 19H7Z"
						fill="black"
					/>
				</svg>
			</div>
			<div class="control-panel__item redo">
				<svg
					width="24"
					height="24"
					viewBox="0 0 24 24"
					fill="none"
					xmlns="http://www.w3.org/2000/svg"
				>
					<path
						d="M17 19V17H9.9C8.85 17 7.93733 16.6667 7.162 16C6.38667 15.3333 5.99933 14.5 6 13.5C6 12.5 6.38767 11.6667 7.163 11C7.93833 10.3333 8.85067 10 9.9 10H16.2L13.6 12.6L15 14L20 9L15 4L13.6 5.4L16.2 8H9.9C8.28333 8 6.89567 8.525 5.737 9.575C4.57833 10.625 3.99933 11.9333 4 13.5C4 15.0667 4.57933 16.375 5.738 17.425C6.89667 18.475 8.284 19 9.9 19H17Z"
						fill="black"
					/>
				</svg>
			</div>
			<div class="undo-tooltip">CTRL+Z</div>
		</div>
  </div>
</template>

<style scoped>
			.control-panel {
				position: fixed;
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
