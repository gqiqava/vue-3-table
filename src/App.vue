<template>
  <div class="m-5" style="font-family: 'Roboto Mono', monospace">
    <TableComponent :data-table="users" :config="conf" :numbers="['id']" :theme="'#a6d3fb'" />
  </div>
</template>
<script setup>
import { ref } from 'vue'
import TableComponent from './components/TableComponent.vue'

let users = ref()
let locals = ref('en')

const conf = {
  id: { name: 'id', from: 'id', value: 'id' },
  status: { name: 'status', from: 'status', value: `status.localization[${locals.value}]` },
  building: {
    name: 'building',
    from: 'building',
    value: `building.status.localization[${locals.value}]`
  },
  profile: { name: 'profile one', from: 'profile', value: 'profile.properties[0].value' },
  profile2: { name: 'profile two', from: 'profile', value: 'profile.properties[2].value' }
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
            localization{
              en
              ka
            }
            name
          }
          building {
            id
            status {
              localization{
                en
                ka
              }
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
