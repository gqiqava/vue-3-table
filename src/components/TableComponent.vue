<template>
  <div class="tableResp">
    <table class="table table-striped table-bordered container-fluid border">
      <caption style="caption-side: top" v-if="hideCol.length > 0">
        <img src="@/assets/hidden.png" alt="hidden" style="width: 20px; margin-right: 5px" />
        <span
          v-for="item in hideCol"
          :key="item"
          @click="hideCol.splice(hideCol.indexOf(item), 1)"
          class="hiddenCol"
        >
          {{ item }}
        </span>
        <small>Click on a desired pill to restore</small>
      </caption>
      <colgroup>
        <col
          span="1"
          v-for="(singleData, ind) in tableHeaders"
          :key="ind"
          class="ps-2"
          :style="[
            hideCol.includes(singleData) ? { visibility: 'collapse' } : { visibility: 'visible' }
          ]"
        />
      </colgroup>
      <tr class="shadow-sm sticky-top" style="background-color: #f5fbff">
        <th v-for="(singleData, ind) in tableHeaders" :key="ind" class="p-2">
          <span
            @click="hideCol.push(singleData)"
            style="text-transform: capitalize; cursor: pointer"
            >{{ singleData.slice(0, 14) }}<span v-if="singleData.length > 14">..</span>
          </span>
          <button
            type="button"
            @click="sortData(singleData, ind)"
            class="btn btn-light p-1 fw-bold border-0"
            v-if="typeof compTable[0][singleData] !== 'object'"
          >
            <img v-if="filterActive === ind" src="@/assets/filterActive.png" style="width: 22px" />
            <img v-else src="@/assets/filter.png" style="width: 22px" />
          </button>
        </th>
      </tr>
      <tr
        class="shadow-sm sticky-top mt-5"
        :style="{ top: `50px` }"
        style="background-color: #f5fbff"
      >
        <th v-for="(singleData, ind) in tableHeaders" :key="ind" class="p-2">
          <InputComponent
            v-if="recNumbers?.includes(singleData)"
            :field="singleData"
            :in-case="2"
            @filter-min="filterMinAr"
          ></InputComponent>
          <div v-if="typeof compTable[0][singleData] === 'object'">
            <span v-for="dec in Object.entries(compTable[0][singleData])">
              <span
                @click="showParam(dec[0], singleData)"
                :class="{ activeFilter: filteredCol === dec[0] }"
                class="subCat"
                >{{ dec[0] }}</span
              >
            </span>
          </div>
        </th>
      </tr>
      <tr>
        <th class="p-1" v-for="(singleData, ind) in tableHeaders" :key="ind">
          <InputComponent :field="singleData" :in-case="1" @some-event="filterArr"></InputComponent>
        </th>
      </tr>
      <tbody>
        <tr
          v-for="(item, ind) in userTable"
          :key="ind"
          :class="{ stickyRow: fixedRows.includes(ind) }"
          :style="{ top: `${fixedRows.indexOf(ind) * 65 + 115}px` }"
          @dblclick="addToFixed(ind)"
        >
          <td class="" v-for="(conc, i) in item" :key="i" style="min-width: 10vw">
            <span v-if="typeof conc === 'object'">{{
              // JSON.stringify(conc).replace(/[\{\}"]+/g, ' ')
              conc[filteredCol] ? conc[filteredCol] : JSON.stringify(conc).replace(/[\{\}"]+/g, ' ')
            }}</span>
            <span v-else>{{ conc }} </span>
          </td>
        </tr>
      </tbody>
      <tr class="mb-5">
        <th class="p-1" v-for="(singleData, ind) in tableHeaders" :key="ind">
          <NewObject :field="singleData" @add-event="addToArr"></NewObject>
        </th>
      </tr>
    </table>
  </div>

  <button class="btn btn-primary fw-bold" @click="userTable.push(newObj)">Add data</button>

  <button class="btn btn-success float-end fw-bold" @click="generateCSV()">Download XLSX</button>
</template>

<script setup>
import { ref, watch } from 'vue'
import InputComponent from './InputComponent.vue'
import NewObject from './NewObject.vue'

let csvString = ref()

let inputArr = ref({})
let newObj = ref({})
let filteredCol = ref({})
let filterActive = ref()
let hideCol = ref([])
let fixedRows = ref([])

const props = defineProps({
  dataTable: {
    type: Array,
    required: true
  },
  numbers: {
    type: Array
  }
})

const origin = [...props.dataTable]

let compTable = ref(props.dataTable)
let userTable = ref(props.dataTable)
let recNumbers = ref(props.numbers)
let tableHeaders = Object.keys(compTable.value[0])

const filterArr = (val) => {
  // inputArr.value[val.field] = val.userInput

  // for (const item in inputArr.value) {
  //   console.log(item, inputArr.value[item])
  //   userTable.value = compTable.value.filter((e) =>
  //     e[item].toLowerCase().includes(inputArr.value[item].toLowerCase())
  //   )
  // }

  if (typeof userTable.value[0][val.field] !== 'string') {
    userTable.value = compTable.value.filter((e) =>
      JSON.stringify(e[val.field]).includes(val.userInput)
    )
  } else {
    userTable.value = compTable.value.filter((e) =>
      e[val.field].toLowerCase().includes(val.userInput.toLowerCase())
    )
  }
}

const addToArr = (val) => {
  newObj.value[val.field] = val.userInput
}

// const hideCol = (val) => {
//   console.log(val)
//   delete thisIsObject[val]
// }

const addToFixed = (val) => {
  if (fixedRows.value.includes(val)) {
    fixedRows.value = fixedRows.value.filter((item) => item !== val)
  } else {
    fixedRows.value.push(val)
  }
}

const showParam = (val) => {
  if (filteredCol.value === val) {
    filteredCol.value = null
  } else {
    filteredCol.value = val
  }
}

const filterMinAr = (val) => {
  console.log(val.minVal, val)
  userTable.value = compTable.value.filter(
    (e) =>
      parseFloat(e[val.field]) >= (val.minVal ? val.minVal : -Infinity) &&
      parseFloat(e[val.field]) <= (val.maxVal ? val.maxVal : Infinity)
  )
}

// Sorts

const sortData = (val, val2) => {
  if (filterActive.value !== val2) {
    filterActive.value = val2
    console.log(1)
    if (recNumbers.value.includes(val)) {
      userTable.value.sort((a, b) => a[val] - b[val])
    } else {
      userTable.value.sort((a, b) => (a[val] > b[val] ? 1 : b[val] > a[val] ? -1 : 0))
    }
  } else {
    filterActive.value = null
    userTable.value = origin
  }
}

// Generate XLSX
const generateCSV = () => {
  csvString.value = [
    Object.keys(userTable.value[0]),
    ...userTable.value.map((item) =>
      Object.values(item).map((e) =>
        typeof e === 'object'
          ? JSON.stringify(e).replace(/[\{\}\,"]+/g, ' ')
          : JSON.stringify(e).replace(',', ' ')
      )
    )
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
.tableResp {
  overflow: auto;
  height: 100vh;
}
.hiddenCol {
  background-color: #ffcccb;
  border: 2px dashed #fc9796;
  padding: 5px;
  border-radius: 10px;
  margin-right: 5px;
  cursor: pointer;
}
.subCat {
  background-color: #acdadd;
  padding: 5px 8px 5px 8px;
  border-radius: 10px;
  margin-right: 5px;
  cursor: pointer;
}
.activeFilter {
  background-color: #acdadd;
  border: 2px dashed #238086;
  padding: 3px 5px 3px 5px;
  border-radius: 10px;
}
.stickyRow {
  position: sticky;
  box-shadow: inset 0em -0.15em #acdadd;
  background: #ffffff;
}
</style>
