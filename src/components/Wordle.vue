<template>
  <canvas class="absolute -z-50 inset-0 w-full h-full" id="confetti-holder"></canvas>
  <div class="container mx-auto">
    <Words
      :activeIndex="activeIndex"
      :lettersIndex="lettersIndex"
      :currentRow="currentRow"
      @setIndex="setActiveIndex"
    />
    <Keyboard
      @keyPressed="keyPressed"
      :correctLetters="correctLetters"
      :existingLetters="existingLetters"
      :notExistingLetters="notExistingLetters"
    />
  </div>

  <!-- Modal Defeated -->
  <input type="checkbox" id="my-modal-6" class="modal-toggle" />
  <div class="modal modal-bottom sm:modal-middle">
    <div class="modal-box">
      <p class="text-center mb-8">
        Correct Word: <br />
        <b>{{ hiddenWord.toLocaleUpperCase() }}</b>
      </p>

      <h3 class="font-bold text-center text-lg">Statistics</h3>
      <div class="flex justify-center gap-3 mt-4">
        <div class="text-center">
          <p class="text-center">3</p>
          <p class="font-bold">Played</p>
        </div>
        <div class="text-center">
          <p class="text-center">3</p>
          <p class="font-bold">Win %</p>
        </div>
        <div class="text-center">
          <p class="text-center">0</p>
          <p class="font-bold">
            Current<br />
            Streak
          </p>
        </div>
        <div class="text-center">
          <p class="text-center">2</p>
          <p class="font-bold">
            Max<br />
            Streak
          </p>
        </div>
      </div>

      <div class="modal-action">
        <label for="my-modal-6" class="btn">Play Again</label>
      </div>
    </div>
  </div>
</template>

<script>
import Words from "./Words.vue";
import Keyboard from "./Keyboard.vue";
import confetti from "canvas-confetti";
import words from "../words.json";

export default {
  components: {
    Words,
    Keyboard,
  },
  data() {
    return {
      currentRow: 1,
      indexRow: 1,
      activeIndex: 1,
      lettersIndex: [],
      correctLetters: [],
      existingLetters: [],
      notExistingLetters: [],
    };
  },
  methods: {
    keyPressed(letter) {
      if (letter != "Backspace" && letter != "Enter") {
        if (this.lettersIndex.length / this.currentRow == 5) return;

        this.lettersIndex.push({
          letter: letter,
          index: this.activeIndex,
          indexRow: this.indexRow,
          row: this.currentRow,
        });

        console.log(Math.ceil((this.activeIndex + 1) / 5));
        console.log(this.currentRow);

        this.activeIndex++;
        this.indexRow++;

        return;
      }

      if (letter === "Backspace") {
        const letterToRemove = this.lettersIndex[this.lettersIndex.length - 1];

        if (letterToRemove.row != this.currentRow) return;

        this.lettersIndex.pop();
        this.activeIndex--;
        this.indexRow--;
        return;
      }

      if (letter == "Enter") {
        this.checkWord();
      }
    },
    checkWord() {
      const word = this.lettersIndex
        .filter((letter) => letter.row == this.currentRow)
        .map((letter) => letter.letter)
        .join("")
        .toLocaleLowerCase();

      if (word.length < 5) return;

      const hiddenWord = this.hiddenWord;

      if (word == hiddenWord) {
        this.correctWord(word);
        return;
      }

      if (this.currentRow == 6) {
        this.resetGame();
        return;
      }

      for (let i = 0; i < word.length; i++) {
        const letter = this.lettersIndex.find((letter) => letter.indexRow == i + 1 && letter.row == this.currentRow);

        if (word[i] == hiddenWord[i]) {
          letter.correctPos = true;
          this.correctLetters.push(letter.letter);
          this.lettersIndex = [...this.lettersIndex];
        } else if (hiddenWord.includes(word[i])) {
          letter.letterExists = true;
          this.existingLetters.push(letter.letter);
          this.lettersIndex = [...this.lettersIndex];
        } else {
          letter.letterExists = false;
          this.notExistingLetters.push(letter.letter);
          this.lettersIndex = [...this.lettersIndex];
        }
      }

      this.indexRow = 1;
      this.currentRow++;

      if (this.currentRow == 6) this.defeated();
    },
    correctWord(word) {
      for (let i = 0; i < word.length; i++) {
        const letter = this.lettersIndex.find((letter) => letter.indexRow == i + 1 && letter.row == this.currentRow);

        letter.correctPos = true;
        this.lettersIndex = [...this.lettersIndex];
      }

      this.showConfetti();
    },
    resetGame() {
      document.getElementById("my-modal-6").checked = true;

      this.currentRow = 1;
      this.indexRow = 1;
      this.activeIndex = 1;
      this.lettersIndex = [];
      this.correctLetters = [];
      this.existingLetters = [];
      this.notExistingLetters = [];
    },
    setActiveIndex(index) {
      this.activeIndex = index;
    },
    showConfetti() {
      const confettiCanvas = document.getElementById("confetti-holder");

      const myConfetti = confetti.create(confettiCanvas, {
        resize: true,
      });

      myConfetti({
        particleCount: 100,
        spread: 70,
      });
    },
  },
  computed: {
    hiddenWord() {
      return words[Math.round(Math.random() * words.length)];
    },
  },
};
</script>
