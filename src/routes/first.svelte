<script>
	export let data;
	export let number;
	import { onMount } from 'svelte';
	import * as d3 from 'd3';

	let chart;
	let num;
	let color = '#5D5FEF';
	let width = 2000;
	let height = 700;
	const marginTop = 20;
	const marginRight = 20;
	const marginBottom = 30;
	const marginLeft = 70;
	const barSpacing = 10;
	let diff = 5;

	onMount(() => {
		num = number;
		height = 700;
		width = 2000;
		diff = 5;
		color = '#5D5FEF';
		const maxRate = Math.round(d3.max(data, (d) => d.rate));

		const x = d3
			.scaleBand()
			.domain(data.map((d) => d.region))
			.range([marginLeft, width - marginRight])
			.paddingInner(0.1)
			.paddingOuter(0.1)
			.padding(barSpacing / (width - marginLeft - marginRight));

		const y = d3
			.scaleLinear()
			.domain([0, maxRate])
			.range([height - marginBottom, marginTop]);

		const svg = d3
			.create('svg')
			.attr('width', width)
			.attr('height', height)
			.attr('viewBox', [0, 0, width, height])
			.attr('style', 'max-width: 100%; height: auto;');

		svg
			.append('g')
			.selectAll('line')
			.data(y.ticks())
			.join('line')
			.attr('x1', marginLeft)
			.attr('x2', width - marginRight)
			.attr('y1', (d) => y(d))
			.attr('y2', (d) => y(d))
			.attr('stroke', 'gray')
			.attr('stroke-width', 1)
			.attr('stroke-dasharray', '2');

		svg
			.append('g')
			.selectAll('line')
			.data(x.domain())
			.join('line')
			.attr('x1', (d) => x(d) + x.bandwidth() / 2)
			.attr('x2', (d) => x(d) + x.bandwidth() / 2)
			.attr('y1', marginTop)
			.attr('y2', height - marginBottom)
			.attr('stroke', 'gray')
			.attr('stroke-width', 1)
			.attr('stroke-dasharray', '2');

		svg
			.append('g')
			.selectAll('rect')
			.data(data)
			.join('rect')
			.attr('x', (d) => x(d.region) + diff / 2)
			.attr('y', (d) => height - marginBottom)
			.attr('height', 0) // Initial height set to 0
			.attr('width', x.bandwidth() - diff)
			.attr('fill', color)
			.call((enter) =>
				enter
					.transition() // Start of the transition
					.duration(600) // Duration of the animation
					.delay((d, i) => 50 * i)
					.attr('y', (d) => y(d.rate))
					.attr('height', (d) => height - marginBottom - y(d.rate))
			),
			svg
				.append('g')
				.attr('transform', `translate(0,${height - marginBottom})`)
				.call(d3.axisBottom(x));

		svg.append('g').attr('transform', `translate(${marginLeft},0)`).call(d3.axisLeft(y));

		chart = svg.node();

		let solve = document.querySelector('.goat1');
		solve.appendChild(chart);
	});
</script>

<div class="goat1" bind:this={chart}></div>

<style>
</style>
