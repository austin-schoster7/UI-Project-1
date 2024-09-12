<script>
    import { onMount, createEventDispatcher } from "svelte";

    export let showSidebar;
    export let currentDate; // Date selected from the sidebar
    export let firstName;
    let workouts = {}; // Store all workouts
    let restDay = false;
    let wo_rating = 0;
    let areas = [];
    let durationHours = 0;
    let durationMinutes = 0;
    let caloriesBurned = 0;
    let weight = 0;
    let notes = '';
    
    const dispatch = createEventDispatcher();

    // Watch for changes to the currentDate and load the corresponding workout
    $: if (currentDate) {
        const savedWorkouts = localStorage.getItem('workouts');
        if (savedWorkouts) {
            workouts = JSON.parse(savedWorkouts);
            const workout = workouts[currentDate];
            if (workout) {
                loadWorkout(workout);
            } else {
                resetFields();
            }
        } else {
            resetFields();
        }
    }

    // Reset all form fields when no workout data is found
    function resetFields() {
        restDay = false;
        wo_rating = 0;
        areas = [];
        durationHours = 0;
        durationMinutes = 0;
        caloriesBurned = 0;
        weight = 0;
        notes = '';
    }

    // Function to load workout data into fields
    function loadWorkout(workout) {
        restDay = workout.restDay;
        wo_rating = workout.wo_rating;
        areas = workout.areas;
        const [hours, minutes] = workout.totalDuration.split(' ').filter(e => !isNaN(e));
        durationHours = parseInt(hours);
        durationMinutes = parseInt(minutes);
        caloriesBurned = workout.caloriesBurned;
        weight = workout.weight;
        notes = workout.notes;
    }

    // Save workout data and dispatch to parent (App.svelte)
    function saveWorkout() {
        const totalDuration = `${durationHours} hours ${durationMinutes} minutes`;
        const workout = {
            wo_rating,
            areas,
            totalDuration,
            caloriesBurned,
            weight,
            notes,
            restDay
        };

        // Emit event to parent to save the workout
        dispatch('saveWorkOut', workout);
        alert('Workout saved successfully!');
    }

    // Toggle workout areas
    function toggleArea(area) {
        if (areas.includes(area)) {
            areas = areas.filter(a => a !== area);
        } else {
            areas = [...areas, area];
        }
    }

    function clearWorkoutData() {
        resetFields();
        localStorage.clear();
    }
</script>

<!-- Main Content -->
<div class="content-wrapper {showSidebar ? 'shifted' : ''}">
    <section class="main-content">
        <h1>Welcome {firstName ? firstName : ''}!</h1><br>
        <h2>Track Your Workout - {currentDate}</h2>

        <label>
            Rest day?
            <input type="checkbox" bind:checked={restDay}/>
        </label>

        {#if restDay}
            <p>Enjoy your rest day!</p>
        {:else}
            <h4>Rate your workout:</h4>
            {#each [1, 2, 3, 4, 5] as rating}
                <label>
                    <input type="radio" value={rating} bind:group={wo_rating} /> {rating}
                </label>
            {/each}

            <h4>Select areas of workout:</h4>
            {#each ['Biceps', 'Triceps', 'Chest', 'Back', 'Shoulders', 'Legs', 'Core', 'Cardio'] as area}
                <label>
                    <input type="checkbox" value={area} on:change={() => toggleArea(area)} checked={areas.includes(area)} /> {area}
                </label>
            {/each}

            <h4>Duration</h4>
            <div>
                <input type="number" bind:value={durationHours} min="0" placeholder="Hours" /> Hours
                <input type="number" bind:value={durationMinutes} min="0" max="59" placeholder="Minutes" /> Minutes
            </div>

            <h4>Calories burned</h4>
            <input type="number" bind:value={caloriesBurned}/>

            <h4>Weight</h4>
            <input type="number" bind:value={weight}/>

            <h4>Notes</h4>
            <textarea bind:value={notes}></textarea>
        {/if}

        <br><br>
        <div class="button-group">
            <button class="btn save-btn" on:click={saveWorkout}>Save Workout</button>
        </div>
          
        <br><br>
          
        <div class="button-group">
            <button class="btn clear-btn" on:click={clearWorkoutData}>Clear ALL Workout Data</button>
        </div>
          
    </section>

    <section class="today-summary">
        <h2>{currentDate} Summary</h2>
        <p>Workout Rating: {wo_rating}</p>
        <p>Areas worked: {areas.join(', ')}</p>
        <p>Duration: {durationHours} hours and {durationMinutes} minutes</p>
        <p>Calories burned: {caloriesBurned}</p>
        <p>Weight: {weight}</p>
        <p>Notes: {notes}</p>
    </section>
</div>

<style>
    .content-wrapper {
        display: flex;
        justify-content: space-between;
        transition: margin-left 0.3s ease;
    }

    .content-wrapper.shifted {
        margin-left: 350px; /* Adjust based on sidebar width */
    }

    .main-content, .today-summary {
        flex: 1; /* Allow sections to take up equal space */
        padding: 20px;
        margin: 10px;
    }

    .main-content {
        border-right: 1px solid #ccc;
    }

    .today-summary {
        border-left: 1px solid #ccc;
    }

    .btn {
        font-size: 16px;
        padding: 12px 20px;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        transition: background-color 0.3s ease, box-shadow 0.3s ease;
    }

    .save-btn {
        background-color: #4CAF50;
        color: white;
    }

    .save-btn:hover {
        background-color: #45a049;
        box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.2);
    }

    .clear-btn {
        background-color: #f44336;
        color: white;
    }

    .clear-btn:hover {
        background-color: #e53935;
        box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.2);
    }

    .button-group {
        display: flex;
        justify-content: center;
        margin: 10px 0;
    }

    button:focus {
        outline: none;
    }

    textarea {
        width: 100%;
        height: 100px;
        padding: 8px;
        box-sizing: border-box;
    }

</style>
