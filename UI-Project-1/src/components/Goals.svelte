<script>
    import { createEventDispatcher } from "svelte";

    const dispatch = createEventDispatcher();

    export let showSidebar;
    export let avgRating = 0;
    export let lastName = "";
    export let email = "";
  
    function saveGoals() {
        //dispatch('saveGoals', firstName);
        //localStorage.setItem('goals', JSON.stringify({ firstName, lastName, email }));
        alert('Goals saved successfully!');
    }
  </script>
  
  <div class="goal-page {showSidebar ? 'shifted' : ''}">
    <h2>Goals</h2>
    
    <form on:submit|preventDefault={saveGoals}>
      <label for="avg-rating-goal">Average workout rating</label>
      <input type="text" id="avg-rating-goal" placeholder=3.5 bind:value={avgRating} />

      <label for="last-name">Last Name</label>
      <input type="text" id="last-name" placeholder="Last name" bind:value={lastName} />
  
      <label for="email">Email</label>
      <input type="email" id="email" placeholder="example@email.com" bind:value={email} />
  
      <button type="submit">Save Goals</button>
    </form>
  </div>
  
  <style>
    .goal-page {
      padding: 20px;
      border-top: 1px solid #ccc;
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
    }
  
    button {
        font-size: 16px;
        padding: 12px 20px;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        transition: background-color 0.3s ease, box-shadow 0.3s ease;
        background-color: #4CAF50;
        color: white;
    }

    button:focus {
        outline: none;
    }

    button:hover {
        background-color: #45a049;
        box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.2);
    }
  </style>
  