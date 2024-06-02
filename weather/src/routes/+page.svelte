<script>
  import { onMount } from "svelte";
  import { writable } from "svelte/store";

  // Define writable stores for city and weather data
  let city = writable("");
  let currentWeather = writable(null);
  let previousWeather = writable(null);

  let previousCity = "";
  let currentCity = "";

  const API_URL = import.meta.env.VITE_API_URL;
  const API_KEY = import.meta.env.VITE_API_KEY;

  const fetch_weather = async (cityName) => {
    try {
      const response = await fetch(`${API_URL}?q=${cityName}&appid=${API_KEY}`);
      if (!response.ok) {
        throw new Error("Network response was not ok");
      }
      return await response.json();
    } catch (error) {
      console.error(
        "There has been a problem with your fetch operation:",
        error
      );
      return null; // Return null if there was an error
    }
  };

  const Searched = async () => {
    previousCity = currentCity;
    currentCity = $city;

    const newWeather = await fetch_weather(currentCity);
    const oldWeather = await fetch_weather(previousCity);

    previousWeather.set(oldWeather);
    currentWeather.set(newWeather);
  };

  onMount(() => {
    console.log("Component mounted");
  });
</script>

<div class="appContainer">
  <div class="top">
    <h5>Weather Application</h5>
    <div class="input-field">
      <input type="text" bind:value={$city} placeholder="Enter city" />
      <button on:click={Searched}>Search</button>
    </div>
  </div>
  <div class="values-display">
    {#if $currentWeather}
      <div class="currentResult">
        <h2>Current Search:</h2>
        <div>
          <img
            src="src/routes/images/{$currentWeather.weather[0].main}.png"
            alt=""
          />
          <br />
          City: {$currentWeather.name}

          <br />
          {$currentWeather.weather[0].main}
          <br />
          Temperature: {Math.round($currentWeather.main.temp - 273)}°C
        </div>
      </div>
    {/if}
    {#if $previousWeather}
      <div class="previousResult">
        <h2>last Searched:</h2>
        <div>
          <img
            src="src/routes/images/{$previousWeather.weather[0].main}.png"
            alt=""
          />
          <br />
          <br />
          City: {$previousWeather.name}
          {$previousWeather.weather[0].main}
          <br />
          Temperature: {Math.round($previousWeather.main.temp - 273)}°C
        </div>
      </div>
    {/if}
  </div>
</div>

<style>
  * {
    font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  }
  .appContainer {
    display: flex;
    align-items: center;
    flex-direction: column;
  }
  .top {
    padding: 20px;
    display: flex;
    flex-direction: column;
    row-gap: 20px;
    align-items: center;
    background-color: aqua;
    width: 95vw;
    border-radius: 50px 50px 0px 00px;
  }
  .input-field input,
  button {
    width: 150px, 50px;
    border-radius: 20px;
    font-size: 12px;
    border-style: none;
    padding: 8px 16px;
  }
  .values-display {
    padding: 20px;
    background-color: hotpink;
    width: 95vw;
    display: flex;
    justify-content: space-evenly;
    align-items: center;
    border-radius: 0px 0px 50px 50px;
  }

  .currentResult {
    width: 40%;
    background-color: rgba(0, 0, 0, 0.1);
    padding: 20px;
    border-radius: 20px;
  }
  .previousResult {
    width: 40%;
    background-color: rgba(0, 0, 0, 0.1);
    padding: 20px;
    border-radius: 20px;
  }
  img{
    height: 50px;
    width: auto;
  }
</style>
