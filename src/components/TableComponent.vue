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
            hideCol.includes(singleData.name)
              ? { visibility: 'collapse' }
              : { visibility: 'visible' }
          ]"
        />
      </colgroup>
      <tr class="shadow-sm sticky-top" style="background-color: #f5fbff">
        <th
          v-for="(singleData, ind) in tableHeaders"
          :key="ind"
          class="p-2"
          @mouseenter="showSettings.push(singleData.name)"
          @mouseleave="showSettings = showSettings.filter((e) => e !== singleData.name)"
        >
          <span
            v-if="!showSettings.includes(singleData.name)"
            style="text-transform: capitalize; cursor: pointer"
            >{{ singleData.name }}
          </span>
          <span v-else>
            <span
              style="color: grey; font-size: 12px; text-transform: capitalize; margin-right: 5px"
            >
              {{ singleData.name.slice(0, 14) }}:
            </span>
            <img
              src="@/assets/hidden.png"
              alt="hidden"
              style="width: 20px; margin-right: 5px; cursor: pointer"
              @click="hideCol.push(singleData.name)"
            />
            <span
              class="settingsCol"
              v-if="typeof compTable[0][singleData.from] !== 'object'"
              @click="sortData(singleData.from, ind)"
            >
              <img
                v-if="filterActive === ind"
                src="@/assets/filterActive.png"
                style="width: 20px"
              />
              <img v-else src="@/assets/filter.png" style="width: 20px" />
            </span>
          </span>
        </th>
      </tr>
      <tr>
        <th
          class="p-1 sticky-top"
          style="top: 40px; background: #ffffff"
          v-for="(singleData, ind) in tableHeaders"
          :key="ind"
        >
          <InputComponent
            :field="singleData.name"
            :in-case="1"
            @some-event="filterArr"
          ></InputComponent>
        </th>
      </tr>
      <tbody>
        <tr
          v-for="(item, ind) in userTable"
          :key="ind"
          :class="{ stickyRow: fixedRows.includes(ind) }"
          :style="{ top: `${fixedRows.indexOf(ind) * 41 + 97}px` }"
          @dblclick="addToFixed(ind)"
        >
          <td class="" v-for="(conc, index) in config" :key="index" style="min-width: 10vw">
            {{ filterCol(item, conc.value).toString() }}
          </td>
        </tr>
      </tbody>
      <!-- <tr class="mb-5">
        <th class="p-1" v-for="(singleData, ind) in tableHeaders" :key="ind">
          <NewObject :field="singleData.name" @add-event="addToArr"></NewObject>
        </th>
      </tr> -->
    </table>
  </div>

  <!-- <button class="btn btn-primary fw-bold" @click="userTable.push(newObj)">Add data</button> -->

  <button class="btn btn-success float-end fw-bold" @click="generateCSV()">Download XLSX</button>
</template>

<script setup>
import { ref } from 'vue'
import InputComponent from './InputComponent.vue'
// import NewObject from './NewObject.vue'

let hideCol = ref([])
let showSettings = ref([])

const props = defineProps({
  dataTable: {
    type: Array,
    required: true
  },
  config: {
    type: Object,
    required: true
  },
  numbers: {
    type: Array
  }
})

const origin = [...props.dataTable]

console.log(props.config)

let compTable = ref(props.dataTable)
let userTable = ref(props.dataTable)
let recNumbers = ref(props.numbers)
let tableHeaders = ref(props.config)
let parsedArray = ref([])

const parseArray = () => {
  for (let i = 0; i < compTable.value.length; i++) {
    console.log(compTable.value[i])
  }
}

parseArray()

// Fixed row

let fixedRows = ref([])

const addToFixed = (val) => {
  if (fixedRows.value.includes(val)) {
    fixedRows.value = fixedRows.value.filter((item) => item !== val)
  } else {
    fixedRows.value.push(val)
  }
}

// Range

const filterMinAr = (val) => {
  console.log(val.minVal, val)
  userTable.value = compTable.value.filter(
    (e) =>
      parseFloat(e[val.field]) >= (val.minVal ? val.minVal : -Infinity) &&
      parseFloat(e[val.field]) <= (val.maxVal ? val.maxVal : Infinity)
  )
}

// Sorts

// sort how to do with config headers

let filterActive = ref()

const sortData = (val, val2) => {
  if (filterActive.value !== val2) {
    filterActive.value = val2
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

let csvString = ref()

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

// FIlters

const filterCol = (from, ...selectors) =>
  [...selectors].map((s) =>
    s
      .replace(/\[([^\[\]]*)\]/g, '.$1.')
      .split('.')
      .filter((t) => t !== '')
      .reduce((prev, cur) => prev && prev[cur], from)
  )

const filterArr = (val) => {
  if (typeof compTable.value[0][val.field] !== 'string') {
    userTable.value = compTable.value.filter((e) => {
      JSON.stringify(e[val.field]).includes(val.userInput)
      console.log(JSON.stringify(e[val.field]).toLowerCase().includes(val.userInput.toLowerCase()))
    })
  } else {
    userTable.value = compTable.value.filter((e) =>
      e[val.field].toLowerCase().includes(val.userInput.toLowerCase())
    )
    console.log(1)
  }
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

.settingsCol {
  /* background-color: #ffffff;
  border: 2px dashed #5e89ff;
  padding: 5px;
  border-radius: 10px; */
  margin-right: 5px;
  cursor: pointer;
}

.stickyRow {
  position: sticky;
  box-shadow: inset 0em -0.15em #acdadd;
  background: #ffffff;
}
</style>
