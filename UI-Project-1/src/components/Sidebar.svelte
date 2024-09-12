<script>
  export let showSidebar;
  export let selectDate;
  export let currentDate;
  export let workouts = {};

  const today = new Date().getDate();
  let currentMonth = new Date().getMonth();
  let currentYear = new Date().getFullYear();
  
  let firstEntryDate = null;
  let currentStreak = 0;
  let totalWorkouts = 0;
  let totalRestDays = 0;
  let avgWorkoutRating = 0;
  let avgWorkoutDuration = '0 hours 0 minutes';
  let avgWorkoutsPerWeek = 0;

  // Load saved workouts
  $: {
    const savedWorkouts = localStorage.getItem('workouts');
    if (savedWorkouts) {
      workouts = JSON.parse(savedWorkouts);
      calculateStats();
    }
  }

  // Calculate stats when workouts change
  $: if (workouts) {
    calculateStats();
  }

  // Function to calculate the stats
  function calculateStats() {
    let totalRating = 0;
    let totalDurationMinutes = 0;
    let streak = 0;
    let isInStreak = true;

    const workoutDates = Object.keys(workouts).sort((a, b) => new Date(a).getTime() - new Date(b).getTime());

    firstEntryDate = workoutDates.length ? workoutDates[0] : "No entries";

    workoutDates.forEach((date) => {
      const workout = workouts[date];
      
      // Calculate total workouts and rest days
      if (workout.restDay) {
        totalRestDays++;
        isInStreak = false;
      } 
      else {
        totalWorkouts++;
        totalRating += workout.wo_rating;

        const durationParts = workout.totalDuration.split(' ').filter(e => !isNaN(e));
        const hours = parseInt(durationParts[0], 10) || 0;
        const minutes = parseInt(durationParts[1], 10) || 0;
        totalDurationMinutes += hours * 60 + minutes;

        // Check for streak (only if the day before had a workout)
        if (isInStreak) streak++;
        isInStreak = true;
      }
    });

    currentStreak = streak;
    avgWorkoutRating = totalWorkouts > 0 ? (totalRating / totalWorkouts) : 0;
    avgWorkoutDuration = totalWorkouts > 0 ? `${Math.floor(totalDurationMinutes / totalWorkouts / 60)} hours and ${totalDurationMinutes / totalWorkouts % 60} minutes` : '0 hours 0 minutes';
  }

  // Helper functions to get month and days information
  const daysInMonth = (month, year) => new Date(year, month + 1, 0).getDate();
  const getFirstDayOfMonth = (month, year) => new Date(year, month, 1).getDay();

  // Function to move to the next month
  function nextMonth() {
    if (currentMonth === 11) {
      currentMonth = 0;
      currentYear++;
    } 
    else {
      currentMonth++;
    }
  }

  // Function to move to the previous month
  function prevMonth() {
    if (currentMonth === 0) {
      currentMonth = 11;
      currentYear--;
    } 
    else {
      currentMonth--;
    }
  }

  // Helper function to check if a date is selected
  function isSelected(day) {
    const selectedDate = new Date(currentYear, currentMonth, day).toLocaleDateString();
    return selectedDate === currentDate;
  }
</script>

<div class="sidebar {showSidebar ? 'open' : ''}">
  <div class="calendar">
    <h3>Calendar</h3>
    
    <!-- Month and Year Controls -->
    <div class="calendar-controls">
      <button on:click={prevMonth}>&lt;</button>
      <span>{new Date(currentYear, currentMonth).toLocaleString('default', { month: 'long' })} {currentYear}</span>
      <button on:click={nextMonth}>&gt;</button>
    </div>
  
    <!-- Days of the Week Header -->
    <div class="calendar-grid-header">
      {#each ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'] as day}
        <div class="calendar-day-name">{day}</div>
      {/each}
    </div>
  
    <!-- Calendar Grid for Days -->
    <div class="calendar-grid">
      {#each Array(getFirstDayOfMonth(currentMonth, currentYear)) as _}
        <div class="calendar-day empty"></div> <!-- Empty cells to align the start of the month -->
      {/each}
      
      {#each Array(daysInMonth(currentMonth, currentYear)) as _, index}
        <div
          class="calendar-day {isSelected(index + 1) ? 'selected' : ''} {today === index + 1 && currentMonth === currentMonth && currentYear === currentYear ? 'today' : ''}"
          on:click={() => selectDate(index + 1)}
        >
          {index + 1}
        </div>
      {/each}
    </div>
  </div>

  <div class="stats">
    <h3>Stats</h3>
    <p>First entry: {firstEntryDate}</p>
    <p>Current streak: {currentStreak}</p>
    <p>Total workouts: {totalWorkouts}</p>
    <p>Total rest days: {totalRestDays}</p>
    <p>Average Workout Rating: {avgWorkoutRating.toFixed(1)}</p>
    <p>Average time per workout: {avgWorkoutDuration}</p>
    <p>Average workouts per week: {avgWorkoutsPerWeek}</p>
  </div>
</div>

<style>
  .sidebar {
    position: absolute;
    left: -350px;
    top: 0;
    width: 350px;
    height: 100%;
    background-color: #f4f4f4;
    transition: left 0.3s ease;
    box-shadow: 2px 0 5px rgba(0, 0, 0, 0.1);
    overflow: hidden;
    z-index: 2;
  }

  .sidebar.open {
    left: 0;
  }

  .calendar {
    width: 100%;
    padding: 10px;
    box-sizing: border-box;
  }

  .calendar-controls {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 10px;
  }

  .calendar-grid-header, .calendar-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 5px;
    
  }

  .calendar-day {
    text-align: center;
    padding: 10px;
    border: 1px solid #ddd;
    cursor: pointer;
  }

  .calendar-day.empty {
    border: none;
  }

  .calendar-day.selected {
    background-color: #4CAF50;
    color: white;
  }

  .calendar-day.today {
    border: 2px solid red;
  }

  .calendar-day:hover {
    background-color: #ddd;
  }

  button {
    cursor: pointer;
    padding: 5px 10px;
  }

  .stats {
    padding: 10px;
  }
</style>
