<!-- Please remove this file from your project -->
<template>
  <div class="numbers-container">
    <p>{{ label }}</p>
    <div class="numbers">
      <input
        v-if="isInput"
        class="number"
        v-for="(_, index) in numberArray"
        :key="index + 'rand'"
        @input="onKeyDown"
        type="number"
        v-model="numberArray[index]"
        min="minLimit"
        max="maxLimit"
      />
      <p
        v-else
        v-for="(luckyNumber, index) in numberArray"
        :key="index + 'lucky'"
        class="number lucky"
      >
        {{ luckyNumber }}
      </p>
    </div>
  </div>
</template>
<style lang="scss" scoped>
.numbers-container {
  display: flex;
  flex-wrap: wrap;
  gap: 15px;
  margin-bottom: 20px;
  min-width: 330px;
  align-items: center;

  .numbers {
    display: flex;
    gap: 5px;
    max-width: 320px;

    @media (min-width: 438px) {
      gap: 15px;
    }

    .number {
      -moz-appearance: textfield;
      text-align: center;
      border-radius: 5px;
      line-height: 45px;
      width: 47px;
      height: 50px;
      border: 1px solid rgb(32, 137, 198);
      &::-webkit-outer-spin-button,
      &::-webkit-inner-spin-button {
        -webkit-appearance: none;
        margin: 0;
      }

      &.lucky {
        width: 50px;
        border-color: rgb(16, 222, 112);
      }
    }
  }
}
</style>
;

<script setup lang="ts">
const { numberArray, isInput, maxLimit, minLimit, label } = defineProps<{
  numberArray: number[];
  isInput?: boolean;
  maxLimit: number;
  minLimit: number;
  label: string;
}>();

const onKeyDown = (event: Event) => {
  const numberInput = event.target as HTMLInputElement;
  if (Number(numberInput.value) > maxLimit) {
    numberInput.value = maxLimit.toString();
  }
  if (Number(numberInput.value) < minLimit) {
    numberInput.value = minLimit.toString();
  }
};
</script>
