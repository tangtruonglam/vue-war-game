<template>
  <div id="home">
    <!-- START  -->
    <!-- LIST LESSION -->
    <!-- <ul>
      <li v-for="ingredient in ingredients" :key="ingredient">{{ingredient}}</li>
      <li
        v-for="(person, index) in people"
        :key="index"
      >{{index}}. {{person.name}} da {{person.color}}</li>
    </ul>-->
    <!-- LIST LESSION -->
    <!-- END -->

    <!-- START -->
    <!-- CONDITIONAL - LISTS -->
    <section class="row">
      <div class="small-6 columns">
        <h1 class="text-center">SOLDIER</h1>
        <div class="healthbar">
          <div
            class="healthbar text-center"
            style="background-color: green; margin: 0; color: white;"
            :style="{width: playerHealth + '%'}"
          >{{playerHealth}}</div>
        </div>
      </div>
      <div class="small-6 columns">
        <h1 class="text-center">MONSTER</h1>
        <div class="healthbar">
          <div
            class="healthbar text-center"
            style="background-color: green; margin: 0; color: white;"
            :style="{width: monsterHealth + '%'}"
          >{{monsterHealth}}</div>
        </div>
      </div>
    </section>
    <section class="row controls" v-if="!gameIsRunning">
      <div class="small-12 columns">
        <button id="start-game" @click="startGame">START NEW GAME</button>
      </div>
    </section>
    <section class="row controls" v-else>
      <div class="small-12 columns">
        <button id="attack" @click="attack">ATTACK</button>
        <br />
        <button id="special-attack" @click="specialAttack">SOLDIER SPECIAL ATTACK</button>
        <button id="heal" @click="heal">MONSTER HEAL</button>
        <button id="give-up" @click="giveUp">GIVE UP</button>
      </div>
    </section>
    <section class="row log">
      <div class="small-12 columns">
        <ul>
          <li
            v-for="(turn, index) in turns"
            :key="index"
            :class="{'player-turn': turn.isPlayer, 'monster-turn': !turn.isPlayer}"
          >
            {{turn.text}}
            <hr v-if="index % 2" />
          </li>
        </ul>
      </div>
    </section>
    <!-- CONDITION - LISTS -->
    <!-- END -->
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';

interface ITurn {
  isPlayer: boolean;
  text: string;
}

@Component
export default class Home extends Vue {
  private playerHealth: number = 0;
  private monsterHealth: number = 0;
  private gameIsRunning: boolean = false;
  private turns: ITurn[] = [];
  private startGame() {
    this.gameIsRunning = true;
    this.playerHealth = 100;
    this.monsterHealth = 100;
    this.turns = [];
  }
  private attack() {
    const max = 6;
    const min = 2;
    const damage = this.calculateDamage(min, max);
    this.monsterHealth -= damage;
    this.turns.unshift({
      isPlayer: true,
      text: `SOLDIER hits MONSTER for ${damage}hp`,
    });

    if (this.checkWin()) {
      return;
    }
    this.monsterAttack();
  }

  private monsterAttack() {
    const max2 = 9;
    const min2 = 3;
    const damage = this.calculateDamage(min2, max2);
    this.playerHealth -= damage;
    this.turns.unshift({
      isPlayer: false,
      text: `MONSTER hits PLAYER for ${damage}hp`,
    });

    this.checkWin();
  }

  private specialAttack() {
    const damage = this.calculateDamage(10, 15);
    this.monsterHealth -= damage;
    this.turns.unshift({
      isPlayer: true,
      text: `SOLDIER hits MONSTER ${damage}hp`,
    });

    if (this.checkWin()) {
      return;
    }
    this.monsterAttack();
  }

  private heal() {
    if (this.monsterHealth <= 50) {
      this.monsterHealth += 10;
      this.playerHealth -= 2;
    }
    this.turns.unshift({
      isPlayer: true,
      text: `SOLDIER had decreased ${2}hp`,
    });
    this.turns.unshift({
      isPlayer: false,
      text: `MONSTER had increased ${10}hp`,
    });
  }
  private giveUp() {
    this.resetGame();
  }

  private checkWin() {
    if (this.monsterHealth <= 0) {
      if (confirm('Do you want play new game?')) {
        this.startGame();
      } else {
        this.resetGame();
      }
      return true;
    }
    if (this.playerHealth <= 0) {
      if (confirm('Do you want play new game?')) {
        this.startGame();
      } else {
        this.resetGame();
      }
      return true;
    }
    return false;
  }
  private resetGame() {
    this.gameIsRunning = false;
    this.playerHealth = 0;
    this.monsterHealth = 0;
    this.turns = [];
  }
  private calculateDamage(min: number, max: number) {
    return Math.max(Math.floor(Math.random() * max) + 1, min);
  }

  // private ingredients: string[] = ['meat', 'fruit', 'cookie'];
  // private people: object[] = [
  //   { name: 'Nam', age: 18, color: 'black' },
  //   { name: 'Minh', age: 18, color: 'blue' },
  // ];
}
</script>

<style scoped lang="scss">
.text-center {
  text-align: center;
}

.healthbar {
  height: 40px;
  background-color: #eee;
  margin: auto;
  transition: width 500ms;
}

.controls,
.log {
  margin-top: 30px;
  text-align: center;
  padding: 10px;
  border: 1px solid #ccc;
  box-shadow: 0px 3px 6px #ccc;
}

.turn {
  margin-top: 20px;
  margin-bottom: 20px;
  font-weight: bold;
  font-size: 22px;
}

.log ul {
  list-style: none;
  font-weight: bold;
  text-transform: uppercase;
}

.log ul li {
  margin: 5px;
}

.log ul .player-turn {
  color: blue;
  background-color: #e4e8ff;
}

.log ul .monster-turn {
  color: red;
  background-color: #ffc0c1;
}

button {
  font-size: 20px;
  background-color: #eee;
  padding: 12px;
  box-shadow: 0 1px 1px black;
  margin: 10px;
}

#start-game {
  background-color: #aaffb0;
}

#start-game:hover {
  background-color: #76ff7e;
}

#attack {
  background-color: #ff7367;
}

#attack:hover {
  background-color: #ff3f43;
}

#special-attack {
  background-color: #ffaf4f;
}

#special-attack:hover {
  background-color: #ff9a2b;
}

#heal {
  background-color: #aaffb0;
}

#heal:hover {
  background-color: #76ff7e;
}

#give-up {
  background-color: #ffffff;
}

#give-up:hover {
  background-color: #c7c7c7;
}
</style>
