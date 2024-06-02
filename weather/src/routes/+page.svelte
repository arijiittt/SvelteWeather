<script>
    import { writable } from 'svelte/store';

    // Define writable stores for weather and city
    let weather = writable(null);
    let city = writable('');

    // Access environment variables
    const API_URL = import.meta.env.VITE_API_URL;
    const API_KEY = import.meta.env.VITE_API_KEY;

    // Fetch weather data for a given city name and update the weather store
    const fetch_weather = async (cityName) => {
        try {
            const response = await fetch(`${API_URL}?q=${cityName}&appid=${API_KEY}`);
            if (!response.ok) {
                throw new Error('Network response was not ok');
            }
            const data = await response.json();
            weather.set(data);
        } catch (error) {
            console.error('There has been a problem with your fetch operation:', error);
            weather.set(null);  // Set to null if there was an error
        }
    };

    // Handle the search action
    const Searched = () => {
        weather.set(null);  // Reset weather data
        fetch_weather($city);  // Fetch weather data for the current city
    };
</script>

<div class="weatherContainer">
    <input type="text" bind:value={$city} placeholder="Enter City Name...">  <!-- Bind the input value to the city store -->
    <button on:click={Searched}>Search</button>
</div>
<div class="weatherResult">
    {#if $weather}  <!-- Only display weather info if it exists -->
        {#if $weather.name && $weather.main} <!-- Check if data is valid -->
            <div>
                City: {$weather.name}
                <br>
                Temperature: {Math.round($weather.main.temp-273)}°C
            </div>
        {:else}
            <p>Invalid weather data received.</p>
        {/if}
    {:else}
        <p>No weather data available.</p>
    {/if}
</div>
