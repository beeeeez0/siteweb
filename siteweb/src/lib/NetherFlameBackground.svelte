<script lang="ts">
	import { onMount } from "svelte";

	let canvas: HTMLCanvasElement;
	let ctx: CanvasRenderingContext2D;

	type Particle = {
		x: number;
		y: number;
		vx: number;
		vy: number;
		size: number;
		life: number;
		maxLife: number;
	};

	let particles: Particle[] = [];
	const PARTICLE_COUNT = 120;

	function createParticle(): Particle {
		return {
			x: Math.random() * canvas.width,
			y: canvas.height + Math.random() * 50,
			vx: (Math.random() - 0.5) * 0.5,
			vy: -(Math.random() * 1.5 + 0.5),
			size: Math.random() * 3 + 1,
			life: 0,
			maxLife: Math.random() * 100 + 60
		};
	}

	function init() {
		particles = [];
		for (let i = 0; i < PARTICLE_COUNT; i++) {
			particles.push(createParticle());
		}
	}

	function drawParticle(p: Particle) {
		const lifeRatio = p.life / p.maxLife;

		// Color transitions: red → orange → transparent
		const alpha = 1 - lifeRatio;
		ctx.fillStyle = `rgba(255, ${100 + 100 * (1 - lifeRatio)}, 0, ${alpha})`;

		ctx.beginPath();
		ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
		ctx.fill();
	}

	function animate() {
		// Fade trail effect (heat haze look)
		ctx.fillStyle = "rgba(0, 0, 0, 0.2)";
		ctx.fillRect(0, 0, canvas.width, canvas.height);

		particles.forEach((p, i) => {
			p.x += p.vx;
			p.y += p.vy;
			p.life++;

			// Flicker effect
			p.size *= 0.99 + Math.random() * 0.02;

			drawParticle(p);

			// Reset particle
			if (p.life > p.maxLife || p.y < -10) {
				particles[i] = createParticle();
			}
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

		return () => {
			window.removeEventListener("resize", resize);
		};
	});
</script>

<canvas bind:this={canvas} class="bg"></canvas>

<style>
	.bg {
		position: fixed;
		inset: 0;
		z-index: -1;

		/* Nether-style gradient */
		background: radial-gradient(circle at bottom, #3b0a0a, #000000 70%);
	}
</style>