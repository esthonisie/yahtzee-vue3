<script setup>
  import { computed } from 'vue'

  const props = defineProps({
    diceRolled: Array
  });

  // --------------> LEFT PAD

  const itemsLeftPad = {
    1: {name: "aces"},
    2: {name: "twos"},
    3: {name: "threes"},
    4: {name: "fours"},
    5: {name: "fives"},
    6: {name: "sixes"},
  };

  const calcQuantity = computed(() => {
    const quantityResult = {};

    for (let diceNr = 1; diceNr <= 6; diceNr++) {
      quantityResult[diceNr] = {quantity: 0};
    }

    props.diceRolled.forEach(element => {
      quantityResult[element].quantity++;
    });
    
    return quantityResult;   
  });

  const calcPointsDice = computed(() => {
    const pointsDiceResult = {};
    
    for (const [key, value] of Object.entries(calcQuantity.value)) {
      pointsDiceResult[key] = {points: (key * value.quantity)};
    }

    return pointsDiceResult;   
  });

  const calcSubtotalDice = computed(() => {
    let sum = 0;

    Object.values(calcPointsDice.value).forEach(value => {
      sum += value.points;
    });

    return sum;   
  });

  const hasBonus = computed(() => {
    return calcSubtotalDice.value < 21 ? false : true;
  }); 

  // --------------> RIGHT PAD

  const itemsRightPad = [
    {name: "3 of a kind", description: "sum of all dice", id: 1},
    {name: "4 of a kind", description: "sum of all dice", id: 2},
    {name: "full house", description: "25 points", id: 3},
    {name: "small straight", description: "30 points", id: 4},
    {name: "large straight", description: "40 points", id: 5},
    {name: "5 of a kind", description: "50 points", id: 6},
    {name: "chance", description: "sum of all dice", id: 7},
  ];

  const calcOfKind = computed(() => {
    let whatKind = 0;

    Object.values(calcQuantity.value).forEach(value => {
      value.quantity < 3 ? whatKind : whatKind = value.quantity;
    });

    return whatKind;
  });

  const checkThreeOfKind = computed(() => {
    let points = 0;

    calcOfKind.value >= 3 ? points = calcSubtotalDice.value : points;

    return points;
  });

  const checkFourOfKind = computed(() => {
    let points = 0;

    calcOfKind.value >= 4 ? points = calcSubtotalDice.value : points;

    return points;
  });

  const checkFiveOfKind = computed(() => {
    let points = 0;

    calcOfKind.value === 5 ? points = 50 : points;

    return points;
  });

  const calcFullHouse = computed(() => {
    let points = 0;

    Object.values(calcQuantity.value).forEach(value => {
      if (value.quantity === 2) {
        points += 10;
      } else if (value.quantity === 3) {
        points += 15;
      }
    });

    points < 25 ? points = 0 : points;

    return points;
  });

  const calcStraight = computed(() => {
    const arr = [];
    let points = 0;
    
    // zero|one pattern
    Object.values(calcQuantity.value).forEach(value => {
      value.quantity !== 0 ? arr.push(1) : arr.push(0);
    });

    // first check if dice 3 or 4 exist (always needed in a straight)
    for (let i = 0; i < 6; i++) {
      if (arr[2] === 0 || arr[3] === 0) {
        points = 0; 
      } else if (arr[i] === 1 && arr[i] === arr[i + 1]) {
        points += 10; 
      }
    }

    points < 30 ? points = 0 : points;

    return points;
  });

  const checkSmallStraight = computed(() => {
    let points = 0;

    calcStraight.value != 30 ? points : points = 30;

    return points;
  });

  const checkLargeStraight = computed(() => {
    let points = 0;

    calcStraight.value != 40 ? points : points = 40;

    return points;
  });

  const combiDice = {
    1: checkThreeOfKind,
    2: checkFourOfKind,
    3: calcFullHouse,
    4: checkSmallStraight,
    5: checkLargeStraight,
    6: checkFiveOfKind,
    7: calcSubtotalDice,
  };

  const calcSubtotalCombi = computed(() => {
    let sum = 0;

    Object.values(combiDice).forEach(value => {
      sum += value.value;
    });

    return sum;   
  });

  const calcTotalScore = computed(() => {
    const leftPad = hasBonus.value ? 
      calcSubtotalDice.value + 12 : calcSubtotalDice.value;
    const total = leftPad + calcSubtotalCombi.value;

    return total;   
  });
</script>

<template>
  <div class="padContainer">

    <table class="padLeftTable">
      <tr v-for="(item, key) in itemsLeftPad">
        <th class="noRightBorder">{{ item.name }}</th>
        <td class="smallDiceBox">
          <img :src="`img/dice${ key }.png`" class="smallDice" />
        </td>
        <td class="description">sum of all {{ item.name }}</td>
        <td class="score">{{ calcPointsDice[key].points }}</td>
      </tr>
      <tr>
        <td class="description noRightBorder" colspan="4">
          earn 12 extra points with a score of 21 or more!
        </td>
      </tr>
      <tr class="blueBorder">
        <th colspan="3">subtotal</th>
        <td class="score">{{ hasBonus ? calcSubtotalDice + 12 : calcSubtotalDice }}</td>
      </tr>
      <tr class="noBottomBorder">
        <th colspan="2">bonus</th>
        <td class="description">12 points</td>
        <td class="score" :class="hasBonus ? 'bonusGreen' : 'bonusRed'">
          {{ hasBonus ? '&#x2713;' : 'x' }}
        </td>
      </tr>
    </table>

    <table class="padRightTable">
      <tr v-for="item in itemsRightPad">
        <th>{{ item.name }}</th>
        <td class="description">{{ item.description }}</td>
        <td class="score">{{ combiDice[item.id] }}</td>
      </tr>
      <tr class="blueBorder">
        <th colspan="2">subtotal</th>
        <td class="score">{{ calcSubtotalCombi }}</td>
      </tr>
      <tr class="noBottomBorder">
        <th colspan="2">total score</th>
        <td class="score totalScoreBg">{{ calcTotalScore }}</td>
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

  .bonusRed {
    color: #a73a1c;
  }

  .bonusGreen {
    color: #6d9a24;
  }
</style>