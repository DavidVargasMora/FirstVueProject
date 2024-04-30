<template>
  <div>
    <svg 
      viewBox="0 0 300 200"
      @touchstart="tap"
      @touchmove="tap"
      @touchend="untap">
        <line stroke="#c4c4c4" stroke-width="2" x1="0" :y1="zero" x2="300" :y2="zero" />
        <polyline fill="none" stroke="#0689B0" stroke-witdh="2" :points="points" />
        <line v-show="showPointer" stroke="#04b500" stroke-witdh="2" :x1="pointer" y1="0" :x2="pointer" y2="200" />
    </svg>
    <p>Ultimos 30 días</p>
  </div>
</template>
<script setup>
import { defineProps, toRefs, computed, ref, defineEmits, watch } from 'vue'

const props = defineProps({
  amounts: {
    type: Array,
    default: () => []
  }
})

const { amounts } = toRefs(props)

const amountToPixels = (amount) => {
    let min = Math.min(...amounts.value);
    let max = Math.max(...amounts.value);

    var amountAbs = amount + Math.abs(min);
    var minMax = Math.abs(max) + Math.abs(min);
    
    return 200 - (amountAbs * 100 / minMax) * 2;

}

const zero = computed (() => {
    return amountToPixels(0);
} )

const points = computed(() => {
  const total = amounts.value.length
  return amounts.value
    .reduce((points, amount, i) => {
      const x = (300 / total) * (i+1);
      const y = amountToPixels(amount);
      return `${points} ${x},${y}`
    }, `0, ${amountToPixels(amounts.value.length ? amounts.value[0] : 0)}`)
})

const showPointer = ref(false);
const pointer = ref(0);

watch(pointer, (value) => {
  const index = Math.ceil(value / (300 / amounts.value.length))
  if (index < 0 || index  > amounts.value.length) {
    return;
  }
  else {
    emit("select", amounts.value[index - 1]);
  }
});

const emit = defineEmits(["select"]);

const tap = ({ target, touches }) => {
  showPointer.value = true;
  const elementWidth = target.getBoundingClientRect().width;
  const elementX = target.getBoundingClientRect().x;
  const touchX = touches[0].clientX;

  pointer.value = (touchX - elementX) * 300 / elementWidth;
  
}

const untap = () => {
  showPointer.value = false;
}
</script>
<style scoped>
svg {
  width: 100%;
}

p {
  text-align: center;
}
</style>
