<template>
  <div class="container">
    <div class="form-ra">
      <div class="form-ra__form" action="post">

        <div class="form-ra__input-con" v-for="inp of inputs" :key="inp.name">
          <span v-if="inp.type == 'num'">
            <p class="form-ra__title">{{inp.title}}</p>
            <input class="form-ra__input" :class="!inp.valid && inp.touched ? 'form-ra__input_invalid' : ''" 
            type="number" v-model="inp.value" @blur="checkInput(inp.name)">
            <img class="form-ra__input-wrn" src="../assets/warning.svg" v-show="!inp.valid && inp.touched">
          </span>

          <span v-else-if="inp.type == 'date'">
            <p class="form-ra__title">{{inp.title}}</p>
            <div class="form-ra__date">
              <input class="form-ra__input-date" required type="date" v-model="inp.value"
              min="2020-01-01" max="2020-12-31" @blur="checkInput(inp.name)">
            </div>
          </span>
        </div>

        <div class="form-ra__btn-con">
          <div class="btn" :class="!isFormValid ? 'btn_disabled' : ''" @click="sendForm()">
            Отправить
          </div>
        </div>

      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: 'FormRa',
  data: () => ({
    inputs: [
      {
        type: 'num',
        name: 'countRa',
        title: 'Количество ДТП',
        touched: false,
        value: '',
        valid: false
      },
      {
        type: 'num',
        name: 'countWound',
        title: 'Количество раненых',
        touched: false,
        value: '',
        valid: false
      },
      {
        type: 'num',
        name: 'countLost',
        title: 'Количество погибших',
        touched: false,
        value: '',
        valid: false
      },
      {
        type: 'date',
        name: 'inputDate',
        title: 'Дата',
        value: '',
        valid: false
      }
    ]   
  }),
  mounted() {
    document.getElementsByClassName('form-ra__input').forEach(i => i.onkeydown = function (e) {
      return !(/^[-+e.]$/.test(e.key))
    })
  },
  computed: {
    isFormValid() {
      for (let inp of this.inputs) {
        if (!inp.valid) return false
      }
      return true
    }
  },
  methods: {
    checkInput(name) {
      let inp = this.inputs.find(inp => inp.name == name)

      if (inp) {
        inp.touched = true
        inp.valid = !!inp.value.length
      }
    },
    async sendForm() {
      let params = {}
      this.inputs.forEach(inp => params[inp.name] = inp.value)

      await axios({
        method: 'POST',
        url: '/endpoint',
        data: JSON.stringify(params),
        headers: {
          'Content-Type': 'application/json'
        },
        json: true
      })
      .then(res => console.log(res))
      .catch(err => console.log(err.message))
    }
  }
}
</script>

<style lang="scss">
.form-ra{
  max-width: 30%;
  max-height: 50%;

  &__input-con{
    position: relative;
  }
  &__input-con + &__input-con, &__btn-con{
    margin-top: 10px;
  }

  &__title{
    margin: 0 0 5px;
    text-align: center;
  }

  &__input {
    -moz-appearance: textfield;
  }
  &__input:focus{
    outline: none;
  }
  &__input::-webkit-outer-spin-button,
  &__input::-webkit-inner-spin-button {
      -webkit-appearance: none;
  }
  &__input, &__input-date{
    width: 100%;
    text-align: center;
  }

  &__date {
    position: relative;
    background: #fff;
  }
  &__input-date:valid::before {
    display: none;
  }
  &__input-date::before {
    content: "Введите дату";
    white-space: nowrap;
    position: absolute;
    top: 2px;
    right: 2px;
    bottom: 2px;
    left: 10px;
    background: white;
  }

  &__input_invalid{
    border-color: #ff3333 ;
  }
  &__input-wrn{
    width: 17px;
    height: 17px;
    right: 3px;
    bottom: 3px;
    position: absolute;
  }

  &__btn-con{
    display: flex;
    justify-content: center;
  }
}

::-webkit-calendar-picker-indicator{
  cursor: pointer;
  position: absolute;
  left: -20px;
}

.btn{
  text-align: center;
  padding: 5px 10px;
  border-radius: 3px;
  cursor: pointer;
  border: 1px solid #00b4ff;
  color: #00b4ff;
  &_disabled{
    opacity: 0.3;
  }
}
</style>
