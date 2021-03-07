<template>
  <header>
    <h1>Monster Slayer</h1>
  </header>
  <div id="game">
    <section id="monster" class="container">
      <h2>Monster Health</h2>
      <div class="healthbar">
        <div class="healthbar__value" :style="monsterHealthStyle"></div>
      </div>
    </section>
    <section id="player" class="container">
      <h2>Your Health</h2>
      <div class="healthbar">
        <div class="healthbar__value" :style="playerHealthStyle"></div>
      </div>
    </section>
    <section class="conatiner" v-if="result">
      <h2>Game over!</h2>
      <h3 v-if="result === 'draw'">Draw</h3>
      <h3 v-if="result === 'playerlost'">You lost!</h3>
      <h3 v-if="result === 'monsterlost'">You win!</h3>
    </section>
    <section id="controls">
      <button @click="monsterAttack()">ATTACK</button>
      <button
        @click="specialMonsterAttack()"
        :disabled="SpecialAttackRound % 3 !== 0"
      >
        SPECIAL ATTACK
      </button>
      <button @click="healPlayer()">HEAL</button>
      <button @click="surrender()">SURRENDER</button>
      <button @click="startGame()" v-if="result">Start new game</button>
    </section>
    <section id="log" class="container">
      <h2>Battle Log</h2>
      <ul>
        <li
          v-for="logMessage in logMessages"
          :key="logMessage.actionBy"
          :class="{
            'log--player': logMessage.actionBy === 'Player',
            'log--monster': logMessage.actionBy === 'Monster',
          }"
        >
          <span>{{
            logMessage.actionBy === "Player" ? "Player" : "Monster"
          }}</span>
          <span v-if="logMessage.actionType === 'Heal'">
            healths himself for
            <span class="log--heal">{{ logMessage.actionValue }}</span></span
          >
          <span v-else-if="logMessage.actionType === 'Special-Attack'">
            special-attcks and deals
            <span class="log--damage">{{ logMessage.actionValue }}</span></span
          >
          <span v-else>
            attacks and deals
            <span class="log--damage">{{ logMessage.actionValue }}</span></span
          >
        </li>
      </ul>
    </section>
  </div>
</template>

<script>
export default {
  data() {
    return {
      playerHealth: 100,
      monsterHealth: 100,
      SpecialAttackRound: 0,
      result: null,
      logMessages: [],
    };
  },
  computed: {
    playerHealthStyle() {
      if (this.playerHealth <= 0) {
        return { width: "0%" };
      } else {
        return { width: this.playerHealth + "%" };
      }
    },
    monsterHealthStyle() {
      if (this.monsterHealth <= 0) {
        return { width: "0%" };
      } else {
        return { width: this.monsterHealth + "%" };
      }
    },
  },
  watch: {
    playerHealth(value) {
      if (value <= 0 && this.monsterHealth <= 0) {
        this.result = "draw";
      } else if (value <= 0) {
        this.result = "playerlost";
      }
    },
    monsterHealth(value) {
      if (value <= 0 && this.playerHealth <= 0) {
        this.result = "draw";
      } else if (value <= 0) {
        this.result = "monsterlost";
      }
    },
  },
  methods: {
    startGame() {
      (this.monsterHealth = 100),
        (this.playerHealth = 100),
        (this.SpecialAttackRound = 0),
        (this.result = null),
        (this.logMessages = []);
    },
    monsterAttack() {
      this.SpecialAttackRound++;
      const attackValue = this.randomValue(12, 5);
      this.monsterHealth -= attackValue;
      this.playerAttcak();
      this.logMessageAdd("Player", "Attack", attackValue);
    },
    playerAttcak() {
      const attackValue = this.randomValue(15, 8);
      this.playerHealth -= attackValue;
      this.logMessageAdd("Monster", "Attack", attackValue);
    },
    specialMonsterAttack() {
      this.playerAttcak();
      this.SpecialAttackRound++;
      const attackValue = this.randomValue(25, 10);
      this.monsterHealth -= attackValue;
      this.logMessageAdd("Player", "Special-Attack", attackValue);
    },
    healPlayer() {
      const healValue = this.randomValue(20, 8);
      if (this.playerHealth > 90) {
        this.playerHealth = 100;
      } else if (this.playerHealth < 100) {
        this.playerHealth += healValue;
      }
      this.playerAttcak();
      this.logMessageAdd("Player", "Heal", healValue);
    },
    surrender() {
      this.result = "playerlost";
    },
    logMessageAdd(who, what, value) {
      this.logMessages.unshift({
        actionBy: who,
        actionType: what,
        actionValue: value,
      });
    },

    randomValue(max, min) {
      return Math.round(Math.random() * (max - min) + min);
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
* {
  box-sizing: border-box;
}

/* html {
  font-family: 'Jost', sans-serif;
}

body {
  margin: 0;
} */

header {
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  padding: 0.5rem;
  background-color: #880017;
  color: white;
  text-align: center;
  margin-bottom: 2rem;
}

section {
  width: 90%;
  max-width: 40rem;
  margin: auto;
}

.healthbar {
  width: 100%;
  height: 40px;
  border: 1px solid #575757;
  margin: 1rem 0;
  background: #fde5e5;
}

.healthbar__value {
  background-color: #00a876;
  width: 100%;
  height: 100%;
}

.container {
  text-align: center;
  padding: 0.5rem;
  margin: 1rem auto;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  border-radius: 12px;
}

#monster h2,
#player h2 {
  margin: 0.25rem;
}

#controls {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
}

button {
  font: inherit;
  border: 1px solid #88005b;
  background-color: #88005b;
  color: white;
  padding: 1rem 2rem;
  border-radius: 12px;
  margin: 1rem;
  width: 12rem;
  cursor: pointer;
  box-shadow: 1px 1px 4px rgba(0, 0, 0, 0.26);
}

button:focus {
  outline: none;
}

button:hover,
button:active {
  background-color: #af0a78;
  border-color: #af0a78;
  box-shadow: 1px 1px 8px rgba(0, 0, 0, 0.26);
}

button:disabled {
  background-color: #ccc;
  border-color: #ccc;
  box-shadow: none;
  color: #3f3f3f;
  cursor: not-allowed;
}

#log ul {
  list-style: none;
  margin: 0;
  padding: 0;
}

#log li {
  margin: 0.5rem 0;
}

.log--player {
  color: #7700ff;
}

.log--monster {
  color: #da8d00;
}

.log--damage {
  color: red;
}

.log--heal {
  color: green;
}
</style>
