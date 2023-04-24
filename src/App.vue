<template>
  <div class="m-5" style="font-family: 'Roboto Mono', monospace">
    <TableComponent :data-table="users" :config="conf" :numbers="['id']" :theme="'#fc9796'" />
  </div>
</template>
<script setup>
import { ref } from 'vue'
import TableComponent from './components/TableComponent.vue'

let users = ref()

const conf = {
  id: { name: 'id', from: 'id', value: 'id' },
  status: { name: 'status', from: 'status', value: 'status.name' },
  building: { name: 'building', from: 'building', value: 'building.status.name' },
  profile: { name: 'profile one', from: 'profile', value: 'profile.properties[3].value' },
  profile2: { name: 'profile two', from: 'profile', value: 'profile.properties[1].value' }
}

const fetchCHeck = () => {
  fetch('https://roomswaregraphql.zuniac.com/graphql', {
    method: 'POST',

    headers: {
      'Content-Type': 'application/json'
    },

    body: JSON.stringify({
      query: `{
        apartments {
          id
          status {
            name
          }
          building {
            id
            status {
              name
            }
            state {
              name
            }
          }
          profile {
            id
            properties {
              name
              value
            }
          }
        }
      }`
    })
  })
    .then((res) => res.json())
    .then((res) => {
      users.value = res.data.apartments
    })
}

fetchCHeck()
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Roboto+Mono:wght@300;400;500;700&display=swap');

body {
  font-family: 'Roboto Mono', monospace;
}
</style>
