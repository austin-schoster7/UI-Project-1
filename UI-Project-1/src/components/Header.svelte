<script>
    import { createEventDispatcher } from "svelte";

    export let toggleSidebar;
    export let showSidebar;

    const dispatch = createEventDispatcher();

    let currentDateTime = new Date();

    // Function to update the current date and time
    function updateDateTime() {
        currentDateTime = new Date();
    }

    function scrollToLocation(location) {
        // Emit an event to App.svelte to trigger the scroll
        dispatch('scrollToLocation', {location});
    }

    // Update the time every second
    setInterval(updateDateTime, 1000);
</script>

<header class="app-header">
    <div class="header-left {showSidebar ? 'shifted' : ''}">
        <h1>Workout Tracker</h1>
        <!-- Displaying current date and time -->
        <p class="date-time">{currentDateTime.toLocaleDateString()} {currentDateTime.toLocaleTimeString()}</p>
    </div>
    
    <div class="header-buttons">
        <div class="button-wrapper">
            <button on:click={toggleSidebar} class="menu-button">&#9776;</button>
            <label for="menu-button">Menu</label>
        </div>

        <div class="button-wrapper">
            <button on:click={() => scrollToLocation('goals')} class="goals-button">&#x2713;</button>
            <label for="goal-button">Goals</label>
        </div>
      
        <div class="button-wrapper">
            <button on:click={() => scrollToLocation('profile')} class="profile-button">&#128100;</button>
            <label for="profile-button">Profile</label>
        </div>
      
        <div class="button-wrapper">
          <button class="settings-button">&#9881;</button>
          <label for="settings-button">Settings</label>
        </div>
    </div>
      
</header>

<style>
    .app-header {
        position: fixed; /* Make the header fixed at the top */
        top: 0;
        width: 100%;
        height: 100px;
        background-color: #00aaf8;
        z-index: 1;
        display: flex;
        align-items: center;
        justify-content: space-between;
        box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.1);
        transition: margin-left 0.3s ease;
    }

    /* .app-header.shifted {
        margin-left: 350px;
    } */

    .header-left {
        display: flex;
        flex-direction: column;
        transition: margin-left 0.3s ease;
        padding: 20px;
        padding-top: 10px;
    }

    .header-left.shifted {
        margin-left: 350px;
    }

    .date-time {
        font-size: 1rem;
        color: #363a3e;
        margin: 0;
    }

    .header-buttons {
        display: flex;
        gap: 20px;
        padding: 0 20px;
    }

    .button-wrapper {
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 5px;
    }

    button {
        font-size: 24px; 
        background: none;
        border: none;
        cursor: pointer;
    }

    label {
        font-size: 14px;
        text-align: center;
    }
</style>
