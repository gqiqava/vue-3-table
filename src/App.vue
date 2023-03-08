<template>
  <div class="m-5">
    <TableComponent :data-table="users" />
  </div>
</template>
<script setup>
import { ref } from 'vue'
import TableComponent from './components/TableComponent.vue'

let users = ref()

fetch('https://dummyjson.com/products')
  .then((res) => res.json())
  .then((res) => (users.value = res.products))

let inMeal = ref([
  {
    name: 'გიორგი',
    order: 'ქათმის ატრია',
    price: 15,
    category: { name: 'ატრია', id: 1 },
    details: { sauce: 'ტკბილ-ცხარე', double_meat: true },
    number: '599568686',
    time: '11:15',
    comment: 'სტაფილოს გარეშე რაა',
    id: '1'
  },
  {
    name: 'დავით',
    order: 'საქონლის ატრია',
    price: 12.55,
    category: { name: 'ატრია', id: 1 },
    details: { sauce: 'ტკბილ-მჟავე', double_meat: false },
    number: '599500686',
    time: '15:15',
    comment: 'სტაფილოს გარეშე რაა',
    id: '2'
  },
  {
    name: 'ბექა',
    order: 'ქათმის ატრია',
    price: 26,
    category: { name: 'ატრია', id: 1 },
    details: { sauce: 'ტკბილ-მჟავე', double_meat: true },
    number: '599565586',
    time: '20:15',
    comment: 'სტაფილოს გარეშე რაა',
    id: '3'
  },
  {
    name: 'ბექა',
    order: 'ქათმის ატრია',
    price: 26,
    category: { name: 'ატრია', id: 1 },
    details: { sauce: 'ტკბილ-მჟავე', double_meat: true },
    number: '599565586',
    time: '20:15',
    comment: 'სტაფილოს გარეშე რაა',
    id: '366'
  },
  {
    name: 'ბექა',
    order: 'ქათმის ატრია',
    price: 26,
    category: { name: 'ატრია', id: 1 },
    details: { sauce: 'ტკბილ-მჟავე', double_meat: true },
    number: '599565586',
    time: '20:15',
    comment: 'სტაფილოს გარეშე რაა',
    id: '312'
  },
  {
    name: 'ბექა',
    order: 'ქათმის ატრია',
    price: 26,
    category: { name: 'ატრია', id: 1 },
    details: { sauce: 'ტკბილ-მჟავე', double_meat: true },
    number: '599565586',
    time: '20:15',
    comment: 'სტაფილოს გარეშე რაა',
    id: '123'
  },
  {
    name: 'ბექა',
    order: 'ქათმის ატრია',
    price: 26,
    category: { name: 'ატრია', id: 1 },
    details: { sauce: 'ტკბილ-მჟავე', double_meat: true },
    number: '599565586',
    time: '20:15',
    comment: 'სტაფილოს გარეშე რაა',
    id: '369'
  },
  {
    name: 'ლაზარე',
    order: 'სქონლის ატრია',
    price: 32,
    category: { name: 'ბრინჯი', id: 2 },
    details: { sauce: 'ტკბილ-მჟავე', double_meat: false },
    number: '599568600',
    time: '15:19',
    comment: 'სტაფილოს გარეშე რაა',
    id: '4'
  },
  {
    name: 'ლაზარე',
    order: 'სქონლის ატრია',
    price: 33,
    category: { name: 'ბრინჯი', id: 2 },
    details: { sauce: 'ტკბილ-მჟავე', double_meat: false },
    number: '599568600',
    time: '15:19',
    comment: 'სტაფილოს გარეშე რაა',
    id: '4'
  },
  {
    name: 'გიორგი',
    order: 'ქათმის ბრინჯი',
    price: 15,
    category: { name: 'ბრინჯი', id: 2 },
    details: { sauce: 'ტკბილ-მჟავე', double_meat: true },
    number: '599568686',
    time: '23:15',
    comment: 'სტაფილოს მეტი რაა',
    id: '42'
  },
  {
    name: 'გიორგი',
    order: 'ქათმის ატრია',
    price: 18,
    category: { name: 'ატრია', id: 1 },
    details: { sauce: 'ტკბილ-მჟავე', double_meat: false },
    number: '599568686',
    time: '17:15',
    comment: 'სტაფილოს მეტი რაა',
    id: '42'
  },
  {
    name: 'ჯემო',
    order: 'საქონლის ბრინჯი',
    price: 17,
    category: { name: 'ატრია', id: 1 },
    details: { sauce: 'ტკბილ-მჟავე', double_meat: false },
    number: '599567000',
    time: '15:15',
    comment: 'სტაფილოს გარეშე რაა',
    id: '41'
  },
  {
    name: 'გიორგი',
    order: 'ქათმის ატრია',
    price: 15,
    category: { name: 'ატრია', id: 1 },
    details: { sauce: 'ტკბილ-ცხარე', double_meat: true },
    number: '599568686',
    time: '11:15',
    comment: 'სტაფილოს გარეშე რაა',
    id: '1'
  },
  {
    name: 'თემო',
    order: 'საქონლის ატრია',
    price: 12.55,
    category: { name: 'ატრია', id: 1 },
    details: { sauce: 'ტკბილ-მჟავე', double_meat: false },
    number: '591943299',
    time: '15:15',
    comment: 'სტაფილოს გარეშე რაა',
    id: '2'
  },
  {
    name: 'ბექა',
    order: 'ქათმის ატრია',
    price: 26,
    category: { name: 'ატრია', id: 1 },
    details: { sauce: 'ტკბილ-მჟავე', double_meat: true },
    number: '599565586',
    time: '20:15',
    comment: 'სტაფილოს გარეშე რაა',
    id: '3'
  },
  {
    name: 'ლაზარე',
    order: 'სქონლის ატრია',
    price: 32,
    category: { name: 'ბრინჯი', id: 2 },
    details: { sauce: 'ტკბილ-მჟავე', double_meat: false },
    number: '599568700',
    time: '15:19',
    comment: 'სტაფილოს გარეშე რაა',
    id: '4'
  },
  {
    name: 'გიორგი',
    order: 'ქათმის ბრინჯი',
    price: 15,
    category: { name: 'ბრინჯი', id: 2 },
    details: { sauce: 'ტკბილ-მჟავე', double_meat: true },
    number: '599568686',
    time: '23:15',
    comment: 'სტაფილოს მეტი რაა',
    id: '42'
  },
  {
    name: 'გიორგი',
    order: 'ქათმის ატრია',
    price: 18,
    category: { name: 'ატრია', id: 1 },
    details: { sauce: 'ტკბილ-მჟავე', double_meat: false },
    number: '599568626',
    time: '17:15',
    comment: 'სტაფილოს მეტი რაა',
    id: '42'
  },
  {
    name: 'ჯემო',
    order: 'საქონლის ბრინჯი',
    price: 17,
    category: { name: 'ატრია', id: 1 },
    details: { sauce: 'ტკბილ-მჟავე', double_meat: false },
    number: '599567010',
    time: '15:15',
    comment: 'სტაფილოს გარეშე რაა',
    id: '41'
  }
])
</script>

<style scoped></style>
