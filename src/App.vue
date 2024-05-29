<script setup>
  import ThrownDice from './components/ThrownDice.vue'
  import ScoreTable from './components/ScoreTable.vue'
  import { ref } from 'vue'

  const diceRolled = ref([]);
</script>

<template>
  <div class="mainContainer">
    <ThrownDice v-model="diceRolled" />
    <ScoreTable :diceRolled="diceRolled" />	
	</div>
</template>

<style scoped>
  .mainContainer {
    display: flex;
    flex-direction: column;
    background-color: #438fad;
    border-radius: 4px;
    width: 768px;
    height: 481px;
    margin: 40px auto;
    padding: 15px 20px 25px;
  }
</style>