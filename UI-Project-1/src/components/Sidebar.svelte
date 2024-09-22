<script>
  export let showSidebar;
  export let selectDate;
  export let currentDate;
  export let workouts = {};
  export let longestDuration = 0;
  export let goalData;
  export let saveWorkoutPressed = false;

  const today = new Date().getDate();
  let currentMonth = new Date().getMonth();
  let currentYear = new Date().getFullYear();
  
  let firstEntryDate = "No entries";
  let currentStreak = 0;
  let totalWorkouts = 0;
  let totalRestDays = 0;
  let avgWorkoutRating = 0;
  let avgWorkoutDuration = '0 hours 0 minutes';
  let avgWorkoutsPerWeek = 0;
  let longestDistance;
  let benchPR = 0;
  let milePRMinBest = 0;
  let milePRSecBest = 0;
  let milePRBest = 0;

  let avgRating = 0;
  let benchPRGoal = 0;
  let milePRMinutes = 0;
  let milePRSeconds = 0;
  let workoutsPerWeek = 0;
  let weightGoal = 0;
  let bestWeight = 0;
  let higherOrLower;

  let totalRating = 0;
  let totalDurationMinutes = 0;
  let streak = 0;

  // Load saved workouts
  $: {
    const savedWorkouts = localStorage.getItem('workouts');
    if (savedWorkouts) {
      workouts = JSON.parse(savedWorkouts);

      // Load stats
      let savedStats = JSON.parse(localStorage.getItem('stats'));
      if (savedStats) {
        firstEntryDate = savedStats.firstEntryDate ? savedStats.firstEntryDate : "No entries";
        currentStreak = savedStats.currentStreak ? savedStats.currentStreak : 0;
        totalWorkouts = savedStats.totalWorkouts ? savedStats.totalWorkouts : 0;
        totalRestDays = savedStats.totalRestDays ? savedStats.totalRestDays : 0;
        avgWorkoutRating = savedStats.avgWorkoutRating ? savedStats.avgWorkoutRating : 0;
        avgWorkoutDuration = savedStats.avgWorkoutDuration ? savedStats.avgWorkoutDuration : '0 hours 0 minutes';
        avgWorkoutsPerWeek = savedStats.avgWorkoutsPerWeek ? savedStats.avgWorkoutsPerWeek : 0;
        longestDistance = savedStats.longestDistance ? savedStats.longestDistance : 0;
        totalRating = savedStats.totalRating ? savedStats.totalRating : 0;
        totalDurationMinutes = savedStats.totalDurationMinutes ? savedStats.totalDurationMinutes : 0;
        benchPR = savedStats.benchPR ? savedStats.benchPR : 0;
        milePRBest = savedStats.milePRBest ? savedStats.milePRBest : 0;
        milePRMinBest = savedStats.milePRMinBest ? savedStats.milePRMinBest : 0;
        milePRSecBest = savedStats.milePRSecBest ? savedStats.milePRSecBest : 0;
        bestWeight = savedStats.bestWeight ? savedStats.bestWeight : (higherOrLower === 'higher' ? 0 : 9999);
      }
    }
  }

  // Calculate stats when workouts change
  $: if (workouts && saveWorkoutPressed) {
    calculateStats(workouts[currentDate]);
    //saveWorkoutPressed = false;
  }

  // Load saved goals
  $: {
    if (goalData) {
      avgRating = goalData.avgRating ? goalData.avgRating : 0;
      benchPRGoal = goalData.benchPR ? goalData.benchPR : 0;
      milePRMinutes = goalData.milePRMinutes ? goalData.milePRMinutes : 0;
      milePRSeconds = goalData.milePRSeconds ? goalData.milePRSeconds : 0;
      workoutsPerWeek = goalData.workoutsPerWeek ? goalData.workoutsPerWeek : 0;
      weightGoal = goalData.weightGoal ? goalData.weightGoal : 0;
      higherOrLower = goalData.higherOrLower ? goalData.higherOrLower : '';
    }
  }

  // Function to calculate the stats
  function calculateStats(workout) {

    clearStats();
    const workoutDates = Object.keys(workouts).sort((a, b) => new Date(a).getTime() - new Date(b).getTime());
    
    firstEntryDate = workoutDates.length ? workoutDates[0] : "No entries";
    workoutDates.forEach(date => {
      const workout = workouts[date];

      let totalDurationForThisWorkout = 0;

      // Calculate total workouts and rest days
      if (workout.restDay) {
        totalRestDays++;
      } 
      else {

        totalWorkouts++;
        totalRating += workout.wo_rating;

        const durationParts = workout.totalDuration.split(' ').filter(e => !isNaN(e));
        const hours = parseInt(durationParts[0], 10) || 0;
        const minutes = parseInt(durationParts[1], 10) || 0;
        totalDurationForThisWorkout = hours * 60 + minutes;
        totalDurationMinutes += hours * 60 + minutes;

        // Check for streak (only if the previous date had a workout)
        const previousDate = new Date(date);
        previousDate.setDate(previousDate.getDate() - 1);
        const previousDateString = previousDate.toLocaleDateString();

        if (workoutDates.includes(previousDateString)) {
          streak++;
        } 
        else {
          streak = 1;
        }
      }

      currentStreak = streak;
      avgWorkoutRating = totalWorkouts > 0 ? (totalRating / totalWorkouts) : 0;
      avgWorkoutDuration = totalWorkouts > 0 ? `${Math.floor(totalDurationMinutes / totalWorkouts / 60)} hours and ${(totalDurationMinutes / totalWorkouts % 60).toFixed(1)} minutes` : '0 hours 0 minutes';

      longestDistance = workout.distance > longestDistance ? workout.distance : longestDistance;

      // Check for PRs
      if (workout.benchWeight > benchPR) {
        benchPR = workout.benchWeight;
      }
      if (workout.areas.includes('Cardio') && workout.selectedCardioType === 'Running' && totalDurationForThisWorkout / workout.distance > milePRBest) {
        milePRBest = totalDurationForThisWorkout / workout.distance;
        milePRMinBest = Math.floor(milePRBest);
        milePRSecBest = milePRBest % 1 * 60;
      }
      // Check for weight goal
      if (higherOrLower === 'higher' && workout.weight > bestWeight) {
        bestWeight = workout.weight;
      }
      else if (higherOrLower === 'lower' && workout.weight !== 0 && workout.weight < bestWeight) {
        bestWeight = workout.weight;
      }
    });

    let stats = {
      firstEntryDate,
      currentStreak,
      totalWorkouts,
      totalRestDays,
      avgWorkoutRating,
      avgWorkoutDuration,
      avgWorkoutsPerWeek,
      longestDistance,
      totalRating,
      totalDurationMinutes,
      benchPR,
      milePRBest,
      milePRMinBest,
      milePRSecBest,
      bestWeight
    };
    localStorage.setItem('stats', JSON.stringify(stats));
  }

  // Function to clear stats
  function clearStats() {
    //localStorage.removeItem('stats');
    firstEntryDate = "No entries";
    currentStreak = 0;
    streak = 0;
    totalWorkouts = 0;
    totalRestDays = 0;
    avgWorkoutRating = 0;
    avgWorkoutDuration = '0 hours 0 minutes';
    avgWorkoutsPerWeek = 0;
    longestDistance = 0;
    totalDurationMinutes = 0;
    totalRating = 0;
    milePRBest = 0;
    milePRMinBest = 0;
    milePRSecBest = 0;
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
          id="day-{index + 1}" class="calendar-day {isSelected(index + 1) ? 'selected' : ''} 
          {today === index + 1 && currentMonth === new Date().getMonth() && currentYear === new Date().getFullYear() ? 'today' : ''} 
          {workouts[new Date(currentYear, currentMonth, index + 1).toLocaleDateString()] && !workouts[new Date(currentYear, currentMonth, index + 1).toLocaleDateString()].restDay ? 'streak' : ''} 
          {workouts[new Date(currentYear, currentMonth, index + 1).toLocaleDateString()] && workouts[new Date(currentYear, currentMonth, index + 1).toLocaleDateString()].restDay ? 'restdays' : ''}"
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
    <!-- <p>Average workouts per week: {avgWorkoutsPerWeek}</p> -->
    <p>Longest cardio distance: {longestDistance} mi</p>
  </div>

  <div class="goals">
    <h3>Goals</h3>
    <p class="avg-rating-goal {avgWorkoutRating >= avgRating ? 'met' : ''}">Average workout rating: {avgWorkoutRating.toFixed(1)} / {avgRating}</p>
    <p class="bench-goal {benchPR >= benchPRGoal ? 'met' : ''}">Bench press PR: {benchPR} / {benchPRGoal} lbs.</p>
    <p class="milepr-goal {milePRBest <= milePRMinutes + (milePRSeconds / 60) ? 'met' : ''}">Mile PR: {milePRMinBest} minutes and {milePRSecBest} seconds / {milePRMinutes} minutes and {milePRSeconds} seconds</p>
    <!-- <p>Workouts per week: / {workoutsPerWeek}</p> -->
    <p class="weight-goal {higherOrLower === 'higher' ? (bestWeight >= weightGoal ? 'met' : '') : (bestWeight <= weightGoal ? 'met' : '')}">Weight: {bestWeight} / {weightGoal} lbs.</p>

  </div>
</div>

<style>
  .sidebar {
    position: absolute;
    left: -350px;
    top: 0;
    width: 350px;
    height: 2500px;
    background-color: #2D3C51; 
    transition: left 0.3s ease;
    box-shadow: 2px 0 5px rgba(0, 0, 0, 0.1);
    overflow: hidden;
    z-index: 2;
    overflow-y: auto;
    color: #F5F4F3;
  }

  .sidebar.open {
    left: 0;
  }

  .calendar {
    width: 100%;
    padding: 10px;
    box-sizing: border-box;
    background-color: #F5F4F3; 
    color: #2D3C51; 
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
    border: 2px solid #8EBFDA;
    cursor: pointer;
  }

  .calendar-day.empty {
    border: none;
  }

  .calendar-day.selected {
    background-color: #4E71C6;
    color: white;
  }

  .calendar-day.streak {
    border: 2px solid #e9983b;
  }

  .calendar-day.restdays {
    border: 2px solid #7572A8;
  }

  .calendar-day.today {
    border: 2px solid red;
  }

  .calendar-day:hover {
    background-color: #8EBFDA;
  }

  button {
    cursor: pointer;
    padding: 5px 10px;
    background-color: #4E71C6;
    color: white;
    border: none;
  }

  .stats {
    padding: 10px;
    color: white;
  }

  .goals {
    padding: 10px;
    color: white;
  }

  .avg-rating-goal.met {
    text-decoration: line-through;
    color: #4E71C6;
  }

  .bench-goal.met {
    text-decoration: line-through;
    color: #4E71C6;
  }

  .milepr-goal.met {
    text-decoration: line-through;
    color: #4E71C6;
  }

  .weight-goal.met {
    text-decoration: line-through;
    color: #4E71C6;
  }
</style>

