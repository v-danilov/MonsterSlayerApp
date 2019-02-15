<template>
  <div id="app">
    <section class="row">
      <div class="small-6 columns">
        <h1 class="text-center">YOU</h1>
        <div class="healthbar">
          <div class="healthbar text-center"
               style="background-color: green; margin: 0; color: white;"
          :style="{width : playerHealth + '%'}">
            {{playerHealth}}
          </div>
        </div>
      </div>
      <div class="small-6 columns">
        <h1 class="text-center">MONSTER</h1>
        <div class="healthbar">
          <div class="healthbar text-center"
               style="background-color: green; margin: 0; color: white;"
               :style="{width : monsterHealth + '%'}">
            {{monsterHealth}}
          </div>
        </div>
      </div>
    </section>
    <section class="row controls"
             v-if="!gameIsStarted" >
      <div class="small-12 columns">
        <button id="start-game"
                @click="startTheGame">
          START NEW GAME</button>
      </div>
    </section>
    <section class="row controls"
             v-else>
      <div class="small-12 columns">
        <button id="attack" @click="attackAction(1)">ATTACK</button>
        <button id="special-attack" @click="attackAction(1.5)">SPECIAL ATTACK</button>
        <button id="heal"
                :style="{disabled : playerHealth === 100}"
                @click="healAction">HEAL</button>
        <button id="give-up" @click="giveUp">GIVE UP</button>
      </div>
    </section>
    <section class="row log" v-if="gameActionsLog.length !== 0">
      <div class="small-12 columns">
        <ul>
          <li v-for="(action, index) in gameActionsLog" :key="index">
            {{action}}
          </li>
        </ul>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: 'MainComponent',
  data () {
    return {
      gameIsStarted: false,
      playerHealth: 100,
      monsterHealth: 100,
      gameActionsLog: [],
      randomConfig: {
        maxRandom: 11,
        minRandom: 8
      },
      turnCounter: 0
    }
  },
  methods: {
    startTheGame () {
      this.playerHealth = 100
      this.monsterHealth = 100
      this.gameIsStarted = true
    },
    attackAction (multiplier) {
      // Start new turn
      this.nextTurnLog()
      // Player action
      this.playerAttackTurn(multiplier)
      // Monster action
      this.monsterAttackTurn(multiplier)
      this.checkGameStatus()
    },
    healAction () {
      // Start new turn
      this.nextTurnLog()
      // Player action
      let healValue = this.generateRandom(this.randomConfig.maxRandom)
      let healedPlayerHealthPoints = this.playerHealth + healValue
      this.playerHealth = healedPlayerHealthPoints > 100 ? 100 : healedPlayerHealthPoints
      this.gameActionsLog.push('YOU healed by ' + healValue)
      // Monster action
      this.monsterAttackTurn(1)
      this.checkGameStatus()
    },
    giveUp () {
      this.playerHealth = 100
      this.monsterHealth = 100
      this.gameIsStarted = false
      this.gameActionsLog = []
      this.turnCounter = 0
    },

    // Utils
    generateRandom (max) {
      return Math.floor(Math.random() * Math.floor(max))
    },
    playerAttackTurn (damageMultiplier) {
      let damage = damageMultiplier * this.generateRandom(this.randomConfig.maxRandom)
      this.monsterHealth -= damage
      let logMessage = damage !== 0 ? 'YOU deal ' + damage + ' damage to MONSTER' : 'YOU missed!'
      this.gameActionsLog.push(logMessage)
    },
    monsterAttackTurn (damageMultiplier) {
      let damage = damageMultiplier * this.generateRandom(this.randomConfig.maxRandom)
      this.playerHealth -= damage
      let logMessage = damage !== 0 ? 'MONSTER deals ' + damage + ' damage to YOU' : 'MONSTER missed!'
      this.gameActionsLog.push(logMessage)
    },
    nextTurnLog () {
      this.turnCounter++
      this.gameActionsLog.push('Turn: ' + this.turnCounter)
    },
    checkGameStatus () {
      if (this.playerHealth <= 0) {
        alert('Sorry bro, you loose like a goose')
        this.giveUp()
      }
      if (this.monsterHealth <= 0) {
        alert('Yeah! You win!')
        this.giveUp()
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
