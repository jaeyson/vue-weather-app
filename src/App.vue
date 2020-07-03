<template>
	<div id="app">
		<main class="container mx-auto">
			<!-- start searchbar -->
			<div class="flex items-center border-b border-b-2 border-teal-500 py-2">
				<input
					type="text"
					class="appearance-none bg-transparent border-none w-full text-gray-700 mr-3 py-1 px-2 leading-tight focus:outline-none"
					placeholder="Search..."
					aria-label="Full name"
					v-model="query"
					v-on:keypress="fetchWeather"
				/>
			</div>
			<!-- end searchbar -->
			<!-- start weather output -->
			<div
				class="mx-auto max-w-sm rounded overflow-hidden shadow-lg"
				v-if="typeof weather.main != 'undefined'"
			>
				<div class="px-6 py-4">
					<div class="font-bold text-xl">
						{{ weather.name }}&comma;
						{{ weather.sys.country }}
					</div>
					<p class="text-gray-700 text-base">
						{{ dateBuilder() }}
					</p>
				</div>
				<div class="bg-gray-700 rounded text-white bg-opacity-50 px-6 py-4">
					<div class="font-bold text-6xl">
						<span>
							{{ Math.round(weather.main.temp) + "&deg;c" || "" }}
						</span>
					</div>
					<p class="flex justify-center text-3xl text-base">
						<img
							:src="`${icon + weather.weather[0].icon}.png`"
							alt="weather-icon"
						/>
						{{ weather.weather[0].description }}
					</p>
				</div>
				<div class="px-6 py-4">
					<span
						class="inline-block bg-gray-200 rounded-full px-3 py-1 text-sm font-semibold text-gray-700"
					>
						{{ "#" + weather.name }}
					</span>
					<span
						class="inline-block bg-gray-200 rounded-full px-3 py-1 text-sm font-semibold text-gray-700 mr-2"
					>
						#FeelsLike
						{{ Math.round(weather.main.feels_like) + "&deg;c" || "" }}
					</span>
				</div>
			</div>
			<!-- start weather output -->
			<!-- https://source.unsplash.com/1600x900/?weather,clouds -->
			<div
				class="mx-auto mt-5 max-w-sm rounded overflow-hidden shadow-lg"
				v-else
			>
				<div class="bg-gray-700 text-white bg-opacity-50 px-6 py-4">
					<div class="font-bold text-3xl">
						Weather App
					</div>
				</div>
			</div>
			<!-- end weather output -->

			<!-- sample input for post -->
			<div class="w-full max-w-xs">
				<form
					id="form-app"
					class="bg-white shadow-md rounded px-8 pt-6 pb-8 mb-4">
					<div class="mb-4">
						<label
							class="block text-gray-700 text-sm font-bold mb-2"
							for="email"
						>
							Email
						</label>
						<input
							class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
							id="email"
							type="text"
							placeholder="Username"
							v-model="email"
						/>
					</div>
					<div class="mb-6">
						<label
							class="block text-gray-700 text-sm font-bold mb-2"
							for="password"
						>
							Password
						</label>
						<input
							class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 mb-3 leading-tight focus:outline-none focus:shadow-outline"
							id="password"
							type="password"
							v-model="password"
							placeholder="******************"
						/>
					</div>
					<div class="flex items-center justify-between">
						<button
							class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline"
							type="button"
						>
							Sign In
						</button>
						<a
							class="inline-block align-baseline font-bold text-sm text-blue-500 hover:text-blue-800"
							href="#"
						>
							Forgot Password?
						</a>
					</div>
				</form>
			</div>
		</main>
	</div>
</template>
<script>
export default {
	name: "App",
	data() {
		return {
			// api_key: process.env.VUE_APP_OPEN_WEATHER_APIKEY,
			// url_base: process.env.VUE_APP_OPEN_WEATHER_URLBASE,
			url: "http://localhost:8000/api/login",
			query: "",
			weather: {},
			icon: "http://openweathermap.org/img/wn/",
			status: "init state",
			email: "",
			password: "",
			body: {
				email: "john.smith@email.co",
				password: "test1234",
			},
		};
	},
	methods: {
		localfetch() {
			fetch(this.url, {
				method: "POST",
				body: JSON.stringify(this.body),
				headers: {
					// 'Content-Type': 'application/x-www-form-urlencoded;charset=utf-8',
					"Content-Type": "application/x-www-form-urlencoded",
					"Access-Control-Allow-Origin": "*",
				},
			}).then((response) => response.json());
			// .then(response => console.log(res.token));
		},
		fetchWeather(e) {
			if (e.key == "Enter") {
				fetch(
					`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`
				)
					.then((data) => {
						console.log(data.status);
						this.status = statusBuilder(data.status.toString());
						return data.json();
					})
					.then(this.setResults);
			}
		},
		setResults(results) {
			this.weather = results;
		},
		dateBuilder() {
			const d = new Date();
			const months = [
				"Jan",
				"Feb",
				"Mar",
				"Apr",
				"May",
				"Jun",
				"Jul",
				"Aug",
				"Sep",
				"Oct",
				"Nov",
				"Dec",
			];
			const days = [
				"Sunday",
				"Monday",
				"Tuesday",
				"Wednesday",
				"Thursday",
				"Friday",
				"Saturday",
			];

			const day = days[d.getDay()];
			const date = d.getDate();
			const month = months[d.getMonth()];
			const year = d.getFullYear();

			return `${day}: ${date} ${month} ${year}`;
		},
	},
};
// const dateBuilder = () => {};

const statusBuilder = (status) => {
	if (status == "init state") return "default";

	try {
		showErrorMessage(status);
	} catch (e) {
		return "err...";
	}
};

const showErrorMessage = (error) =>
	({
		"404": "hmm... new planet?",
		"200": "nice!",
	}[error]);
</script>
<style>
#app {
	font-family: Avenir, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
	margin-top: 60px;
}
/*#app {
	background-image: url('./assets/cold-bg.jpg');
	background-size: cover;
	background-position: bottom;
	transition: 0.4s;
}
*/
</style>
