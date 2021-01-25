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
          :style="{width: `${playerHealth}%`}"
          :aria-label="playerHealth"
        ></span>
      </p>
    </section>
    <section class="game__player player">
      <h2 class="title">Monster Health</h2>
      <p class="player__bar">
        <span
          class="player__health"
          :style="{width: `${monsterHealth}%`}"
          :aria-label="monsterHealth"
        ></span>
      </p>
    </section>
    <p class="game__controls controls">
      <button
        class="button"
        type="button"
        @click="attackMonster"
      >Attack</button>
      <button
        class="button"
        type="button"
        @click="attackMonsterSpecial"
      >Special Attack</button>
      <button class="button" type="button">Heal</button>
      <button class="button" type="button">Surrender</button>
    </p>
    <section class="game__log log">
      <h2 class="title">Game Log</h2>
      <ul class="log__list">
        <li class="log__item log__item--monster">
          Monster hit: <span class="log__value">40</span>
        </li>
        <li class="log__item">
          Player hit: <span class="log__value">100</span>
        </li>
      </ul>
    </section>
  </main>
</template>

<script>
const getRandomInt = (min, max) => {
  return Math.floor(Math.random() * (max - min) + min);
};

export default {
  data() {
    return {
      playerHealth: 100,
      monsterHealth: 100,
      monsterMin: 7,
      monsterMax: 20,
      playerMin: 3,
      playerMax: 15,
      specialDiff: 7
    }
  },
  name: 'App',
  methods: {
    attackMonster() {
      this.monsterHealth -= getRandomInt(this.playerMin, this.playerMax);
      this.attackPlayer();
    },
    attackMonsterSpecial() {
      this.monsterHealth -= getRandomInt(
        this.playerMin + this.specialDiff,
        this.monsterMax + this.specialDiff
      );
      this.attackPlayer();
    },
    attackPlayer() {
      this.playerHealth -= getRandomInt(this.monsterMin, this.monsterMax);
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

.controls {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
  max-width: 500px;
  margin-left: auto;
  margin-right: auto;
}

.log {
  max-width: 500px;
  margin-left: auto;
  margin-right: auto;
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
