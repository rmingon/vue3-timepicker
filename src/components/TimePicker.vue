<template>
  <div class="flex flex-col w-32 h-64 overflow-y-scroll block timepicker shadow-xl space-y-1">
    <input class="border p-1 mx-2 mt-1" type="text" v-model="search">
    <div class="cursor-pointer text-xl text-cyan-800 hover:bg-cyan-100 pl-2"
         :class="time.to_string === selected ? 'bg-cyan-200' : ''"
         @click="setHour(time)"
         v-for="time in times_filtered"
         value=""
    >
      {{time.to_string }}
    </div>
  </div>
</template>

<script setup lang="ts">
  import {computed, onMounted, ref} from "vue";

  const search = ref('')

  interface Props {
    step: number
    modelValue: Time
    format: "12" | "24"
  }

  interface Time {
    hour: number,
    minute: number,
    period?: "PM" | "AM" | null,
    to_string: string
  }

  const times_filtered = computed<Time[]>(() => times.value.filter(el => el.to_string.replaceAll(' ', '').includes(search.value.replaceAll(' ', ''))))
  const selected = computed(() => props.modelValue.to_string)

  const props = withDefaults(defineProps<Props>(), {
    step: 15,
    period: null
  })

  const emits = defineEmits<{
    (e: 'update:modelValue', time: Time): void
  }>()

  const setHour = (time_selected: Time) => {
    emits('update:modelValue', time_selected)
  }

  const times = computed<Time[]>(() => {
    const _times : Time[] = []
    for(let hour = 0; hour <= 23; hour++) {
      for (let minute = 0; minute < 60; minute += props.step) {
        const period: string = ` ${hour < 12 ? 'AM' : 'PM'}`
        const hour_by_format: number = hour <= 12 ? hour : hour - 12
        _times.push({
          hour: props.format === "12" ? hour_by_format : hour,
          minute,
          period: props.period,
          to_string: `${props.format === "12" ? hour_by_format : hour} : ${minute < 10 ? '0'+minute : minute}${props.format === "12" ? period : ''}`
        })
      }
    }
    return _times
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