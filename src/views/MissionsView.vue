<script setup>
import {inject, onMounted, ref} from "vue";
import apiFetch from "@/heplers/apiFetch.js";

const missions = ref([])
const userId = inject('user_id')

onMounted(async () => {
  const result = await apiFetch('get', '/lunar-missions')
  result.forEach(m => {
    const mission = m.mission

    mission.is_open = false

    missions.value.push(mission)
  })
})

const open = mission => {
  missions.value.forEach(mission => {
    mission.is_open = false
  })

  mission.is_open = true
}
const remove = async mission => {
  await apiFetch('delete', `/lunar-missions/${mission.id}`)

  missions.value.splice(missions.value.indexOf(mission), 1)
}
</script>

<template>
  <div class="container mt-10 sm:mx-auto sm:w-full sm:max-w-2xl" v-if="missions">
    <RouterLink to="/add-mission" class="bg-sky-500 text-white py-2 px-2 rounded shadow-md hover:bg-sky-600 mb-4">
      Добавить миссию
    </RouterLink>

    <div class="px-4 mb-10 sm:px-0 bg-white shadow-xl rounded" v-for="mission in missions">
      <div class="flex justify-between w-full items-center p-6">
        <h2 class="text-base font-semibold leading-7 text-sky-600 text-xl">{{ mission.name }}</h2>
        <div class="flex gap-4">
          <button @click="open(mission)">
            <i class="fa  text-xl" :class="{'fa-angle-down': mission.is_open, 'fa-angle-left': !mission.is_open}" aria-hidden="true"></i>
          </button>
          <template v-if="userId == mission.author.id">
            <button>
              <i class="fa fa-edit text-xl" aria-hidden="true"></i>
            </button>
            <button @click="remove(mission)">
              <i class="fa fa-trash text-xl text-red-500" aria-hidden="true"></i>
            </button>
          </template>
        </div>
      </div>
      <div class="px-6" v-if="mission.is_open">
        <p class="mt-1 max-w-2xl text-sm leading-6 text-gray-500">{{ mission.launch_details.launch_date }}</p>
        <p class="mt-1 max-w-2xl text-sm leading-6 text-gray-500">{{ mission.landing_details.landing_date }}</p>
        <div class="mt-6 border-t border-gray-100">
          <dl class="divide-y divide-gray-100">
            <div class="px-4 py-6 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-0">
              <dt class="text-sm font-medium leading-6 text-gray-900">Место запуска</dt>
              <dd class="mt-1 text-sm leading-6 text-gray-700 sm:col-span-2 sm:mt-0">Космический центр имени Кеннеди</dd>
            </div>
            <div class="px-4 py-6 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-0">
              <dt class="text-sm font-medium leading-6 text-gray-900">Широта</dt>
              <dd class="mt-1 text-sm leading-6 text-gray-700 sm:col-span-2 sm:mt-0">28.5721000</dd>
            </div>
            <div class="px-4 py-6 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-0">
              <dt class="text-sm font-medium leading-6 text-gray-900">Долгота</dt>
              <dd class="mt-1 text-sm leading-6 text-gray-700 sm:col-span-2 sm:mt-0">-80.6480000</dd>
            </div>
            <div class="px-4 py-6 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-0">
              <dt class="text-sm font-medium leading-6 text-gray-900">Место посадки</dt>
              <dd class="mt-1 text-sm leading-6 text-gray-700 sm:col-span-2 sm:mt-0">Море спокойствия</dd>
            </div>
            <div class="px-4 py-6 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-0">
              <dt class="text-sm font-medium leading-6 text-gray-900">Широта</dt>
              <dd class="mt-1 text-sm leading-6 text-gray-700 sm:col-span-2 sm:mt-0">0.6740000</dd>
            </div>
            <div class="px-4 py-6 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-0">
              <dt class="text-sm font-medium leading-6 text-gray-900">Долгота</dt>
              <dd class="mt-1 text-sm leading-6 text-gray-700 sm:col-span-2 sm:mt-0">23.4720000</dd>
            </div>
          </dl>
        </div>
        <h2 class="my-6 text-xl text-sky-600">Информация о космическом корабле</h2>
        <div class="px-4 sm:px-0">
          <p class="mt-1 max-w-2xl text-sm leading-6 text-gray-500">Лунный модуль: </p>
          <p class="mt-1 max-w-2xl text-sm leading-6 text-gray-500">Управляющий модуль:</p>
        </div>
        <div class="px-4 py-6 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-0">
          <ul>
            <li class="flex justify-between gap-x-6 py-5" v-for="crew in mission.spacecraft.crew">
              <div class="flex gap-x-4">
                <p class="text-sm font-semibold text-gray-900">{{ crew.name }}</p>
                <p class="mt-1 text-xs text-gray-500">{{ crew.role }}</p>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </div>
    <a class="bg-sky-500 text-white py-2 px-2 rounded shadow-md text-xs hover:bg-sky-600" href="">К списку миссий</a>
  </div>
</template>
