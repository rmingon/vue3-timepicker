<template>
  <div class="flex flex-col w-32 h-64 overflow-y-scroll block timepicker shadow-xl space-y-1">
    <input class="border p-1 mx-2 mt-1" type="text" v-model="search">
    <div class="cursor-pointer text-xl text-cyan-800 hover:bg-cyan-100 pl-2" @click="setHour(time)" v-for="time in times" value="">{{ time }}</div>
  </div>
</template>

<script setup lang="ts">
  import {computed, onMounted, ref} from "vue";

  const times = ref<Time[]>([])

  const search = ref('')

  interface Props {
    step: number
  }

  interface Time {
    hour: number,
    minute: number,
    period?: "PM" | "AM" | null,
    toString: string
  }

  const times = computed(() => minutes.value.filter(el => el.replaceAll(' ', '').includes(search.value.replaceAll(' ', ''))))

  const props = withDefaults(defineProps<Props>(), {
    step: 15
  })

  const emits = defineEmits<{
    (e: 'selected', time: Time)
  }>()

  const setHour = (time: Time) => {
    emits('selected', time)
  }

  onMounted(() => {
    for(let hour = 0; hour <= 23; hour++) {
      for (let minute = 0; minute < 60; minute += props.step) {
        minutes.value.push(`${hour} : ${minute < 10 ? '0'+minute : minute} ${hour < 12 ? 'AM' : 'PM'}`)
      }
    }
  })
</script>

<style scoped >

.timepicker {
  font-family: 'Montserrat', cursive;
}

.scrollbar::-webkit-scrollbar {
  width: 20px;
  height: 20px;
}

.scrollbar::-webkit-scrollbar-track {
  border-radius: 100vh;
  background: #f7f4ed;
}

.scrollbar::-webkit-scrollbar-thumb {
  background: #e0cbcb;
  border-radius: 100vh;
  border: 3px solid #f6f7ed;
}

.scrollbar::-webkit-scrollbar-thumb:hover {
  background: #c0a0b9;
}

</style>