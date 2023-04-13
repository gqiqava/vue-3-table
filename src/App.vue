<template>
  <div class="m-5" style="font-family: 'Roboto Mono', monospace">
    <TableComponent :data-table="users" :config="conf" :numbers="['id']" />
  </div>
</template>
<script setup>
import { ref } from 'vue'
import TableComponent from './components/TableComponent.vue'

let users = ref()

const conf = {
  id: 'id',
  status: 'status.name',
  building: 'building.status.name',
  profile: 'profile.properties[0].value'
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
      console.log(res.data.apartments)
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
