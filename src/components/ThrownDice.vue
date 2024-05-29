<script setup>

  const props = defineProps(['modelValue'])
  const emit = defineEmits(['update:modelValue'])

  let dicePieces = 5;
  
  const rollDice = () => {
    const diceRolled = [];

    for(let i = 0; diceRolled.length < dicePieces; i++) {
      diceRolled.push(Math.ceil(Math.random() * 6));
    }

    emit('update:modelValue', diceRolled);
  };
</script>

<template>
  <div class="diceContainer">
    <button @click="rollDice" id="btnRoll">ROLL DICE!</button>
    <div class="diceImageBox">
      <template v-if="props.modelValue.length === 0">
        <img v-for="n in 5" :src="`img/dice${ n }Large-nb.png`" class="diceDummy">
      </template>
      <template v-for="dicePiece of props.modelValue">
        <img :src="`img/dice${ dicePiece }Large-nb.png`" class="fiveDice">
      </template>
    </div>
    <div class="infoBox"><p>some info</p></div>
  </div>
</template>

<style scoped>
  .diceContainer {
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 100%;
  }

  .diceImageBox {
    display: flex;
    justify-content: space-between;
    width: fit-content;
  }

  .fiveDice,
  .diceDummy {
    width: 70px;
    height: 70px;
    background-color: #F5EBE3;
    border-radius: 10px;
    margin-right: 5px;
  }

  .fiveDice {
    box-shadow: 1px 2px 6px rgba(53, 30, 12, 0.45);
  }
  
  .diceDummy {
    opacity: 0.3;
  }

  .infoBox {
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: #F5EBE3;
    color: #424242;
    font-size: 14px;
    text-transform: uppercase;
    height: 70px;
    width: 190px;
    border-radius: 4px;
    box-shadow: 1px 2px 6px rgba(53, 30, 12, 0.45);
  }

  #btnRoll {
    background-color: #F5EBE3;
    color: #357892;
    font-size: 18px;
    font-weight: 700;
    height: 70px;
    width: 76px;
    border-radius: 10px;
    box-shadow: 1px 2px 6px rgba(53, 30, 12, 0.45);
    cursor: pointer;
    transition: box-shadow 0.15s ease-in-out, 
    color 0.15s ease-in-out, background-color 0.15s ease-in-out;
  } 

  #btnRoll:hover {
    color: #438fad;
    background-color: #fdf6f0;
  }

  #btnRoll:active {
    box-shadow: 0 0 10px rgba(245, 235, 227, 0.35);
  }
</style>