<template>
  <div id="app">
    <section class="row">
      <div class="small-6 columns">
        <h1 class="text-center">YOU 😃</h1>
        <div class="healthbar">
          <div class="healthbar text-center"
               style="margin: 0; color: white;"
               :style="{width : playerHealth + '%',
                  backgroundColor: invaderHealth > 15 ? 'green' : 'red'}">
            {{playerHealth}}
          </div>
        </div>
      </div>
      <div class="small-6 columns">
        <h1 class="text-center">INVADER 👽</h1>
        <div class="healthbar">
          <div class="healthbar text-center"
               style=" margin: 0; color: white;"
               :style="{width : invaderHealth + '%',
                  backgroundColor: invaderHealth > 15 ? 'green' : 'red' }">
            {{invaderHealth}}
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
          <li v-for="(action, index) in gameActionsLog"
              :key="index"
          :class= "{'invader-turn' : action.initiator === 'invader',
                    'player-turn'  : action.initiator === 'player'}">
            <div>
              {{action.message}}
            </div>
          </li>
        </ul>
      </div>
    </section>
  </div>
</template>

<script>
import { Constants } from './constants/Constants'

export default {
  name: 'MainComponent',
  data () {
    return {
      gameIsStarted: false,
      playerHealth: 100,
      invaderHealth: 100,
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
      this.invaderHealth = 100
      this.gameIsStarted = true
      this.gameActionsLog = []
      this.turnCounter = 0
    },
    attackAction (multiplier) {
      // Start new turn
      this.nextTurnLog()
      // Player action
      this.playerAttackTurn(multiplier)
      // Monster action
      if (this.gameIsStarted) {
        this.invaderAttackTurn(multiplier)
      }
    },
    healAction () {
      // Start new turn
      this.nextTurnLog()
      // Player action
      let healValue = this.generateRandom(this.randomConfig.maxRandom)
      let healedPlayerHealthPoints = this.playerHealth + healValue
      this.playerHealth = healedPlayerHealthPoints > 100 ? 100 : healedPlayerHealthPoints
      this.logAction(Constants.PLAYER, '🚑 YOU healed by ' + healValue)
      // Monster action
      this.invaderAttackTurn(1)
    },
    giveUp () {
      this.gameIsStarted = false
    },

    // Utils
    generateRandom (max) {
      return Math.floor(Math.random() * Math.floor(max))
    },
    playerAttackTurn (damageMultiplier) {
      let damage = damageMultiplier * this.generateRandom(this.randomConfig.maxRandom)
      this.invaderHealth = damage > this.invaderHealth ? 0 : this.invaderHealth - damage
      let logMessage = damage !== 0 ? '🎯 YOU deal ' + damage + ' damage to INVADER' : '⭕ YOU missed!'
      this.logAction(Constants.PLAYER, logMessage)
      this.checkResults()
    },
    invaderAttackTurn (damageMultiplier) {
      let damage = damageMultiplier * this.generateRandom(this.randomConfig.maxRandom)
      this.playerHealth = damage > this.playerHealth ? 0 : this.playerHealth - damage
      let logMessage = damage !== 0 ? '🎯 INVADER deals ' + damage + ' damage to YOU' : '⭕ INVADER missed!'
      this.logAction(Constants.INVADER, logMessage)
      this.checkResults()
    },
    nextTurnLog () {
      this.turnCounter++
      this.logAction(Constants.SYSTEM, 'Turn: ' + this.turnCounter)
    },
    logAction (_initiator, _message) {
      this.gameActionsLog.unshift({initiator: _initiator, message: _message})
    },
    checkResults () {
      if (this.playerHealth <= 0) {
        alert('Sorry bro, you loose like a goose')
        this.gameIsStarted = false
      }
      if (this.invaderHealth <= 0) {
        alert('Yeah! You win!')
        this.gameIsStarted = false
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this compnent only -->
<style scoped>

</style>
