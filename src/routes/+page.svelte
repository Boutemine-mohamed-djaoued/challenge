<script>
	import StatisticsGraph from './first.svelte';
	import StatisticsGraph1 from './second.svelte';
	import { jsonData } from './json';
	import { state } from '$lib/state.js';

	// intail drop down
	let stores = [];
	for (let i = 1; i <= 45; i++) {
		stores.push(i);
	}
	let selected = 1;

	let allData = [];
	let oneStoreData = [];

	// get data of all stores
	for (let i = 1; i < 36; i++) {
		let amount = 0;
		for (let j = 0; j < jsonData.length; j++) {
			let dateString = jsonData[j].Date;
			// Split the date string by "-"
			let parts = dateString.split('-');
			// Extract year, month, and day
			let year = parseInt(parts[2]);
			let month = parseInt(parts[1]);
			if ((year - 2010) * 12 + month == i) amount += jsonData[j].Weekly_Sales;
		}
		allData.push({ region: `${i}`, rate: amount });
	}
	$: {
		// change when we select a new store
		selected;
		oneStoreData = [];
		for (let i = 1; i < 36; i++) {
			let amount = 0;
			for (let j = 0; j < jsonData.length; j++) {
				let dateString = jsonData[j].Date;
				// Split the date string by "-"
				let parts = dateString.split('-');
				// Extract year, month
				let year = parseInt(parts[2]);
				let month = parseInt(parts[1]);

				if ((year - 2010) * 12 + month == i && jsonData[j].Store == 1) {
					amount += jsonData[j].Weekly_Sales;
				}
			}
			oneStoreData.push({ region: `${i}`, rate: amount });
		}
		state.update((value) => !value);
	}
</script>

<h1>the first question</h1>
<StatisticsGraph changed="false" number={36} data={allData}></StatisticsGraph>
<div>
	<h2>the second question</h2>
	<label for="stores">Choose a store:</label>
	<select bind:value={selected} id="stores" name="stores">
		{#each stores as store}
			<option value={store}>store {store}</option>
		{/each}
	</select>
</div>
<h1>{selected}</h1>
<StatisticsGraph1 number={36} data={oneStoreData}></StatisticsGraph1>
