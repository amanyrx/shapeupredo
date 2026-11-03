<!--<script setup>-->
<!--import { ref } from 'vue'-->
<!--const likes = ref(0)-->
<!--const name = ref('Amany Rozuan')-->
<!--const bio = ref('I am studying Mobile and Web Computing in TUS Moylish.')-->
<!--// TASK 5: Create reactive showFact and fact values.-->
<!--const showFact = ref(false)-->
<!--const fact = ref('I can name five constellations.')-->
<!--</script>-->
<script setup>
import { ref, computed } from 'vue'
import ExerciseList from "./components/ExerciseList.vue";
const editingId = ref(null)
const newExercise = ref('')
const newMinutes = ref(15)
const errorMessage = ref('')
let nextId = 3
const exercises = ref([
  { id: 1, name: 'Walking', minutes: 20, completed: false },
  { id: 2, name: 'Stretching', minutes: 10, completed: false }
])
function removeExercise(id) {
  exercises.value = exercises.value.filter(
      exercise => exercise.id !== id
  )
}
function saveExercise() {
  const name = newExercise.value.trim()
  const minutes = Number(newMinutes.value)
  if (!name) {
    errorMessage.value = 'Please enter an exercise name.'
    return
  }
  if (!Number.isInteger(minutes) || minutes < 1 || minutes > 180) {
    errorMessage.value = 'Enter a whole number from 1 to 180 minutes.'
    return
  }
  if (editingId.value === null) {
    exercises.value.push({
      id: nextId++,
      name,
      minutes,
      completed: false
    })
  }
  else { const exercise = exercises.value.find(
      item => item.id === editingId.value
  )
    if (!exercise) { errorMessage.value = 'This exercise is no longer available.'
      return
    }
    exercise.name = name
    exercise.minutes = minutes
  }
  resetForm()
}
const completedCount = computed(() => {
  return exercises.value.filter(exercise => exercise.completed).length
})
const totalMinutes = computed(() => {
  return exercises.value.reduce(
      (total, exercise) => total + exercise.minutes, 0
  )
})
const progressPercent = computed(() => {
  if (exercises.value.length === 0) return 0
  return Math.round(
      (completedCount.value / exercises.value.length) * 100
  )
})
function clearCompleted() {
  exercises.value = exercises.value.filter(
      exercise => !exercise.completed
  )
}
function startEditing(id) {
  const exercise = exercises.value.find(item => item.id === id)
  if (!exercise) return
  editingId.value = id
  newExercise.value = exercise.name
  newMinutes.value = exercise.minutes
  errorMessage.value = ''
}
function resetForm() {
  editingId.value = null
  newExercise.value = ''
  newMinutes.value = 15
  errorMessage.value = ''
}
</script>

<!--<template>-->
<!--  <main>-->
<!--    <article class="card">-->
<!--      <p class="eyebrow">MY FIRST VUE PAGE</p>-->
<!--      <h1>{{ name }}</h1>-->
<!--      <p>{{ bio }}</p>-->
<!--      <label for="name">Your name</label>-->
<!--      <input id="name" v-model="name" maxlength="60">-->
<!--      <label for="bio">Your bio</label>-->
<!--      <input id="bio" v-model="bio" maxlength="60">-->
<!--      <button type = "button" @click="likes++">Like this page</button>-->
<!--      <button type = "button" @click="likes=0">Reset Likes</button>-->
<!--      <br><br>-->
<!--      <p aria-live="polite">Likes: {{likes}}</p>-->
<!--      <button type="button" @click="showFact = !showFact":aria-expanded="showFact" aria-controls="fact">-->
<!--        {{showFact ? 'Hide fun fact' : 'show fun fact' }}-->
<!--      </button>-->
<!--      <br><br>-->
<!--      <p id="fact" v-if="showFact">{{fact}}</p>-->
<!--    </article>-->
<!--  </main>-->
<!--</template>-->

<template>
  <main class="dashboard">
    <header class="hero">
      <p class="eyebrow">YOUR DAILY WORKOUT PLANNER</p>
      <h1>Shape Up</h1>
      <p>How do you eat an elephant? One bite at a time!</p>
    </header>
    <section class="panel summary" aria-labelledby="progress-heading">
      <div>
        <h2 id="progress-heading">Your progress</h2>
        <p aria-live="polite">
          {{ completedCount }} of {{ exercises.length }} exercises completed
        </p>
        <p>{{ totalMinutes }} minutes planned</p>
      </div>
      <div>
        <label for="workout-progress">{{ progressPercent }}% complete</label>
        <progress id="workout-progress" :value="progressPercent" max="100">
          {{ progressPercent }}%
        </progress>
      </div>
    </section>

    <div class="dashboard-grid">
      <section class="panel">
        <h2>{{ editingId === null ? 'Plan an exercise' : 'Edit exercise' }}</h2>
        <form @submit.prevent="saveExercise">
          <label for="exercise-name">Exercise name</label>
          <input id="exercise-name" v-model="newExercise" type="text"
                 placeholder="For example, cycling" maxlength="60" required>
          <label for="exercise-minutes">Duration in minutes</label>
          <input id="exercise-minutes" v-model.number="newMinutes"
                 type="number" min="1" max="180" step="1" required>
          <button type="submit" class="primary-button">
            {{ editingId === null ? 'Add exercise' : 'Save changes' }}
          </button>
          <button v-if="editingId !== null" type="button"
                  class="secondary-button" @click="resetForm">
            Cancel </button>
          <p v-if="errorMessage" class="error-message" role="alert">
            {{ errorMessage }}
          </p>
        </form>
      </section>
      <section class="panel">
        <h2>Your workout</h2>
        <ExerciseList
            :exercises="exercises"
            :editing="editingId !== null"
            @edit="startEditing()"
            @remove="removeExercise()"
            @toggle="toggleExercise"
        <button type="button" class="secondary-button"
                @click="clearCompleted" :disabled="completedCount === 0">
          Clear completed
        </button>
      </section>
    </div>
  </main>
</template>

<!--<style>-->
<!--* { box-sizing: border-box; }-->
<!--body {-->
<!--  margin: 0;-->
<!--  font-family: Arial, sans-serif;-->
<!--  background: #eef2f6;-->
<!--  color: #192d3a;-->
<!--  background-color: #d0c8ed;-->
<!--}-->
<!--main { min-height: 100vh; display: grid; place-items: center; padding: 24px; }-->
<!--.card { width: 100%; max-width: 520px; padding: 60px; background: white; border-radius: 18px; background-color: #eae3ff;-->
<!--}-->
<!--.eyebrow { color: #1d1247; font-size: 12px; font-weight: bold; letter-spacing: 2px; }-->
<!--h1 { overflow-wrap: anywhere; color: #1d1247;-->
<!--}-->
<!--label { display: block; margin-top: 24px; font-weight: bold; }-->
<!--input { width: 100%; padding: 12px; margin-top: 8px; border: 1px solid #637580; border-radius: 8px; font: inherit; }-->
<!--button { padding: 12px 16px; margin: 16px 8px 0 0; border: 0; border-radius: 8px; background: #392b70; color: white; font: inherit; cursor: pointer; }-->
<!--button:hover { background: #524585; }-->
<!--:focus-visible { outline: 3px solid #b55000; outline-offset: 3px; }-->
<!--</style>-->

<style>
:root {
  --background: #f0f4f8;
  --surface: #ffffff;
  --text: #172b3a;
  --muted: #526575;
  --brand: #473b77;
  --brand-dark: #473b77;
  --border: #ccd7df;
}
* { box-sizing: border-box; }
body {
  margin: 0;
  background: var(--background);
  color: var(--text);
  font-family: Arial, sans-serif;
}
button, input { font: inherit; }
.dashboard {
  max-width: 1100px;
  margin: 0 auto;
  padding: 32px 20px 60px;
}
.hero {
  padding: 40px;
  margin-bottom: 24px;
  border-radius: 24px;
  background: linear-gradient(120deg, #3f3370, #b1a7da);
  color: white;
}
.eyebrow {
  font-size: 12px;
  font-weight: bold;
  letter-spacing: 2px;
}
.hero h1 { margin: 12px 0; font-size: clamp(40px, 7vw, 64px); }
.hero p { line-height: 1.6; }
.dashboard-grid {
  display: grid;
  grid-template-columns: 1fr 1.4fr;
  gap: 24px;
}
.panel {
  min-width: 0;
  padding: 28px;
  border: 1px solid var(--border);
  border-radius: 20px;
  background: var(--surface);
  box-shadow: 0 8px 24px #172b3a08;
}
h2 { margin-top: 0; font-size: 22px; }
.summary {
  display: grid;
  grid-template-columns: 1fr 1fr;
  align-items: center;
  gap: 24px;
  margin-bottom: 24px;
}
.summary p { color: var(--muted); }
label { display: block; margin-bottom: 8px; font-weight: bold; }
form input {
  width: 100%;
  padding: 12px;
  margin-bottom: 20px;
  border: 1px solid var(--border);
  border-radius: 8px;
  background: #fafcfd;
  color: var(--text);
}
button {
  padding: 12px 16px;
  border: 0;
  border-radius: 8px;
  cursor: pointer;
  transition: background-color 0.2s ease;
}
.primary-button {
  width: 100%; background: var(--brand); color: white;
}
.primary-button:hover { background: var(--brand-dark); }
.secondary-button {
  margin-top: 20px; background: rgb(236 231 253 / 0.71); color: var(--text);
}
.secondary-button:hover { background: rgb(236 231 253); }
button:disabled { opacity: 0.5; cursor: not-allowed; }
.exercise-list {
  display: grid;
  gap: 12px;
  padding: 0;
  margin: 0;
  list-style: none;
}
.exercise {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 16px;
  padding: 16px;
  border: 1px solid var(--border);
  border-radius: 12px;
}
.exercise-details {
  display: flex;
  align-items: center;
  gap: 12px;
  min-width: 0;
  margin: 0;
  cursor: pointer;
}
.exercise-details input {
  width: 20px;
  height: 20px;
  flex-shrink: 0;
  accent-color: var(--brand);
}
.exercise strong { display: block; overflow-wrap: anywhere; }
.exercise small {
  display: block;
  margin-top: 6px;
  color: var(--muted);
  font-weight: normal;
}
.completed { background: rgb(236 231 253 / 0.71); border-color: #ded7fd; }
.completed strong { text-decoration: line-through; }
.remove-button {
  flex-shrink: 0;
  padding: 8px;
  background: #fff0ee;
  color: #9f291d;
}
.remove-button:hover { background: #ffddd7; }
progress { width: 100%; height: 20px; accent-color: var(--brand); }
.empty-state {
  padding: 24px;
  border: 2px dashed var(--border);
  border-radius: 12px;
  color: var(--muted);
  text-align: center;
}
.error-message { color: #a1261b; line-height: 1.5; }
:focus-visible { outline: 3px solid #b55000; outline-offset: 3px; }
@media (max-width: 700px) {
  .dashboard-grid, .summary { grid-template-columns: 1fr; }
  .hero, .panel { padding: 24px; }
  .exercise { flex-wrap: wrap; }
}
</style>

