<template>
  <div :class="['keyboard', `keyboard--${mode}`]">
    <div class="keyboard__header">
      <h1 class="keyboard__title">
        {{
        mode === 'additionSubtraction' ? 'Addition & Subtraction'
        : (mode === 'multiplication' ? 'Multiplication' : 'Division')
        }}
      </h1>

      <div class="mode-select btn-group" role="group" aria-label="Select mode">
        <button
          :class="['btn', {'active': mode === 'additionSubtraction'}]"
          type="button"
          @click="mode = 'additionSubtraction'"
        >&plusmn;</button>
        <button
          :class="['btn', {'active': mode === 'multiplication'}]"
          type="button"
          @click="mode = 'multiplication'"
        >&times;</button>
        <button
          :class="['btn', {'active': mode === 'division'}]"
          type="button"
          @click="mode = 'division'"
        >&divide;</button>

        <div class="controls" v-click-outside="closeControls">
          <button
            :class="['btn', {'active': showRange}]"
            type="button"
            @click="showRange = !showRange"
          >&vellip;</button>

          <transition name="slide">
            <div v-show="showRange" class="controls__menu" role="menu">
              <label for="range-value">Range</label>
              <input
                v-model.number="range"
                id="range-value"
                type="number"
                min="1"
                :max="max"
                step="1"
                :placeholder="`1 - ${max}`"
                size="2"
              >
              <label for="show-hints">Show Hints</label>
              <input v-model="showHints" id="show-hints" type="checkbox">
            </div>
          </transition>
        </div>
      </div>
    </div>

    <table :class="['keypad', `range--${range}`, {'show-hints': showHints}]">
      <tbody>
        <tr v-for="col in range" :key="col">
          <td v-for="row in range" :key="row" :class="['keypad__key', cellClass(col, row)]">
            <template v-if="mode === 'multiplication'">
              <button class="btn" type="button" v-html="`${col} &times; ${row}`"></button>
              <output class="answer">{{ +col * row }}</output>
            </template>
            <template v-else-if="mode === 'division' && row > 0">
              <button class="btn" type="button" v-html="`${col} &divide; ${row}`"></button>
              <output v-html="toFraction(+col / row)" class="answer"></output>
            </template>
            <template v-else-if="col - row >= 1">
              <button class="btn" type="button">{{ `${col} - ${row}` }}</button>
              <output class="answer">{{ +col - row }}</output>
            </template>
            <template v-else>
              <button class="btn" type="button">{{ `${col} + ${row}` }}</button>
              <output class="answer">{{ +col + row }}</output>
            </template>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>


<script>
import fracty from "fracty";
import ClickOutside from "vue-click-outside";

export default {
  name: "MathKeyboard",
  props: {
    max: {
      type: Number,
      required: false,
      default: () => 12,
      validator: v => v > 1 && v <= 100
    }
  },
  data: () => ({
    mode: "additionSubtraction",
    showHints: true,
    showRange: false,
    range: 9
  }),
  methods: {
    cellClass(col, row) {
      if (this.mode === "multiplication") {
        return this.mode;
      }

      if (this.mode === "division") {
        return (col / row) % 1 === 0 ? "whole" : "fraction";
      }

      return col - row >= 1 ? "subtraction" : "addition";
    },
    toFraction: n =>
      n % 1 === 0
        ? n /// Is integer
        : `${fracty(n)}`.replace(
            /(\d)\/(\d)$/gm,
            `<sup>$1</sup>&frasl;<sub>$2</sub>`
          ),
    closeControls() {
      this.showRange = false;
    }
  },
  directives: {
    ClickOutside
  }
};
</script>

<style lang="scss">
@import "../assets/css/keyboard";
</style>

