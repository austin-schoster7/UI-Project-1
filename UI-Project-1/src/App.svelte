<script>
  import Header from './components/Header.svelte';
  import Sidebar from './components/Sidebar.svelte';
  import MainContent from './components/MainContent.svelte';
  import Profile from './components/Profile.svelte';
  import Goals from './components/Goals.svelte';
  import Settings from './components/Settings.svelte';

  let showSidebar = false;
  let currentDate = new Date().toLocaleDateString();
  let workouts = {}; // Object to store workouts
  let firstName;
  let lastName;
  let email;
  let avgRating;
  let benchPR;
  let milePRMinutes;
  let milePRSeconds;
  let workoutsPerWeek;
  let weightGoal;
  let longestDuration;
  let goalData;
  let saveWorkoutPressed = false;

  function toggleSidebar() {
    showSidebar = !showSidebar;
  }

  // Load saved information from localStorage
  $: {
    const savedWorkouts = localStorage.getItem('workouts');
    if (savedWorkouts) {
      workouts = JSON.parse(savedWorkouts);
    }

    let profileInfo = localStorage.getItem('profile');
    if (profileInfo) {
      firstName = JSON.parse(profileInfo).firstName;
      lastName = JSON.parse(profileInfo).lastName;
      email = JSON.parse(profileInfo).email;
    }

    let goalsInfo = localStorage.getItem('goals');
    if (goalsInfo) {
      avgRating = JSON.parse(goalsInfo).avgRating;
      benchPR = JSON.parse(goalsInfo).benchPR;
      milePRMinutes = JSON.parse(goalsInfo).milePRMinutes;
      milePRSeconds = JSON.parse(goalsInfo).milePRSeconds;
      workoutsPerWeek = JSON.parse(goalsInfo).workoutsPerWeek;
      weightGoal = JSON.parse(goalsInfo).weight;
      goalData = JSON.parse(goalsInfo);
    }
    console.log(workouts);
  }

  // Function to handle selecting a date from the sidebar
  function selectDate(day) {
    // Get selected date
    const selectedDate = new Date(new Date().getFullYear(), new Date().getMonth(), day).toLocaleDateString();

    // Get previous day
    let prevDay = currentDate.split('/')[1];
    let prevCalendarDay = document.getElementById(`day-${prevDay}`);

    // Update the current date
    currentDate = selectedDate;
    let calendarDay = document.getElementById(`day-${day}`);
    
    // Make selected day green and remove the green color from the previous day
    calendarDay.classList.add('selected');
    if (prevCalendarDay) {
      prevCalendarDay.classList.remove('selected');
    }
  }

  // Save workout data to localStorage
  function saveWorkoutData(event) {
    const workout = event.detail;

    saveWorkoutPressed = true;
    // Check if the workout already exists for the selected date
    if (workouts[currentDate]) {
      workouts[currentDate] = { ...workouts[currentDate], ...workout };
      console.log(workouts[currentDate]);
    }
    else {
      workouts[currentDate] = workout;
      document.getElementById(`day-${new Date(currentDate).getDate()}`).classList.add('streak');
    }

    localStorage.setItem('workouts', JSON.stringify(workouts));
  }

  function scrollToLocation(event) {
    const location = event.detail.location;
    if (location === 'profile') {
      document.getElementById('profile-section').scrollIntoView({ behavior: 'smooth' });
    }
    else if (location === 'goals') {
      document.getElementById('goals-section').scrollIntoView({ behavior: 'smooth' });
    }
    else if (location === 'settings') {
      document.getElementById('settings-section').scrollIntoView({ behavior: 'smooth' });
    }
  }

  function saveProfile(event) {
    firstName = event.detail;
  }

  function saveGoals(event) {
    goalData = event.detail;
  }
</script>

<Header {toggleSidebar} {showSidebar} on:scrollToLocation={scrollToLocation}/>
<Sidebar {showSidebar} {selectDate} {currentDate} {workouts} {longestDuration} {goalData} {saveWorkoutPressed}/>
<MainContent {showSidebar} {currentDate} {firstName} on:saveWorkOut={saveWorkoutData}/>

<div id="goals-section">
  <Goals on:saveGoals={saveGoals} {showSidebar} {goalData}/>
</div>

<div id="profile-section">
  <Profile {firstName} {lastName} {email} {showSidebar} on:saveProfile={saveProfile}/>
</div>

<div id="settings-section">
  <Settings {showSidebar}/>
</div>

<style>
  :global(body) {
    margin: 0;
    font-family: Arial, sans-serif;
    padding-top: 100px;
    background-color: #F5F4F3;
    color: #2D3C51;
  }
</style>

