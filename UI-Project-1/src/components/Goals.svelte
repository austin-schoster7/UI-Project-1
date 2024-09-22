<script>
    import { createEventDispatcher, onMount } from "svelte";

    const dispatch = createEventDispatcher();

    export let showSidebar;
    export let goalData;

    let avgRating;
    let benchPR;
    let milePRMinutes;
    let milePRSeconds;
    let workoutsPerWeek;
    let weightGoal;
    let higherOrLower;


    // Load saved goals
    onMount(() => {
        if (goalData) {
            avgRating = goalData.avgRating;
            benchPR = goalData.benchPR;
            milePRMinutes = goalData.milePRMinutes;
            milePRSeconds = goalData.milePRSeconds;
            workoutsPerWeek = goalData.workoutsPerWeek;
            weightGoal = goalData.weightGoal;
            higherOrLower = goalData.higherOrLower;
        }
    });
  
    // Save goals
    function saveGoals() {
        let goals = {
            avgRating,
            benchPR,
            milePRMinutes,
            milePRSeconds,
            workoutsPerWeek,
            weightGoal,
            higherOrLower
        };
        dispatch('saveGoals', goals);
        localStorage.setItem('goals', JSON.stringify(goals));
        alert('Goals saved successfully!');
    }
  </script>
  
  <div class="goal-page {showSidebar ? 'shifted' : ''}">
    <h2>Goals</h2>
    
    <form on:submit|preventDefault={saveGoals}>
      <label for="avg-rating-goal">Average workout rating</label>
      <input type="text" id="avg-rating-goal" placeholder="3.5" bind:value={avgRating} />

      <label for="bench-pr">Bench Press PR</label>
      <input type="number" id="bennch-pr" placeholder="215" bind:value={benchPR} />
  
      <div>
        <label>Mile PR</label>
        <div class="mile-pr">
            <input type="number" id="mile-minutes" placeholder="5" bind:value={milePRMinutes} min="0" /> Minutes
            <input type="number" id="mile-seconds" min="0" max="59" placeholder="0" bind:value={milePRSeconds} /> Seconds
        </div>
      </div>

      <!-- <label for="wo-per-week">Workouts per Week</label>
      <input type="number" id="wo-per-week" placeholder="3" bind:value={workoutsPerWeek} /> -->

      <label for="weight">Weight</label>
      <div class="weight">
        <select id="weight-select" bind:value={higherOrLower}>
            <option value="higher">Higher Than</option>
            <option value="lower">Lower Than</option>
        </select>

        <input type="number" id="weight" placeholder="180" bind:value={weightGoal} />
        <p>lbs</p>
      </div>

      <button type="submit">Save Goals</button>
    </form>
  </div>
  
  <style>
    .goal-page {
      padding: 20px;
      border-top: 5px solid #8EBFDA;
      margin-top: 20px;
      transition: margin-left 0.3s ease;
    }

    .goal-page.shifted {
      margin-left: 350px;
    }
  
    label {
      display: block;
      margin-bottom: 5px;
    }
  
    input {
      margin-bottom: 15px;
      padding: 8px;
      width: 100%;
      box-sizing: border-box;
      width: 200px;
      border: #8EBFDA 1px solid;
      border-radius: 5px;
    }
  
    button {
        font-size: 16px;
        padding: 12px 20px;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        transition: background-color 0.3s ease, box-shadow 0.3s ease;
        background-color: #4E71C6;
        color: white;
    }

    button:focus {
        outline: none;
    }

    button:hover {
        background-color: #7572A8;
        box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.2);
    }

    .mile-pr {
        display: flex;
        gap: 10px;
    }

    .mile-pr input {
        width: 50px;
        height: 20px;
    }

    .weight {
        display: flex;
        gap: 10px;
    }

    .weight input {
        width: 200px;
    }

    .weight select {
        width: 200px;
        padding: 8px;
        border-radius: 5px;
        height: 35px;
        border: #8EBFDA 1px solid;
    }
  </style>
  