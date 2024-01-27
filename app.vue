<script setup lang="ts">
const config = useRuntimeConfig();

const search = ref('tokyo');
const input = ref('');

const { data: city, error } = await useAsyncData(
  'city',
  async () => {
    let response;

    try {
      response = await $fetch(
        `https://api.openweathermap.org/data/2.5/weather?q=${search.value}`,
        {
          params: {
            units: 'metric',
            appid: config.public.WEATHER_APP_SECRET,
          }
        }
      );

    } catch (e) { }

    return response;
  },
  {
    watch: [search]
  }
);

let todayDate = new Date();

let formattedTodayDate = todayDate.toLocaleDateString("en-US", {
  weekday: 'long',
  year: 'numeric',
  month: 'long',
  day: 'numeric',
});

const handleClick = () => { 
  search.value = input.value;
};
const goBack = () => { 
  search.value = 'tokyo';
};

</script>
<template>
  <div v-if="city" class="">
    <div class="h-full">
      <div class="grid grid-cols-2">
          <input
            type="text"
            class="border-solid border-black border h-10"
            placeholder="都市名を入力"
            v-model="input"
          />
        <button class="bg-green-400 h-10" @click="handleClick">
          検索
        </button>
        <div>
          <h1 class="text-7xl h-20 truncate">{{ city.name }}</h1>
          <p class="mt-4">{{ formattedTodayDate }}</p>
          <img
            :src="`https://openweathermap.org/img/wn/${city.weather[0].icon}@4x.png`"
            class="w-56 icon"
          />
        </div>
        <div>
          <p class="text-8xl">
            {{ city.main.temp.toFixed(1) }}°
          </p>
        </div>
      </div>
    </div>
  </div>
  <div v-else class="p-10">
    <h1 class="text-5xl">対象の都市が見つかりません</h1>
    <button class="mt-5 bg-sky-400 px-10 w-50 h-10" @click="goBack">
      戻る 
    </button>
  </div>
</template>
