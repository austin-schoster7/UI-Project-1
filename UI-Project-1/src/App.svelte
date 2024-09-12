<script>
  import Header from './components/Header.svelte';
  import Sidebar from './components/Sidebar.svelte';
  import MainContent from './components/MainContent.svelte';
  import Profile from './components/Profile.svelte';
  import Goals from './components/Goals.svelte';

  let showSidebar = false;
  let currentDate = new Date().toLocaleDateString();
  let workouts = {}; // Object to store workouts
  let firstName;
  let lastName;
  let email;

  function toggleSidebar() {
    showSidebar = !showSidebar;
  }

  // Load saved workouts from localStorage on initialization
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
  }

  // Function to handle selecting a date from the sidebar
  function selectDate(day) {
    const selectedDate = new Date(new Date().getFullYear(), new Date().getMonth(), day).toLocaleDateString();
    currentDate = selectedDate;
  }

  // Save workout data to localStorage
  function saveWorkoutData(event) {
    const workout = event.detail;
    workouts[currentDate] = workout;
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
</script>

<Header {toggleSidebar} {showSidebar} on:scrollToLocation={scrollToLocation}/>
<Sidebar {showSidebar} {selectDate} {currentDate} {workouts}/>
<MainContent {showSidebar} {currentDate} {firstName} on:saveWorkOut={saveWorkoutData}/>

<div id="goals-section">
  <Goals {showSidebar}/>
</div>

<!-- Profile section -->
<div id="profile-section">
  <Profile {firstName} {lastName} {email} {showSidebar} on:saveProfile={saveProfile}/>
</div>

<style>
  :global(body) {
    margin: 0;
    font-family: Arial, sans-serif;
    padding-top: 100px;
  }
</style>
