<template>
  <main class="container">
    <div class="wrapper">
      <button class="start-button" @click="startGame" :disabled="isGameStarted">
        Start
      </button>
      <div class="inputs-wrapper">
        <fieldset :disabled="isGameStarted">
          <p>Choose difficulty</p>
          <label>
            Easy
            <input
              type="radio"
              value="easy"
              name="difficulty"
              v-model="difficulty"
            />
          </label>
          <label>
            Normal
            <input
              type="radio"
              name="difficulty"
              v-model="difficulty"
              value="normal"
            />
          </label>
          <label>
            Hard
            <input
              type="radio"
              name="difficulty"
              v-model="difficulty"
              value="hard"
            />
          </label>
        </fieldset>
      </div>
    </div>
    <fieldset class="buttons-wrapper">
      <button
        v-for="button in initButtons"
        :disabled="areButtonsDisabled"
        :style="{ background: button.color }"
        :class="{ button, active: button.isActivated }"
        :key="button.id"
        @click="handlePress(button.id)"
      ></button>
    </fieldset>

    <span class="round">Round: {{ round }}</span>
    <p class="info">{{ info }}</p>
  </main>
</template>

<script>
export default {
  data() {
    return {
      defaultMaxLevel: 20,
      areButtonsDisabled: true,
      difficulty: "easy",
      isGameStarted: false,
      info: "Start game",
      round: 0,
      playerSequence: [],
      gameSequence: [],
      initButtons: [
        { id: 0, color: "forestgreen ", isActivated: false },
        { id: 1, color: "tomato ", isActivated: false },
        { id: 2, color: "gold", isActivated: false },
        { id: 3, color: "cornflowerblue", isActivated: false },
      ],
    };
  },
  computed: {
    timeout() {
      switch (this.difficulty) {
        case "easy":
          return 1.5;
        case "normal":
          return 1;
        case "hard":
          return 0.4;
        default:
          return 1.5;
      }
    },
  },
  methods: {
    startGame() {
      this.isGameStarted = true;
      this.setInfo("Wait for computer");
      this.nextRound();
    },

    nextRound() {
      this.round++;
      this.playerSequence.length = 0;
      this.areButtonsDisabled = true;
      this.setInfo("Wait for computer");

      this.gameSequence.push(this.getRandomNumber());
      this.playRound();
    },

    activateButton(id) {
      this.initButtons.forEach((button) => {
        if (button.id === id) {
          this.initButtons[button.id].isActivated = true;
          setTimeout(() => {
            this.initButtons[button.id].isActivated = false;
          }, (this.timeout / 2) * 1000);
        }
      });
    },

    playRound() {
      this.gameSequence.forEach((id, index) => {
        setTimeout(() => {
          this.activateButton(id);
        }, this.timeout * 1000 * (index + 1));
      });
      setTimeout(() => {
        this.playerTurn(this.round);
      }, this.timeout * 1000 * (this.gameSequence.length + 0.6));
    },

    playerTurn(round) {
      this.areButtonsDisabled = false;
      this.setInfo(`Your turn:  press ${round} button${round > 1 ? "s" : ""}`);
    },

    handlePress(id) {
      if (this.areButtonsDisabled) {
        return;
      }
      if (!this.isGameStarted) {
        return;
      }
      this.playerSequence.push(id);

      const remainingButtons =
        this.gameSequence.length - this.playerSequence.length;

      if (this.gameSequence[this.playerSequence.length - 1] !== id) {
        this.resetGame("Oops! Game over, you pressed the wrong tile");
        return;
      }

      if (this.gameSequence.length === this.playerSequence.length) {
        if (this.playerSequence.length === this.defaultMaxLevel) {
          this.resetGame(
            `Congrats! You completed all ${this.defaultMaxLevel} levels`
          );
        }
        this.clearPlayerSequence = 0;
        this.info = "Success! Keep going!";
        this.nextRound();
        return;
      }

      this.setInfo(
        `Your turn:  press ${remainingButtons} button${
          remainingButtons > 1 ? "s" : ""
        }`
      );
    },

    getRandomNumber() {
      return Math.floor(Math.random() * 4);
    },

    resetGame(info) {
      this.gameSequence.length = 0;
      this.clearPlayerSequence;
      this.round = 0;
      this.isGameStarted = false;
      this.info = info;
      this.areButtonsDisabled = true;
    },

    clearPlayerSequence() {
      this.playerSequence.length = 0;
    },
    setInfo(info) {
      this.info = info;
    },
  },
};
</script>

<style lang="scss">
.container {
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  gap: 1rem;
}
.buttons-wrapper {
  display: grid;
  grid-template-columns: repeat(2, 20rem);
  gap: 1rem;
  align-content: center;
  justify-content: center;
  border: none;
  &:disabled {
    cursor: not-allowed;
    pointer-events: none;
  }
}

.button {
  border-radius: 4px;
  border: 2px solid rgba(0, 0, 0, 0);
  height: 5rem;
  cursor: pointer;
  opacity: 0.6;

  &:hover {
    opacity: 1;
    border-color: darkslateblue;
  }
  &.active {
    opacity: 1;
    border-color: darkslateblue;
  }

  &:disabled {
    pointer-events: none;
    &:hover {
      opacity: 0.6;
      border-color: rgba(0, 0, 0, 0);
    }

    &.active {
      opacity: 1;
      border-color: darkslateblue;
    }
  }
}
.start-button {
  border-radius: 4px;
  background: deepskyblue;
  border: none;
  color: #fff;
  font-size: 1.8rem;
  padding: 0.5em 1em;
  cursor: pointer;

  &:disabled {
    opacity: 0.6;
    cursor: not-allowed;
  }
}

.round {
  font-size: 1.6rem;
  text-transform: uppercase;
  font-weight: bold;
}

.wrapper {
  display: flex;
  gap: 1rem;
  height: 4rem;

  p {
    font-size: 1.4rem;
    margin: 0;
  }
}

.inputs-wrapper {
  display: flex;
  flex-direction: column;

  label {
    font-size: 1.2rem;
  }

  fieldset {
    height: 100%;
    padding: 0.5em;
    border: 2px solid deepskyblue;
    border-radius: 4px;

    &:disabled {
      opacity: 0.6;
      cursor: not-allowed;
    }
  }
}
.info {
  font-size: 1.4rem;
}
</style>
