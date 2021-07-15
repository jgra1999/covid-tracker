<template>
	<main v-if="!loading">
		<DataTitle :text="title" :dataDate="dataDate" />

		<DataBoxes :stats="stats" />

		<CountrySelect @get-country="getCountryData" :countries="countries" />

		<button
			@click="clearCountryData"
			v-if="stats.Country"
			class="bg-green-700 text-white rounded mt-10 p-3 focus:outline-none hover:bg-green-600"
		>
			Clear Country
		</button>
	</main>

	<main class="flex flex-col align-center justify-center text-center" v-else>
		<div class="text=gray-500 text-3xl mt-10 mb-6 ">
			Fetching Data
		</div>
		<img :src="loadingImage" class="w-24 m-auto" alt="" />
	</main>
</template>

<script>
import DataTitle from '@/components/DataTitle';
import DataBoxes from '@/components/DataBoxes';
import CountrySelect from '@/components/CountrySelect';

export default {
	name: 'Home',
	components: {
		DataTitle,
		DataBoxes,
		CountrySelect,
	},

	data() {
		return {
			loading: true,
			title: 'Global ',
			dataDate: '',
			stats: {},
			countries: [],
			loadingImage: require('../assets/loading.png'),
		};
	},

	methods: {
		async fetchCovidData() {
			const res = await fetch('https://api.covid19api.com/summary');
			const data = await res.json();
			return data;
		},
		getCountryData(country) {
			(this.stats = country), (this.title = country.Country);
		},
		async clearCountryData() {
			this.loading = true;
			const data = await this.fetchCovidData();
			this.title = 'Global';
			this.stats = data.Global;
			this.loading = false;
		},
	},

	async created() {
		const data = await this.fetchCovidData();

		this.dataDate = data.Date;
		this.stats = data.Global;
		this.countries = data.Countries;
		this.loading = false;
	},
};
</script>

<style>
@keyframes rotate {
	from {
		transform: rotate(0deg);
	}
	to {
		transform: rotate(-360deg);
	}
}
@-webkit-keyframes rotate {
	from {
		-webkit-transform: rotate(0deg);
	}
	to {
		-webkit-transform: rotate(-360deg);
	}
}
img {
	-webkit-animation: 3s rotate linear infinite;
	animation: 3s rotate linear infinite;
	-webkit-transform-origin: 50% 50%;
	transform-origin: 50% 50%;
}
</style>
