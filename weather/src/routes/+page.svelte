<script>
    import { writable } from 'svelte/store';
    
    // Define writable stores for weather and city
    let weather = writable(null);
    let city = writable('');

    // Fetch weather data for a given city name and update the weather store
    const fetch_weather = async (cityName) => {
        const response = await fetch(`https://api.openweathermap.org/data/2.5/weather?q=${cityName}&appid=cb683232519a768bc3326a68dc742f4b`);
        const data = await response.json();
        weather.set(data);
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
        <div>
            City: {$weather.name}
            <br>
            Temperature: {$weather.main.temp}°K
        </div>
    {:else}
        <p>No weather data available.</p>
    {/if}
</div>
