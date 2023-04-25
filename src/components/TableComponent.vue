<template>
  <div
    ref="target"
    v-show="showContMenu"
    class="cntMenu"
    :style="{
      position: 'absolute',
      left: `${divX}px`,
      top: `${divY}px`
    }"
  >
    <ul class="px-0" style="list-style-type: none">
      <li @click="addToFixed">
        <span v-if="fixedRows.includes(curretRow)">Unlock row</span>
        <span v-else>Lock row</span>
      </li>
      <li @click="removeRow">Remove row</li>
      <li>option one</li>
      <li>option one</li>
    </ul>
  </div>

  <div class="tableResp">
    <table class="table table-striped table-bordered container-fluid" style="z-index: 0">
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
      <tr class="shadow-sm sticky-top" :style="{ backgroundColor: props.theme }">
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
              v-if="typeof parsedArray[0][singleData.from] !== 'object'"
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
            :field="singleData.from"
            :in-case="1"
            @some-event="filterArr"
          ></InputComponent>
        </th>
      </tr>
      <tbody>
        <tr
          v-for="(item, ind) in parsedArray"
          :key="ind"
          :class="{ stickyRow: fixedRows.includes(ind) }"
          :style="{
            top: `${fixedRows.indexOf(ind) * 41 + 88}px`,
            backgroundColor: fixedRows.includes(ind) ? props.theme : '#ffffff',
            zIndex: '1'
          }"
          @contextmenu.prevent="handler(ind)"
        >
          <td class="" v-for="(column, index) in item" :key="index" style="min-width: 10vw">
            {{ column }}
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
import { onClickOutside } from '@vueuse/core'

const target = ref(null)

onClickOutside(target, () => (showContMenu.value = false))

let hideCol = ref([])
let showSettings = ref([])
let divX = ref(30)
let divY = ref(200)
let showContMenu = ref(false)
let curretRow = ref()
let parsedArray = ref([])

let undoObj = ref()
const undoHandler = () => {
  parsedArray.value.splice(curretRow.value, 0, undoObj.value)
}

const keyupHandler = (event) => {
  if (event.ctrlKey && event.key === 'z') {
    undoHandler()
  }
}

document.addEventListener('keyup', keyupHandler)

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
  },
  theme: {
    type: String,
    default: '#f5fbff'
  }
})

const origin = [...props.dataTable]

let compTable = ref(props.dataTable)
let userTable = ref(props.dataTable)
let recNumbers = ref(props.numbers)
let tableHeaders = ref(props.config)

// Fixed row

let fixedRows = ref([])

const addToFixed = () => {
  if (fixedRows.value.includes(curretRow.value)) {
    fixedRows.value = fixedRows.value.filter((item) => item !== curretRow.value)
  } else {
    fixedRows.value.push(curretRow.value)
  }

  showContMenu.value = false
}

const removeRow = () => {
  undoObj.value = parsedArray.value.splice(curretRow.value, 1)[0]
  showContMenu.value = false
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

const handler = (ind) => {
  curretRow.value = ind
  divX.value = event.clientX + 15
  divY.value = event.clientY
  showContMenu.value = true
}

// Sorts

// sort how to do with config headers

let filterActive = ref()

const sortData = (val, val2) => {
  if (filterActive.value !== val2) {
    filterActive.value = val2
    if (recNumbers.value.includes(val)) {
      parsedArray.value.sort((a, b) => a[val] - b[val])
    } else {
      parsedArray.value.sort((a, b) => (a[val] > b[val] ? 1 : b[val] > a[val] ? -1 : 0))
    }
  } else {
    filterActive.value = null
    // parsedArray.value = origin
  }
}

// Generate XLSX

let csvString = ref()

const generateCSV = () => {
  csvString.value = [
    Object.keys(parsedArray.value[0]),
    ...parsedArray.value.map((item) =>
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

const parseArray = () => {
  for (let i = 0; i < compTable.value.length; i++) {
    let exObj = {}
    for (const item in tableHeaders.value) {
      exObj[item] = filterCol(compTable.value[i], tableHeaders.value[item].value).toString()
    }
    parsedArray.value.push(exObj)
  }
}

parseArray()

let originalArray = parsedArray.value
const filterArr = (val) => {
  if (typeof originalArray[0][val.field] !== 'string') {
    console.log(1, val.field)
    console.log(2, originalArray[0])
    parsedArray.value = originalArray.filter((e) => {
      JSON.stringify(e[val.field]).includes(val.userInput)
      console.log(JSON.stringify(e[val.field]).toLowerCase().includes(val.userInput.toLowerCase()))
    })
  } else {
    parsedArray.value = originalArray.filter((e) =>
      e[val.field].toLowerCase().includes(val.userInput.toLowerCase())
    )
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
  padding: 5px;*/
  margin-right: 5px;
  cursor: pointer;
}

.stickyRow {
  position: sticky;
}

.cntMenu {
  border: 1px solid grey;
  background-color: #303031;
  padding-top: 10px;
  padding-bottom: 10px;
  color: white;
  border-radius: 16px;
  z-index: 10;
}

.cntMenu ul li:hover {
  background-color: #636363;
}
.cntMenu ul li {
  padding: 3px 13px 3px 13px;
}
</style>
