<template>
	<div id="app">
		<main :class="typeof weather.main != 'undefined' ? '' : ''">

			<!-- START SEARCH -->
			<div class="begin search" v-if="typeof weather.main === 'undefined'">
				<!-- BRAND CENTER -->
				<img src="http://openweathermap.org/img/wn/02d@2x.png">
				<h1>WeatherApp</h1>
				<!-- SEARCH CENTER -->
				<input
				type="text"
				placeholder="Search...."
				v-model="query"
				@keypress="fetchWeather"
				>
				<h3>enter a town / city</h3>
			</div>

			<div class="row" v-if="typeof weather.main != 'undefined'">
			<!-- BRAND LEFT -->
			<div class="brand" @click="clearQuery" v-if="typeof weather.main != 'undefined'">
				<img src="http://openweathermap.org/img/wn/02d@2x.png">
				<h1>WeatherApp</h1>
			</div>

			<!-- SEARCH RIGHT -->
			<div class="search" v-if="typeof weather.main != 'undefined'">
				<input
					type="text"
					placeholder="Search...."
					v-model="query"
					@keypress="fetchWeather"
				>
				<button type="button" name="button" @click="clearQuery">
					x
				</button>
			</div>
			</div>


				<!-- WEATHER INFO CENTER -->
				<div class="weather" v-if="typeof weather.main != 'undefined'">
					<img v-bind:src="weatherIconURL">
					<h1>{{ Math.round(weather.main.temp) }}°</h1>
					<h5>( feels like {{ weather.main.feels_like }}° )</h5>
					<h4>{{ weather.weather[0].description }}</h4>
				</div>

				<!-- WEATHER INFO BOTTOM LEFT -->
				<div class="info" v-if="typeof weather.main != 'undefined'">
					<h2>{{ weather.name }}, {{ weather.sys.country }}</h2>
					<h4>{{ dateBuilder() }}</h4>
				</div>


		</main>
	</div>
</template>

<script>

export default {
	name: 'App',
	data () {
		return {
			api_key: '9fa2ebb4b48251ffb557410a1aed4108',
			url_base: 'https://api.openweathermap.org/data/2.5/',
			query: '',
			weather: {},
			weatherIconURL: '',
		}
	},
	methods: {
		// fetches the Weather info from the API and returns the results in JSON format
		fetchWeather (e) {
			if (e.key == "Enter" && this.query) {
				fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)
				.then(res => {
					return res.json();
				}).then(this.setResults);
			}
		},

		// sets JSON results to local data and triggers a change in background colour
		setResults (results) {
			this.weather = results;
			if (results.weather) {
				this.weatherIconURL = 'http://openweathermap.org/img/wn/' + results.weather[0].icon +'@2x.png'
			}
			this.getTempColour()
		},

		// clears the query, and resets the results with an empty dictionary
		clearQuery () {
			this.query = ''
			let results = {}
			this.setResults(results)
		},


		// clears the current Temp Colour and sets a new Temp Colour based on Weather Temp
		getTempColour () {
			this.$el.querySelector("main").classList.remove("warm", "cold");
			if (this.weather.main && this.weather.main.temp > 20) {
				this.$el.querySelector("main").classList.add("warm");
			} else if (this.weather.main && this.weather.main.temp < 20) {
				this.$el.querySelector("main").classList.add("cold");
			}
		},

		// builds the date into a readable string
		dateBuilder () {
			let d = new Date();
			let months = ["January", "February", "March", "April", "May", "June", "July",
			"August", "September", "October", "November", "December"]
			let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday",
			"Friday", "Saturday"]

			let day = days[d.getDay()];
			let date = d.getDate();
			let month = months[d.getMonth()];
			let year = d.getFullYear();

			return `${day} ${date} ${month} ${year}`;
		}
	}
}
</script>

<style>
* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
}

body {
	font-family: 'Geneva', sans-serif;
	text-align: center;
	color: white;
}

main {
	min-height: 100vh;
	width: 100%;
	padding: 3em;
	padding-bottom: 0;
	background-color: #8bd59e;
}

/* changes background on getTempColour */
main.warm {
	background-color: #daaa7d;
}

main.cold {
	background-color: #7dbeda;
}

.brand {
	cursor: pointer;
}

.brand h1 {
	display: inline;
	opacity: 0.8;
	color: white;
	font-size: 3em;
	vertical-align: top;
	line-height: 2.2;
}

.row {
	display: flex;
	justify-content: space-around;
}

.begin.search{
	margin: auto;
	padding-top: 2em;
	text-align: center;
}

.begin.search h1 {
	margin-top: -1em;
	margin-bottom: 1em;
}

.begin.search h3 {
	margin-top: .5em;
	opacity: 0.7;
}

.search {
	width: 100%;
	max-width: 500px;
	right: 0;
	display: block;
	text-align: right;
	padding-top: 1em;
}

.search input {
	width: 100%;
	padding: 1em;
	appearance: none;
	font-size: 1.25em;
	border: none;
	outline: none;
	background: none;
	appearance: none;
	color: grey;
	background-color: rgba(255, 255, 255, 0.3);
	box-shadow: .25em .25em 0 rgba(0,0,0, 0.3);
	border-radius: 1em 0 1em 0;
	transition: 0.4s;
}

.search input:focus {
	margin: -.25em .25em .25em -.25em;
	background-color: rgba(255, 255, 255, 0.4);
	box-shadow: .5em .5em 0 rgba(0,0,0, 0.4);
}

.search button {
	display: inline;
	margin-left: -3em;
	margin-top: -5px;
	margin-right: 1em;
	padding: .25em;
	padding-left: .5em;
	padding-right: .5em;

	vertical-align: middle;

	background-color: rgba(255,255,255, 0.3);
	color: rgba(0,0,0, 0.6);

	border-radius: 10px;

	border: none;
	outline: none;
	appearance: none;
}

.search button:hover {
	background-color: rgba(255,255,255, 0.6);
}

.search button:active {
	background-color: rgba(0,0,0, 0.2);
}

.weather {
	display: block;
	max-width: 500px;
	margin: auto;
	margin-top: 2em;
	padding: 2em;
	padding-bottom: 1em;
	font-size: 150%;
	border-radius: 0 2.5em 0 2.5em;
	background-color: rgba(255, 255, 255, 0.3);
	box-shadow: .5em .5em 0 rgba(0,0,0, 0.3);
	transition: 0.4s;
}

.weather img {
	width: 150px;
	height: 150px;
}

.weather h1 {
	font-size: 5em;
	display: inline;
	vertical-align: top;
	text-shadow: 3px 3px 0 rgba(0,0,0, 0.3);
}

.weather h4 {
	color: rgba(0,0,0, 0.4);
	font-size: 2em;
	padding-bottom: .5em;
}

.weather h5 {
	font-size: 1.2em;
	font-style: italic;
	padding: .5em;
	margin-top: -1em;
}

.info {
	display: block;
	margin-top: 2em;
	font-size: 200%;
	text-align: left;
	position: absolute;
	bottom: 30px;
	left: 50px;
	padding-right: 50px;
	transition: 0.4s;
	z-index: 0;
}

.info h2 {
	margin: 0;
	padding: 0;
	color: rgba(0,0,0, 0.3);
	font-size: 2em;
}

@media (max-width:1199px) {
	.weather {
		font-size: 130%;
	}
	.info {
		font-size: 180%;
	}
	.weather img {
		width: 100px;
		height: 100px;
	}
}

@media (max-width:990px) {
	main {
		padding-top: 0;
	}
	.search {
		padding: 0;
		margin: auto;
	}
	.weather {
		font-size: 120%;
	}
	.info {
		font-size: 180%;
	}
	.weather img {
		width: 100px;
		height: 100px;
	}
	.row {
		display: block;
	}
}

@media (max-width:768px) {
	.weather {
		font-size: 100%;
	}
	.info {
		font-size: 180%;
	}
	.weather img {
		width: 100px;
		height: 100px;
	}
}

</style>
