<template>
  <table class="table table-bordered container mt-5 border" style="border-spacing: 2">
    <tr>
      <!-- <th>
        მომხმარებელი
        <button type="button" @click="sortDataByName" class="btn btn-light ms-1 p-1 fw-bold">
          ^
        </button>
      </th>
      <th>
        ნომერი
        <button type="button" @click="sortDataByNumber" class="btn btn-light ms-1 p-1 fw-bold">
          ^
        </button>
      </th>
      <th>
        შეკვეთა
        <button type="button" @click="sortDataByOrder" class="btn btn-light ms-1 p-1 fw-bold">
          ^
        </button>
      </th>
      <th>
        ფასი
        <button type="button" @click="sortDataByPrice" class="btn btn-light ms-1 p-1 fw-bold">
          ^
        </button>
      </th>
      <th>დეტალები</th>
      <th>
        დრო
        <button type="button" @click="sortDataByTime" class="btn btn-light ms-1 p-1 fw-bold">
          ^
        </button>
      </th> -->
      <th v-for="(singleData, ind) in tableHeaders" :key="ind">
        {{ singleData }}
        <button type="button" @click="sortDataByName" class="btn btn-light ms-1 p-1 fw-bold">
          ^
        </button>
      </th>
    </tr>
    <tr class="mb-5">
      <th class="p-1">
        <input
          type="text"
          class="form-control"
          v-model="name"
          placeholder="სახელი"
          style="border: none"
        />
      </th>
      <th>
        <input
          type="text"
          class="form-control"
          v-model="number"
          placeholder="ნომერი"
          style="border: none"
        />
      </th>
      <th>
        <input
          type="text"
          class="form-control"
          v-model="order"
          placeholder="შეკვეთა"
          style="border: none"
        />
      </th>
      <th>
        <input
          type="text"
          class="form-control"
          v-model="price"
          placeholder="ფასი"
          style="border: none"
        />
      </th>
      <th>
        <input
          type="text"
          class="form-control"
          v-model="details"
          placeholder="დეტალები"
          style="border: none"
        />
      </th>
      <th>
        <input
          type="text"
          class="form-control"
          v-model="time"
          placeholder="დრო"
          style="border: none"
        />
      </th>
    </tr>
    <tr v-for="(item, ind) in userTable" :key="ind" :class="{ darker: ind % 2 === 0 }">
      <td class="" v-for="conc in item">{{ conc }}</td>
    </tr>
  </table>
  <button class="btn btn-success fw-bold ms-5" @click="generateCSV()">Download XLSX</button>
</template>

<script setup>
import { ref, watch } from 'vue'

let csvString = ref()

let name = ref('')
let number = ref('')
let order = ref('')
let price = ref('')
let details = ref('')
let time = ref('')

let inputArr = ref([name, number, order, price, details, time])

const props = defineProps({
  dataTable: Array
})

let compTable = ref(props.dataTable)
let userTable = ref(props.dataTable)
let tableHeaders = Object.keys(userTable.value[0])

watch(
  inputArr,
  () => {
    userTable.value = compTable.value.filter(
      (e) =>
        e.name.includes(name.value) &&
        e.number.includes(number.value) &&
        e.order.includes(order.value) &&
        e.price.toString().includes(price.value) &&
        e.time.includes(time.value)
    )
  },
  { deep: true }
)

// Sorts

const sortDataByName = () => {
  userTable.value.sort((a, b) => (a.name > b.name ? 1 : b.name > a.name ? -1 : 0))
}

const sortDataByTime = () => {
  userTable.value.sort((a, b) => (a.time > b.time ? 1 : b.time > a.time ? -1 : 0))
}

const sortDataByOrder = () => {
  userTable.value.sort((a, b) => (a.order > b.order ? 1 : b.order > a.order ? -1 : 0))
}

const sortDataByPrice = () => {
  userTable.value.sort((a, b) => a.price - b.price)
}

const sortDataByNumber = () => {
  userTable.value.sort((a, b) => a.number - b.number)
}

// Generate XLSX

const generateCSV = () => {
  csvString.value = [
    ['Name', 'Number', 'Time', 'Order', 'Price', 'Category', 'Sauce', 'Double meat'],
    ...userTable.value.map((item) => [
      item.name,
      item.number,
      item.time,
      item.order,
      item.price,
      item.category.name,
      item.details.sauce,
      item.details.double_meat
    ])
  ]
    .map((e) => e.join(','))
    .join('\n')

  var hiddenElement = document.createElement('a')
  hiddenElement.href = 'data:text/csv;charset=utf-8,' + encodeURI(csvString.value)
  hiddenElement.target = '_blank'
  hiddenElement.download = 'data.csv'
  hiddenElement.click()
}
</script>

<style scoped></style>
