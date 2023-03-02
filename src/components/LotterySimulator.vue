<!-- Please remove this file from your project -->
<template>
  <div class="wrapper">
    <h1>Lottery simulator</h1>
    <div class="stats-container">
      <p>Number of tickets</p>
      <p>{{ counter }}</p>
      <p class="years-spent" :class="{ 'game-over': amIFilthyRich }">
        Years spent:
      </p>
      <p class="years-spent" :class="{ 'game-over': amIFilthyRich }">
        {{ numberOfYearsPassed }}
      </p>
      <p>Cost of tickets:</p>
      <p>{{ moneySpent }} Ft</p>
    </div>
    <div class="match-stats-container">
      <div
        class="match-stats"
        v-for="index in Object.keys(numberOfMatches)"
        :key="index + 'match'"
      >
        <p class="match-category">{{ index }} matches</p>
        <p>{{ numberOfMatches[index as keyof NumberOfMatches] }}</p>
      </div>
    </div>
    <NumberFileds
      :number-array="luckyNumbers"
      :max-limit="maxLimit"
      :min-limit="minLimit"
      label="Winning numbers"
    />
    <NumberFileds
      :number-array="betNumbers"
      :is-input="true"
      :max-limit="maxLimit"
      :min-limit="minLimit"
      label="Loosing numbers"
    />
    <div class="control-container">
      <p>Play with random numbers</p>
      <input
        class="random-check"
        type="checkbox"
        v-model="shouldPlayWithRandomNumbers"
      />
    </div>
    <div class="control-container slide">
      <p>Speed</p>
      <input
        class="speed-bar"
        type="range"
        min="1"
        max="1000"
        step="1"
        v-model="delaySpeed"
      />
    </div>
    <button class="reset-button" @click="onReset">Reset</button>
  </div>
</template>
<style lang="scss" scoped>
.wrapper {
  margin: 0 auto;
  padding: 50px 0 50px 16px;
  border-radius: 10px;
  max-width: 1280px;
  min-width: 310px;
  border: 1px solid gray;
  background-color: #242424;

  @media (min-width: 660px) {
    padding: 50px 150px;
  }

  h1 {
    margin-bottom: 45px;
    color: rgb(137, 206, 34);
  }
  .stats-container {
    display: grid;
    grid-template-columns: auto 100px;
    column-gap: 60px;
    width: fit-content;
    background-color: #3470c7;
    padding: 10px;
    margin: 0 auto 25px;
    border-radius: 10px;
    border: 1px solid rgb(232, 232, 232);

    @media (min-width: 570px) {
      grid-template-columns: 200px 100px;
    }

    .years-spent {
      &.game-over {
        font-weight: 700;
        color: red;
      }
    }
  }

  .match-stats-container {
    display: grid;
    grid-template-columns: repeat(2, 148px);
    grid-template-rows: repeat(2, 1fr);
    border-radius: 10px;
    margin: 0 auto 35px;
    border-radius: 10px;
    border: 1px solid rgb(191, 226, 73);
    color: rgb(191, 226, 73);
    width: fit-content;

    > :nth-child(1),
    > :nth-child(2) {
      border-bottom: 1px solid gray;
    }
    > :nth-child(even) {
      border-left: 1px solid gray;
    }

    @media (min-width: 425px) {
      display: flex;

      > :not(:first-child) {
        border-left: 1px solid gray;
      }
      > :nth-child(1),
      > :nth-child(2) {
        border-bottom: none;
      }
    }
    .match-stats {
      padding: 5px 10px;

      .match-category {
        white-space: nowrap;
      }
    }
  }

  .control-container {
    display: flex;
    gap: 95px;

    &.slide {
      gap: 25px;
      margin-bottom: 20px;
    }
  }

  .random-check {
    width: 25px;
    height: 45px;
    cursor: pointer;
  }

  .speed-bar {
    width: 241px;
  }
  .reset-button {
    width: calc(100% - 30px);
    height: 40px;
    padding: 0;

    @media (min-width: 440px) {
      width: 160px;
    }
  }
}
</style>
;

<script setup lang="ts">
//@ts-ignore
import { Chance } from "chance";
import { ref, reactive } from "vue";
import NumberFileds from "./NumberFileds.vue";

interface NumberOfMatches {
  [key: number | string]: number;
}

let betNumbers = reactive([0, 0, 0, 0, 0]);
let luckyNumbers = reactive([0, 0, 0, 0, 0]);
const maxLimit = 90;
const minLimit = 1;
const delaySpeed = ref("1");
const counter = ref(0);
const numberOfYearsPassed = ref(0);
const moneySpent = ref(0);
let numberOfMatches: NumberOfMatches = reactive({
  2: 0,
  3: 0,
  4: 0,
  5: 0,
});
const amIFilthyRich = ref(false);
const shouldPlayWithRandomNumbers = ref(false);

const generateRandomNumbers = (
  minLimit: number,
  maxLimit: number,
  length: number
) => {
  const chance = new Chance();
  const randomNumbers: number[] = [];
  while (randomNumbers.length < length) {
    let value = chance.integer({ min: minLimit, max: maxLimit });
    while (randomNumbers.includes(value)) {
      value = chance.integer({ min: minLimit, max: maxLimit });
    }
    randomNumbers.push(value);
  }
  return randomNumbers;
};

const updateStates = (mathesNr: number) => {
  if (mathesNr in numberOfMatches) {
    numberOfMatches[mathesNr as keyof NumberOfMatches]++;
  }
  numberOfYearsPassed.value = Math.floor(counter.value / 52);
  moneySpent.value = counter.value * 300;
};

const onReset = () => {
  betNumbers = [0, 0, 0, 0, 0];
  luckyNumbers = [0, 0, 0, 0, 0];
  delaySpeed.value = "1";
  counter.value = 0;
  numberOfYearsPassed.value = 0;
  moneySpent.value = 0;
  numberOfMatches = {
    2: 0,
    3: 0,
    4: 0,
    5: 0,
  };
  amIFilthyRich.value = false;
  shouldPlayWithRandomNumbers.value = false;
};

const getLuckyNumbers = () => {
  luckyNumbers = generateRandomNumbers(minLimit, maxLimit, 5);
  if (shouldPlayWithRandomNumbers.value) {
    betNumbers = generateRandomNumbers(minLimit, maxLimit, 5);
  }

  const matchResult = betNumbers
    .map((betNumber, index) => betNumber === luckyNumbers[index])
    .filter((elem) => elem);
  counter.value++;
  updateStates(matchResult.length);

  if (matchResult.length === 5) {
    amIFilthyRich.value = true;
    return;
  }

  setTimeout(getLuckyNumbers, 1001 - Number(delaySpeed.value));
};
setTimeout(getLuckyNumbers, 1001 - Number(delaySpeed.value));
</script>
