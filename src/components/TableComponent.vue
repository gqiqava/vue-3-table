<template>
  <table class="table table-bordered containe-fluid border" style="border-spacing: 2">
    <tr>
      <th v-for="(singleData, ind) in tableHeaders" :key="ind">
        {{ singleData }}
        <button type="button" @click="sortData(singleData)" class="btn btn-light ms-1 p-1 fw-bold">
          ^
        </button>
        <div v-if="typeof compTable[0][singleData] === 'object'">
          <p v-for="dec in Object.entries(compTable[0][singleData])">
            <span
              @click="showParam(dec[0], singleData)"
              :class="{ activeFilter: filteredCol === dec[0] }"
              >{{ dec[0] }}</span
            >
          </p>
        </div>
        <!-- Min Max Values? -->
        <!-- <div v-if="parseFloat(compTable[0][singleData])">
          <input
            type="text"
            class="form-control"
            style="border: none"
            placeholder="min"

            @input="minMax(singleData)"
          />
          <input type="text" class="form-control" style="border: none" placeholder="max" />
        </div> -->
      </th>
    </tr>
    <tr class="mb-5">
      <th class="p-1" v-for="(singleData, ind) in tableHeaders" :key="ind">
        <InputComponent :field="singleData" @some-event="filterArr"></InputComponent>
      </th>
    </tr>
    <tr v-for="(item, ind) in userTable" :key="ind" :class="{ darker: ind % 2 === 0 }">
      <td class="" v-for="conc in item">
        <span v-if="typeof conc === 'object'">{{
          // JSON.stringify(conc).replace(/[\{\}"]+/g, ' ')
          conc[filteredCol] ? conc[filteredCol] : JSON.stringify(conc).replace(/[\{\}"]+/g, ' ')
        }}</span>
        <span v-else>{{ conc }} </span>
      </td>
    </tr>
    <tr class="mb-5">
      <th class="p-1" v-for="(singleData, ind) in tableHeaders" :key="ind">
        <NewObject :field="singleData" @add-event="addToArr"></NewObject>
      </th>
    </tr>
  </table>
  <button class="btn btn-primary fw-bold" @click="userTable.push(newObj)">Add data</button>

  <button class="btn btn-success float-end fw-bold" @click="generateCSV()">Download XLSX</button>
</template>

<script setup>
import { ref, watch } from 'vue'
import InputComponent from './InputComponent.vue'
import NewObject from './NewObject.vue'

let csvString = ref()

let inputArr = ref([])
let newObj = ref({})
let filteredCol = ref({})

const props = defineProps({
  dataTable: Array
})

let compTable = ref(props.dataTable)
let userTable = ref(props.dataTable)
let tableHeaders = Object.keys(compTable.value[0])

const filterArr = (val) => {
  console.log(val.userInput)
  console.log(val.field)

  if (typeof userTable.value[0][val.field] !== 'string') {
    userTable.value = compTable.value.filter((e) =>
      JSON.stringify(e[val.field]).includes(val.userInput)
    )
  } else {
    userTable.value = compTable.value.filter((e) => e[val.field].includes(val.userInput))
  }
}

const addToArr = (val) => {
  newObj.value[val.field] = val.userInput
}

const showParam = (val) => {
  if (filteredCol.value === val) {
    filteredCol.value = null
  } else {
    filteredCol.value = val
  }
}

// temp check

watch(
  newObj,
  () => {
    console.log(newObj)
  },
  { deep: true }
)

// Sorts

const sortData = (val) => {
  // console.log(val)
  userTable.value.sort((a, b) => (a[val] > b[val] ? 1 : b[val] > a[val] ? -1 : 0))
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

<style scoped>
.activeFilter {
  background-color: rgb(167, 216, 219);
  border-radius: 10px;
  padding: 5px;
}
</style>
