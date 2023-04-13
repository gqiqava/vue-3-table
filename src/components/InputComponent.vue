<template>
  <input
    v-if="inCase === 1"
    type="text"
    class="form-control"
    style="border: none"
    :placeholder="`filter ${field}`"
    v-model="userInput"
    @input="$emit('someEvent', { userInput, field })"
  />
  <div class="container" v-else>
    <div class="row">
      <input
        type="text"
        class="form-control col"
        style="border: none; border-radius: 0px"
        placeholder="from"
        v-model="minVal"
        @input="$emit('filterMin', { minVal, maxVal, field })"
      />
      <input
        type="text"
        class="form-control col"
        style="border: none; border-radius: 0px"
        placeholder="to"
        v-model="maxVal"
        @input="$emit('filterMin', { minVal, maxVal, field })"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'

let userInput = ref('')
let minVal = ref()
let maxVal = ref()

let field = ref(props.field)
const inCase = ref(props.inCase)

const props = defineProps({
  field: String,
  inCase: Number
})

// watch(userInput, () => {
//   console.log(userInput.value)
// })
</script>

<style scoped></style>
