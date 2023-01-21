<template>
  <div>
    <div class="flex justify-center gap-1 md:gap-3" @keypress="handleKeyPress">
      <button
        v-for="letter in firstRow"
        :key="letter"
        :class="[
          classButton,
          letterCorrect(letter),
          letterExists(letter),
          letterNotTyped(letter),
          'w-8 h-10 md:w-10 md:h-12',
        ]"
        @click="handleClick(letter)"
      >
        {{ letter }}
      </button>
    </div>
    <div class="flex justify-center gap-1 md:gap-3 mt-2">
      <div
        v-for="letter in secondRow"
        :key="letter"
        :class="[
          classButton,
          letterCorrect(letter),
          letterExists(letter),
          letterNotTyped(letter),
          'w-8 h-10 md:w-10 md:h-12',
        ]"
        @click="handleClick(letter)"
      >
        {{ letter }}
      </div>
    </div>
    <div class="flex justify-center gap-1 md:gap-3 mt-2">
      <div
        v-for="letter in thirdRow"
        :key="letter"
        :class="[
          letterCorrect(letter),
          letterExists(letter),
          letterNotTyped(letter),
          singleLetter(letter),
          classButton,
        ]"
        @click="handleClick(letter)"
      >
        <div v-if="letter == 'Backspace'">
          <Icon icon="mdi:backspace-outline" width="28" />
        </div>
        <div v-else>
          {{ letter }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { Icon } from "@iconify/vue";

export default {
  components: { Icon },
  props: ["correctLetters", "existingLetters", "notExistingLetters"],
  data() {
    return {
      firstRow: ["Q", "W", "E", "R", "T", "Y", "U", "I", "O", "P"],
      secondRow: ["A", "S", "D", "F", "G", "H", "J", "K", "L"],
      thirdRow: ["Enter", "Z", "X", "C", "V", "B", "N", "M", "Backspace"],
      classButton:
        "text-sm md:text-base border-2 cursor-pointer text-black font-bold flex justify-center items-center rounded-md",
    };
  },
  methods: {
    handleClick(letter) {
      this.$emit("keyPressed", letter);
    },
    handleKeyPress(event) {
      let eventKey = event.key;

      // If the key is not an letter, and if its not enter or backspace, return
      if (!eventKey.match(/^[a-z]$/i) && eventKey !== "Enter" && eventKey !== "Backspace") return;

      // If the key is a letter, convert it to uppercase
      if (eventKey !== "Enter" && eventKey !== "Backspace") eventKey = eventKey.toUpperCase();

      this.handleClick(eventKey);
    },
    letterNotTyped(letter) {
      if (this.notExistingLetters.includes(letter)) return "bg-gray-500";
    },
    letterCorrect(letter) {
      if (this.correctLetters.includes(letter)) return "bg-green-400 :hover:bg-green-500";
    },
    letterExists(letter) {
      if (this.existingLetters.includes(letter) && !this.correctLetters.includes(letter)) {
        return "bg-orange-500 :hover:bg-orange-600";
      }
    },
    singleLetter(letter) {
      if (letter.length == 1) return "w-8 h-10 md:w-10 md:h-12";
      return "w-16 h-10 md:w-20 md:h-12";
    },
  },
  created() {
    document.addEventListener("keydown", this.handleKeyPress);
  },
};
</script>
