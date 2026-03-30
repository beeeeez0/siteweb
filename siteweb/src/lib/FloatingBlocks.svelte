<script lang="ts">
	import { onMount } from "svelte";

	let canvas: HTMLCanvasElement;
	let ctx: CanvasRenderingContext2D;

	type Block = {
		x: number;
		y: number;
		size: number;
		speed: number;
	};

	let blocks: Block[] = [];

	function createBlocks() {
		blocks = Array.from({ length: 25 }, () => ({
			x: Math.random() * window.innerWidth,
			y: Math.random() * window.innerHeight,
			size: Math.random() * 40 + 20,
			speed: Math.random() * 0.3 + 0.1
		}));
	}

	function drawBlock(b: Block) {
		// Top (grass)
		ctx.fillStyle = "#5dbb63";
		ctx.fillRect(b.x, b.y, b.size, b.size / 2);

		// Side (dirt)
		ctx.fillStyle = "#8b5a2b";
		ctx.fillRect(b.x, b.y + b.size / 2, b.size, b.size / 2);

		// Pixel outline
		ctx.strokeStyle = "rgba(0,0,0,0.3)";
		ctx.strokeRect(b.x, b.y, b.size, b.size);
	}

	function animate() {
		ctx.clearRect(0, 0, canvas.width, canvas.height);

		blocks.forEach((b) => {
			b.y += b.speed;

			if (b.y > canvas.height) {
				b.y = -b.size;
				b.x = Math.random() * canvas.width;
			}

			drawBlock(b);
		});

		requestAnimationFrame(animate);
	}

	function resize() {
		canvas.width = window.innerWidth;
		canvas.height = window.innerHeight;
	}

	onMount(() => {
		const context = canvas.getContext("2d");
		if (!context) return;
		ctx = context;

		resize();
		createBlocks();
		animate();

		window.addEventListener("resize", resize);
	});
</script>

<canvas bind:this={canvas} class="bg"></canvas>

<style>
	.bg {
		position: fixed;
		inset: 0;
		z-index: -1;
		background: linear-gradient(#87ceeb, #1e1e2f);
		image-rendering: pixelated;
	}
</style>