<script setup>
import { ref, onMounted, watchEffect } from 'vue'
import todo from './todo.vue'
defineProps({
  msg: {
    type: String,
    required: true
  }
})
const sampleText = ref('')
const loading = ref(false)
const todos = ref([])
const titleText = ref('')
const descriptionText = ref('')
watchEffect(() => console.log('title', titleText.value))

const updateText = (newValue) => (sampleText.value = newValue)
import { supabase } from '@/supabase'

const fetchToDos = async () => {
  loading.value = true
  const { data, error } = await supabase.from('todo').select()
  loading.value = false
  console.log('data', data)
  if (!error) return (todos.value = data)
  console.log('error', error)
}

const addToDo = async () => {
  console.log('todo title', titleText.value)
  console.log('todo desc', descriptionText.value)
  loading.value = true
  const { data, error } = await supabase
    .from('todo')
    .insert({
      id: todos.value.length + 1,
      title: titleText.value,
      description: descriptionText.value
    })
    .select()
  loading.value = false
  console.log('data', data[0])
  if (!error) {
    titleText.value = ''
    descriptionText.value = ''
    todos.value = [...todos.value, data[0]]
  }
}

onMounted(async () => {
  await fetchToDos()
})
</script>

<template>
  <h1>{{ loading ? 'LOADING...' : 'hello!' }}</h1>
  <div class="greetings">
    <h1 class="green">{{ msg }}</h1>
    <h3>
      You’ve successfully created a project with
      <a href="https://vitejs.dev/" target="_blank" rel="noopener">Vite</a> +
      <a href="https://vuejs.org/" target="_blank" rel="noopener">Vue 3</a>.
      <input :value="sampleText" @input="(e) => updateText(e.target.value)" />
      {{ 'sample text' + sampleText }}
      <div>
        <label>
          title
          <input v-model="titleText" />
        </label>
      </div>
      <div>
        <label>
          description
          <input v-model="descriptionText" />
        </label>
      </div>
      <button @click="addToDo" :disabled="loading">add to do</button>
      <div v-for="{ id, title, description } in todos">
        <todo :id="id" :title="title" :description="description" />
      </div>
    </h3>
  </div>
</template>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style>
