<template>
  <div class="main">
    <div class="main__row row mb-3">
      <div
        v-for="(item, key) in valueFractions"
        :key="item.id"
        class="main__item"
      >
        <span v-if="key > 0" class="main__plus main_big-text mx-3">+</span>
        <fraction
          :view-close="valueFractions.length > 2"
          @input="changeFraction($event, item.id)"
          @delete="removeFraction(item.id)"
        />
      </div>
      <span class="main__equally main_big-text mx-3">=</span>
      <span class="main__answer">
        <div v-if="answer">
          <span class="main__answer-numerator text-center">
            {{ answer.numerator }}
          </span>
          <hr class="main__line" />
          <span class="main__answer-denominator text-center">
            {{ answer.denominator }}
          </span>
        </div>
        <span v-else class="main__zero main_big-text">0</span>
      </span>
    </div>
    <div class="main__row">
      <b-button @click="addNewFraction(1)">Add new element</b-button>
    </div>
  </div>
</template>

<script>
import Fraction from '~/components/Fraction.vue'
export default {
  components: { Fraction },

  data() {
    return {
      idFraction: 0,
      valueFractions: [],
      answerObj: {
        numerator: null,
        denominator: null,
      },
    }
  },

  computed: {
    answer() {
      if (this.answerObj.numerator && this.answerObj.denominator) {
        let numerator = this.answerObj.numerator
        let denominator = this.answerObj.denominator
        for (let i = 2; i <= this.answerObj.numerator; i++) {
          if (
            this.answerObj.numerator % i === 0 &&
            this.answerObj.denominator % i === 0
          ) {
            numerator = this.answerObj.numerator / i
            denominator = this.answerObj.denominator / i
          }
        }

        return { numerator, denominator }
      }

      return false
    },
  },

  watch: {
    valueFractions: {
      handler() {
        try {
          const answer = this.valueFractions.reduce((obj, val, index) => {
            if (!val.numerator && !val.denominator) throw new Error('error')
            if (index !== 0) {
              if (obj.denominator === val.denominator) {
                return {
                  numerator: Number(obj.numerator) + Number(val.numerator),
                  denominator: Number(obj.denominator),
                }
              } else {
                return {
                  numerator:
                    Number(obj.numerator) * Number(val.denominator) +
                    Number(val.numerator) * Number(obj.denominator),
                  denominator:
                    Number(obj.denominator) * Number(val.denominator),
                }
              }
            }
            return { numerator: val.numerator, denominator: val.denominator }
          }, {})

          this.answerObj.numerator = answer.numerator
          this.answerObj.denominator = answer.denominator
        } catch (error) {
          this.answerObj.numerator = null
          this.answerObj.denominator = null
        }
      },
      deep: true,
    },
  },

  created() {
    this.addNewFraction(2)
  },

  methods: {
    changeFraction(valueFraction, id) {
      const indexFraction = this.valueFractions.findIndex(
        (item) => item.id === id
      )
      if (indexFraction || indexFraction === 0) {
        this.valueFractions[indexFraction].numerator = valueFraction.numerator
        this.valueFractions[indexFraction].denominator =
          valueFraction.denominator
      }
    },
    addNewFraction(count = 1) {
      if (this.valueFractions.length <= 4) {
        for (let i = 1; i <= count; i++) {
          this.valueFractions.push({
            id: this.idFraction,
            numerator: null,
            denominator: null,
          })

          this.idFraction += 1
        }
      }
    },
    removeFraction(idFraction) {
      if (this.valueFractions.length > 2) {
        this.valueFractions = this.valueFractions.filter(
          (item) => item.id !== idFraction
        )
      }
    },
  },
}
</script>

<style lang="scss">
.main {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;

  &__row {
    align-items: center;
  }

  &__item {
    display: flex;
    align-items: center;
  }

  &_big-text {
    font-size: 40px;
  }

  &__line {
    border-color: #333;
  }
}
</style>
