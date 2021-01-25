<template>
  <header class="header">
    <h1 class="page-title">Monster Slayer Game</h1>
  </header>
  <main class="game" id="monster-slayer">
    <section class="game__player player">
      <h2 class="title">Player Health</h2>
      <p class="player__bar">
        <span
          class="player__health"
          :style="playerBarStyles"
          :aria-label="playerHealth"
        ></span>
      </p>
    </section>
    <section class="game__player player">
      <h2 class="title">Monster Health</h2>
      <p class="player__bar">
        <span
          class="player__health"
          :style="monsterBarStyles"
          :aria-label="monsterHealth"
        ></span>
      </p>
    </section>
    <section class="game__result result" v-if="winner">
      <h2 class="title">Game Over!</h2>
      <p class="result__text">{{ resultMessage }}</p>
      <button
        class="button"
        type="button"
        @click="restartGame"
      >Play Again</button>
    </section>
    <p class="game__controls controls" v-else>
      <button
        class="button"
        type="button"
        @click="attackMonster"
      >Attack</button>
      <button
        class="button"
        type="button"
        @click="attackMonsterSpecial"
        :disabled="!isSpecialAvailable"
      >Special Attack</button>
      <button
        class="button"
        type="button"
        @click="healPlayer"
      >Heal</button>
      <button
        class="button"
        type="button"
        @click="surrender"
      >Surrender</button>
    </p>
    <section class="game__log log">
      <h2 class="title">Game Log</h2>
      <ul class="log__list">
        <li
          class="log__item"
          v-for="message in logMessages.reverse()"
          :class="{'log__item--monster': message.isMonster}"
          :key="message"
        >
          {{ message.player }} {{ message.action }}:
          <span class="log__value">{{ message.value }}</span>
        </li>
      </ul>
    </section>
  </main>
</template>

<script>
const Winner = {
  DRAW: 'draw',
  PLAYER: 'player',
  MONSTER: 'monster'
};
const Player = {
  USER: 'You',
  MONSTER: 'Monster'
};
const Action = {
  ATTACK: 'hit',
  SPECIAL_ATTACK: 'used a special attack',
  SURRENDER: 'surrendered',
  HEAL: 'healed'
};

const getRandomInt = (min, max) => {
  return Math.floor(Math.random() * (max - min) + min);
};

export default {
  data() {
    return {
      playerMaxHealth: 100,
      monsterMaxHealth: 100,
      playerHealth: 100,
      monsterHealth: 100,
      monsterMin: 7,
      monsterMax: 20,
      playerMin: 3,
      playerMax: 15,
      playerHealMin: 10,
      playerHealMax: 25,
      specialDiff: 7,
      round: 0,
      winner: null,
      logMessages: []
    }
  },
  computed: {
    monsterBarStyles() {
      return {width: `${this.monsterHealth}%`};
    },
    playerBarStyles() {
      return {width: `${this.playerHealth}%`};
    },
    isSpecialAvailable() {
      return this.round % 3 === 0;
    },
    resultMessage() {
      switch (this.winner) {
        case Winner.PLAYER:
          return 'Congratulations! You won!';
        case Winner.MONSTER:
          return 'Monster won. Try to win next time.';
        default:
          return 'Draw!';
      }
    }
  },
  watch: {
    playerHealth(value) {
      if (value <= 0 && this.monsterHealth <= 0) {
        this.winner = Winner.DRAW;
      } else if (value <= 0) {
        this.winner = Winner.MONSTER;
      }
    },
    monsterHealth(value) {
      if (value <= 0 && this.playerHealth <= 0) {
        this.winner = Winner.DRAW;
      } else if (value <= 0) {
        this.winner = Winner.PLAYER;
      }
    }
  },
  methods: {
    restartGame() {
      this.playerHealth = this.playerMaxHealth;
      this.monsterHealth = this.monsterMaxHealth;
      this.round = 0;
      this.winner = null;
      this.logMessages = [];
    },
    attackMonster() {
      const attackValue = getRandomInt(this.playerMin, this.playerMax);

      if (this.monsterHealth - attackValue < 0) {
        this.monsterHealth = 0;
      } else {
        this.monsterHealth -= attackValue;
      }

      this.addLogMessage(Player.USER, Action.ATTACK, attackValue);
      this.attackPlayer();
      this.round++;
    },
    attackMonsterSpecial() {
      const attackValue = getRandomInt(
        this.playerMin + this.specialDiff,
        this.monsterMax + this.specialDiff
      );

      if (this.monsterHealth - attackValue < 0) {
        this.monsterHealth = 0;
      } else {
        this.monsterHealth -= attackValue;
      }

      this.addLogMessage(Player.USER, Action.SPECIAL_ATTACK, attackValue);
      this.attackPlayer();
      this.round++;
    },
    attackPlayer() {
      const attackValue = getRandomInt(this.monsterMin, this.monsterMax);

      if (this.playerHealth - attackValue < 0) {
        this.playerHealth = 0;
      } else {
        this.playerHealth -= attackValue;
      }

      this.addLogMessage(Player.MONSTER, Action.ATTACK, attackValue);
    },
    healPlayer() {
      const healValue = getRandomInt(this.playerHealMin, this.playerHealMax);

      if (this.playerHealth + healValue > this.playerMaxHealth) {
        this.playerHealth = this.playerMaxHealth;
      } else {
        this.playerHealth += healValue;
      }

      this.addLogMessage(Player.USER, Action.HEAL, healValue);
      this.attackPlayer();
      this.round++;
    },
    surrender() {
      this.winner = Winner.MONSTER;
    },
    addLogMessage(player, action, value) {
      this.logMessages.push({
        player,
        action,
        value,
        isMonster: player === Player.MONSTER
      });
    }
  }
}
</script>

<style>
:root {
  --c-primary: #DFBAC1;
  --c-primary-dark: #B5A5BF;
  --c-primary-neutral: #D8CCE0;
  --c-neutral: #757DA4;
  --c-black: #13161F;
  --c-black-light: #33394F;

  --basic-transition: 0.2s ease-in-out;
}

body,
html {
  margin: 0;
  padding: 0;
}

body {
  min-width: 375px;
  font-family: 'Work Sans', Arial, sans-serif;
  color: var(--c-black);
}

button {
  font: inherit;
}

.header {
  margin-bottom: 40px;
  padding: 20px;
  text-align: center;
  color: var(--c-primary-neutral);
  background-color: var(--c-black-light);
}

.page-title {
  margin: 0;
  font-weight: 600;
  font-size: 40px;
}

.title {
  margin: 0 0 20px;
  font-weight: 600;
  font-size: 24px;
  text-align: center;
}

.button {
  display: inline-block;
  margin: 0;
  padding: 10px 20px;
  font-size: 16px;
  text-align: center;
  color: var(--c-primary-neutral);
  background-color: var(--c-black-light);
  border: 2px solid var(--c-black-light);
  border-radius: 20px;
  transition: var(--basic-transition);
}

.button:not(:disabled) {
  cursor: pointer;
}

.button:not(:disabled):hover {
  color: var(--c-black-light);
  background-color: var(--c-primary-neutral);
}

.button:disabled {
  background-color: var(--c-neutral);
  border-color: var(--c-neutral);
}

.player {
  max-width: 500px;
  margin-left: auto;
  margin-right: auto;
  padding: 20px 20px 30px;
  border: 1px solid var(--c-neutral);
  border-radius: 10px;
}

.player__bar {
  position: relative;
  width: 85%;
  height: 30px;
  margin: 0 auto;
  background-color: var(--c-neutral);
  border-radius: 20px;
}

.player__health {
  position: absolute;
  top: 3px;
  bottom: 3px;
  left: 3px;
  width: 50%;
  max-width: calc(100% - 6px);
  background-color: var(--c-primary);
  border-radius: 20px;
  transition: width var(--basic-transition);
}

.result {
  max-width: 500px;
  margin: 0 auto;
  padding: 40px;
  text-align: center;
}

.result__text {
  font-size: 20px;
  text-align: center;
}

.controls {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
  max-width: 500px;
  margin-left: auto;
  margin-right: auto;
}

.log {
  max-width: 300px;
  margin-left: auto;
  margin-right: auto;
  padding: 20px 50px;
}

.log__item {
  margin-bottom: 5px;
}

.log__item--monster {
  color: var(--c-primary-dark);
}

.log__value {
  font-weight: 600;
}

.game {
  padding: 0 20px 40px;
}

.game__player {
  margin-bottom: 20px;
}

.game__controls {
  margin-bottom: 40px;
}
</style>
