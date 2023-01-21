<template>
  <div class="mx-auto">
    <div class="flex justify-center h-96 w-92 my-28">
      <div class="grid grid-cols-5 gap-2">
        <div
          v-for="index in 30"
          :key="index"
          class="h-16 w-16 border-2 text-3xl flex justify-center items-center"
          :class="[correctPos(index), letterExists(index), activeIndex == index ? 'border-4' : '']"
          @click="selectIndex(index)"
        >
          {{ getLetterIndex(index) }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    activeIndex: {
      type: Number,
      default: 1,
    },
    lettersIndex: {
      type: Object,
    },
    currentRow: {
      type: Number,
      default: 1,
    },
  },
  data() {
    return {
      rows: [1, 2, 3, 4, 5],
    };
  },
  methods: {
    getLetterIndex(index) {
      const letterIdx = this.lettersIndex.find((letterIndex) => letterIndex.index == index);
      return letterIdx ? letterIdx.letter : "";
    },
    correctPos(index) {
      const row = Math.ceil(index / 5);
      const letterIdx = this.lettersIndex.find((letterIndex) => letterIndex.index == index && letterIndex.row == row);

      if (!letterIdx) return;
      return letterIdx.correctPos ? "bg-green-400" : "";
    },
    letterExists(index) {
      const row = Math.ceil(index / 5);
      if (this.currentRow == row) return;

      const letterIdx = this.lettersIndex.find((letterIndex) => letterIndex.index == index && letterIndex.row == row);

      if (!letterIdx) return;
      return letterIdx.letterExists ? "bg-orange-500" : "bg-gray-500";
    },

    selectIndex(index) {
      const row = Math.ceil(index / 5);
      if (this.currentRow !== row) return;

      this.$emit("setIndex", index);
    },
  },
};
</script>
