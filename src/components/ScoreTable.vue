<script setup>
  import { computed } from 'vue'

  const props = defineProps({
    diceRolled: Array
  });

  // --------------> LEFT PAD

  const itemsLeftPad = {
    1: { name: "aces" },
    2: { name: "twos" },
    3: { name: "threes" },
    4: { name: "fours" },
    5: { name: "fives" },
    6: { name: "sixes" },
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

  // combiNr is linked to the 'pointsCombination' object (see row 119)
  const itemsRightPad = [
    { combiNr: 1, name: "3 of a kind", description: "sum of all dice" },
    { combiNr: 2, name: "4 of a kind", description: "sum of all dice" },
    { combiNr: 3, name: "full house", description: "25 points" },
    { combiNr: 4, name: "small straight", description: "30 points" },
    { combiNr: 5, name: "large straight", description: "40 points" },
    { combiNr: 6, name: "5 of a kind", description: "50 points" },
    { combiNr: 7, name: "chance", description: "sum of all dice" },
  ];

  const calcOfKind = computed(() => {
    let whatKind = 0;

    Object.values(calcQuantity.value).forEach(value => {
      value.quantity < 3 ? whatKind : whatKind = value.quantity;
    });

    return whatKind;
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

  const pointsCombination = computed(() => {
    return {
      1: calcOfKind.value >= 3 ? calcSubtotalDice.value : 0,  // three of kind
      2: calcOfKind.value >= 4 ? calcSubtotalDice.value : 0,  // four of kind
      3: calcFullHouse.value,                                 // full house
      4: calcStraight.value != 0 ? 30 : 0,                    // small straight
      5: calcStraight.value != 40 ? 0 : 40,                   // large straight
      6: calcOfKind.value === 5 ? 50 : 0,                     // five of kind
      7: calcSubtotalDice.value,                              // chance
    }
  });

  const calcSubtotalCombi = computed(() => {
    let points = 0;

    Object.values(pointsCombination.value).forEach(value => {
      points += value;
    });

    return points;
  });

  const calcTotalScore = computed(() => {
    return calcSubtotalDice.value + calcSubtotalCombi.value; 
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
      <tr v-for="(item) in itemsRightPad">
        <th>{{ item.name }}</th>
        <td class="description">{{ item.description }}</td>
        <td class="score">{{ pointsCombination[item.combiNr] }}</td>
      </tr>
      <tr class="blueBorder">
        <th colspan="2">subtotal</th>
        <td class="score">{{ calcSubtotalCombi }}</td>
      </tr>
      <tr class="noBottomBorder">
        <th colspan="2">total score</th>
        <td class="score totalScoreBg">{{ hasBonus ? calcTotalScore + 12 : calcTotalScore }}</td>
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