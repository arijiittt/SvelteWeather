<script>
  import { onMount } from "svelte";
  import { writable } from "svelte/store";

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
      return null;
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
    <h3>Weather Application</h3>
    <div class="input-field">
      <input type="text" bind:value={$city} placeholder="Enter city" />
      <button on:click={Searched}>Search</button>
    </div>
  </div>
  <div class="values-display">
    {#if $currentWeather}
      <div class="result currentResult">
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
      <div class="result previousResult">
        <h2>Last Searched:</h2>
        <div>
          <img
            src="src/routes/images/{$previousWeather.weather[0].main}.png"
            alt=""
          />
          <br />
          City: {$previousWeather.name}
          <br />
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
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  .appContainer {
    display: flex;
    flex-direction: column;
    align-items: center;
    background-color: #f0f4f8;
    min-height: 100vh;
    padding: 20px;
  }
  .top {
    background: rgb(102,197,255);
    background: radial-gradient(circle, rgba(102,197,255,1) 0%, rgba(59,233,255,1) 100%);color: white;
    padding: 20px;
    border-radius: 20px 20px 0 0;
    width: 100%;
    max-width: 600px;
    text-align: center;
  }
  .input-field {
    display: flex;
    justify-content: center;
    margin-top: 10px;
  }
  .input-field input,
  button {
    font-size: 16px;
    padding: 10px;
    border: none;
    border-radius: 5px;
    margin-right: 10px;
  }
  .input-field button {
    background-color: #28a745;
    color: white;
    cursor: pointer;
  }
  .input-field button:hover {
    background-color: #218838;
  }
  .values-display {
    background-color: white;
    width: 100%;
    max-width: 600px;
    padding: 20px;
    border-radius: 0 0 20px 20px;
    display: flex;
    justify-content: space-around;
    margin-top: 20px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  }
  .result {
    width: 45%;
    text-align: center;
    padding: 20px;
    background: rgb(243,252,193);
    background: linear-gradient(176deg, rgba(243,252,193,1) 0%, rgba(201,255,174,1) 50%, rgba(145,255,158,1) 100%);
    border-radius: 10px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }
  .result h2 {
    margin-bottom: 10px;
    font-size: 18px;
  }
  img {
    height: 50px;
    width: auto;
    margin-bottom: 10px;
  }
</style>
