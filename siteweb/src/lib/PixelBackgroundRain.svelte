<script lang="ts">
	import { onMount } from "svelte";

	let canvas: HTMLCanvasElement;
	let ctx: CanvasRenderingContext2D;

	type BlockType = "stone" | "dirt" | "grass" | "diamond";

	type Pixel = {
		x: number;
		y: number;
		size: number;
		speed: number;
		type: BlockType;
	};

	const PIXEL_COUNT = 120;
	let pixels: Pixel[] = [];

	function randomType(): BlockType {
		const r = Math.random();
		if (r < 0.7) return "stone";
		if (r < 0.9) return "dirt";
		if (r < 0.98) return "grass";
		return "diamond"; // rare 🔥
	}

	function createPixel(): Pixel {
		return {
			x: Math.random() * canvas.width,
			y: Math.random() * canvas.height,
			size: Math.random() * 12 + 6,
			speed: Math.random() * 2 + 0.5,
			type: randomType()
		};
	}

	function init() {
		pixels = Array.from({ length: PIXEL_COUNT }, createPixel);
	}

	function drawPixel(p: Pixel) {
		switch (p.type) {
			case "stone":
				ctx.fillStyle = "#7a7a7a";
				break;
			case "dirt":
				ctx.fillStyle = "#8b5a2b";
				break;
			case "grass":
				ctx.fillStyle = "#5dbb63";
				break;
			case "diamond":
				ctx.fillStyle = "#4de0ff";

				// glow effect 💎
				ctx.shadowColor = "#4de0ff";
				ctx.shadowBlur = 10;
				break;
		}

		ctx.fillRect(p.x, p.y, p.size, p.size);

		// reset shadow so others aren't glowing
		ctx.shadowBlur = 0;
	}

	function animate() {
		// trail effect (smooth motion)
		ctx.fillStyle = "rgba(0,0,0,0.2)";
		ctx.fillRect(0, 0, canvas.width, canvas.height);

		pixels.forEach((p) => {
			p.y += p.speed;
			p.x += Math.sin(p.y * 0.01) * 0.3; // slight drift

			if (p.y > canvas.height) {
				Object.assign(p, createPixel(), { y: -10 });
			}

			drawPixel(p);
		});

		requestAnimationFrame(animate);
	}

	function resize() {
		const dpr = window.devicePixelRatio || 1;
		canvas.width = window.innerWidth * dpr;
		canvas.height = window.innerHeight * dpr;
		ctx.scale(dpr, dpr);
	}

	onMount(() => {
		const context = canvas.getContext("2d");
		if (!context) return;
		ctx = context;

		resize();
		init();
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
		background: #0a0a0a;
		image-rendering: pixelated;
	}
</style>