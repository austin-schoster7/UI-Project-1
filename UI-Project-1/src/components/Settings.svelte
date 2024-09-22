<script>
    import { createEventDispatcher } from "svelte";

    const dispatch = createEventDispatcher();

    export let showSidebar;
  
    function saveSettings() {
        
    }

    function confirmClearData() {
        const userConfirmed = confirm('Are you sure you want to clear all data?');
        if (userConfirmed) {
            clearWorkoutData();
            alert('All data cleared successfully!');
        }
    }

    function clearWorkoutData() {
        localStorage.clear();
    }
  </script>
  
  <div class="goal-page {showSidebar ? 'shifted' : ''}">
    <h2>Settings</h2>
    
    <div class="button-group">
        <button class="btn clear-btn" on:click={confirmClearData}>Clear ALL Data</button>
    </div>
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

    .clear-btn {
        background-color: #e74c3c;
        color: white;
    }

    .clear-btn:hover {
        background-color: #c0392b;
        box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.2);
    }

    .button-group {
        display: flex;
        /* justify-content: center; */
        margin: 10px 0;
    }
  </style>
  