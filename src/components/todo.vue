<script setup>
import { ref } from 'vue'
const props = defineProps({
  id: {
    type: Number,
    required: true
  },
  // add reactive state for title
  title: {
    type: String,
    required: true
  },
  description: {
    type: String,
    required: true
  }
})
const loading = ref(false)
const isEditing = ref(false)
const titleText = ref(props.title)
const descriptionText = ref(props.description)

import { supabase } from '@/supabase'

const toggleEdit = () => {
  isEditing.value = !isEditing.value
}

const updateToDo = async () => {
  console.log('todo title', titleText.value)
  console.log('todo desc', descriptionText.value)
  loading.value = true
  const { data, error } = await supabase
    .from('todo')
    .update({ title: titleText.value, description: descriptionText.value })
    .eq('id', props.id)
    .select()
  loading.value = false
  toggleEdit()
  console.log('data', data[0])
  if (!error) {
    titleText.value = ''
    descriptionText.value = ''
  }
}

const deleteToDo = async () => {
  loading.value = true
  const { data, error } = await supabase.from('todo').delete().eq('id', props.id).select()
  loading.value = false
  toggleEdit()
  console.log('data', data[0])
  if (!error) {
    titleText.value = ''
    descriptionText.value = ''
  }
}
</script>

<template>
  <div class="greetings">
    <h3>
      <div>
        <label v-if="isEditing">
          title
          <input v-model="titleText" />
        </label>
        <p v-if="!isEditing">Title: {{ title }}</p>
      </div>
      <div>
        <label v-if="isEditing">
          description
          <input v-model="descriptionText" />
        </label>
        <p v-if="!isEditing">description: {{ description }}</p>
      </div>
    </h3>
  </div>
  <button @click="toggleEdit" v-if="!isEditing">edit</button>
  <button @click="updateToDo" v-if="isEditing" :disabled="loading">done</button>
  <button @click="deleteToDo" v-if="isEditing" :disabled="loading">delete</button>
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
