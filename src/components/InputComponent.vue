<template>
  <input
    type="text"
    class="form-control"
    style="border: none"
    :placeholder="field"
    v-model="userInput"
    @input="$emit('someEvent', { userInput, field })"
  />
</template>

<script setup>
import { ref, watch } from 'vue'

let userInput = ref('')
let field = ref(props.field)

const props = defineProps({
  field: String
})

// watch(userInput, () => {
//   console.log(userInput.value)
// })
</script>

<style scoped></style>
