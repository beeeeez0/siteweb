<script lang="ts">
	import { onMount } from "svelte";

	let canvas: HTMLCanvasElement;
	let ctx: CanvasRenderingContext2D;

	const NODE_COUNT: number = 40;

	type Vec2 = { x: number; y: number };

	class Node {
		radius: number;
		angle: number;
		speed: number;
		size: number;

		constructor(radius: number, angle: number, speed: number) {
			this.radius = radius;
			this.angle = angle;
			this.speed = speed;
			this.size = Math.random() * 2 + 1;
		}

		update(): void {
			this.angle += this.speed;
		}

		get x(): number {
			return canvas.width / 2 + this.radius * Math.cos(this.angle);
		}

		get y(): number {
			return canvas.height / 2 + this.radius * Math.sin(this.angle);
		}
	}

	let nodes: Node[] = [];
	let mouse: Vec2 = { x: 0, y: 0 };

	function init(): void {
		nodes = [];
		for (let i = 0; i < NODE_COUNT; i++) {
			nodes.push(
				new Node(
					Math.random() * 300 + 50,
					Math.random() * Math.PI * 2,
					(Math.random() - 0.5) * 0.01
				)
			);
		}
	}

	function draw(): void {
		ctx.clearRect(0, 0, canvas.width, canvas.height);

		// Connections between nodes
		for (let i = 0; i < nodes.length; i++) {
			for (let j = i + 1; j < nodes.length; j++) {
				const dx = nodes[i].x - nodes[j].x;
				const dy = nodes[i].y - nodes[j].y;
				const dist = Math.sqrt(dx * dx + dy * dy);

				if (dist < 120) {
					ctx.strokeStyle = `rgba(100, 200, 255, ${1 - dist / 120})`;
					ctx.lineWidth = 1;
					ctx.beginPath();
					ctx.moveTo(nodes[i].x, nodes[i].y);
					ctx.lineTo(nodes[j].x, nodes[j].y);
					ctx.stroke();
				}
			}
		}

		// Mouse interaction
		nodes.forEach((node) => {
			const dx = node.x - mouse.x;
			const dy = node.y - mouse.y;
			const dist = Math.sqrt(dx * dx + dy * dy);

			if (dist < 150) {
				ctx.strokeStyle = "rgba(255,255,255,0.15)";
				ctx.beginPath();
				ctx.moveTo(node.x, node.y);
				ctx.lineTo(mouse.x, mouse.y);
				ctx.stroke();
			}
		});

		// Draw nodes
		nodes.forEach((node) => {
			node.update();

			ctx.fillStyle = "#8fd3ff";
			ctx.beginPath();
			ctx.arc(node.x, node.y, node.size, 0, Math.PI * 2);
			ctx.fill();
		});

		requestAnimationFrame(draw);
	}

	function resize(): void {
		canvas.width = window.innerWidth;
		canvas.height = window.innerHeight;
	}

	function handleMouseMove(e: MouseEvent): void {
		mouse = { x: e.clientX, y: e.clientY };
	}

	onMount(() => {
		const context = canvas.getContext("2d");
		if (!context) return;

		ctx = context;

		resize();
		init();
		draw();

		window.addEventListener("resize", resize);
		window.addEventListener("mousemove", handleMouseMove);

		return () => {
			window.removeEventListener("resize", resize);
			window.removeEventListener("mousemove", handleMouseMove);
		};
	});
</script>

<canvas bind:this={canvas} class="bg"></canvas>

<style>
	.bg {
		position: fixed;
		inset: 0;
		z-index: -1;
		background: radial-gradient(circle at center, #0a0f1f, #000);
	}
</style>