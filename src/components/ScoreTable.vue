<script setup>
  import { ref, computed } from 'vue'

  const props = defineProps({
    diceRolled: Array
  });

  const itemsLeftPad = ref([
    {name: "aces", diceNr: 1},
    {name: "twos", diceNr: 2},
    {name: "threes", diceNr: 3},
    {name: "fours", diceNr: 4},
    {name: "fives", diceNr: 5},
    {name: "sixes", diceNr: 6},
  ]);

  const itemsRightPad = ref([
    {name: "3 of a kind", description: "sum of all dice"},
    {name: "4 of a kind", description: "sum of all dice"},
    {name: "full house", description: "25 points"},
    {name: "small straight", description: "30 points"},
    {name: "large straight", description: "40 points"},
    {name: "5 of a kind", description: "50 points"},
    {name: "chance", description: "sum of all dice"},
  ]);

  const diceCalcQuantity = computed(() => {
    const rollResult = {
      1: 0,
      2: 0,
      3: 0,
      4: 0,
      5: 0,
      6: 0
    };

    props.diceRolled.forEach(element => {
      rollResult[element]++;
    });
      
    return rollResult;   
  });
</script>

<template>
  <div class="padContainer">

    <table class="padLeftTable">
      <tr v-for="item in itemsLeftPad">
        <th class="noRightBorder">{{ item.name }}</th>
        <td class="smallDiceBox">
          <img :src="`img/dice${ item.diceNr }.png`" class="smallDice" />
        </td>
        <td class="description">sum of all {{ item.name }}</td>
        <td class="score">{{ diceCalcQuantity[item.diceNr] * item.diceNr }}</td>
      </tr>
      <tr>
        <td class="description noRightBorder" colspan="4">
          earn 12 extra points with a score of 21 or more!
        </td>
      </tr>
      <tr class="blueBorder">
        <th colspan="3">subtotal</th>
        <td class="score">-</td>
      </tr>
      <tr class="noBottomBorder">
        <th colspan="2">bonus</th>
        <td class="description">12 points</td>
        <td class="score">x</td>
      </tr>
    </table>

    <table class="padRightTable">
      <tr v-for="item in itemsRightPad">
        <th>{{ item.name }}</th>
        <td class="description">{{ item.description }}</td>
        <td class="score">-</td>
      </tr>
      <tr class="blueBorder">
        <th colspan="2">subtotal</th>
        <td class="score">-</td>
      </tr>
      <tr class="noBottomBorder">
        <th colspan="2">total score</th>
        <td class="score totalScoreBg">-</td>
      </tr>
    </table>

  </div>
</template>

<style scoped>
  .padContainer {
    display: flex;
    margin-top: 20px;
    height: 100%;
  }

  .padLeftTable,
  .padRightTable {
    border-collapse: collapse;
    background-color: #F5EBE3;
    width: 100%;
    border-radius: 4px;
    box-shadow: 1px 2px 6px rgba(53, 30, 12, 0.45);
  }

  .padLeftTable {
    margin-right: 20px;
  }

  .smallDice {
    width: 35px;
    height: auto;
    margin: 4px 0 0 8px;
    opacity: 0.8;
  }

  .score {
    font-size: 18px;
    font-weight: 700;
    color: #2b6981;
    text-align: center;
  }

  td.score {
    width: 70px;
    min-width: 70px;
  }

  .totalScoreBg {
    background-color: #42424210;
  }

  td,
  th {
    height: 35px;
    max-height: 35px;
  }

  th,
  .description {
    text-transform: uppercase;
    text-align: left;
    padding-left: 15px;
  }

  th {
    color: #424242;
    font-size: 13px;
  }

  .description {
    color: #2d2d2d;
    font-size: 10px;
  }

  tr {
    border-bottom: 1px solid #424242;
  }

  th,
  .description,
  .smallDiceBox {
    border-right: 1px solid #424242;
  }

  .noRightBorder {
    border-right: none;
  }

  .noBottomBorder {
    border-bottom: none;
  }

  .blueBorder {
    border-top: 2px solid #2b6981;
  }
</style>