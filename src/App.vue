<template>
  <div id="app">
    <header class="header">
      <nav class="menu">
        <ul class="menu__list">
          <li class="menu__list-item">
            Карта
          </li>
          <li class="menu__list-item">
            Аналитика
          </li>
          <li class="menu__list-item">
            Отчет
          </li>
          <li class="menu__list-item" :class="!state ? 'menu__list-item_active' : ''" @click="state = (state + 1) % 2">
            ДТП
          </li>
          <li class="menu__list-item" :class="state ? 'menu__list-item_active' : ''" @click="state = (state + 1) % 2">
            Рейтинг ГО
          </li>
        </ul>
      </nav>
    </header>
    <form-ra v-if="state == 0"></form-ra>
    <table-rating v-if="state == 1"></table-rating>
  </div>
</template>

<script>
export default {
  name: 'App',
  components: {
    FormRa: () => import('./components/FormRa'),
    tableRating: () => import('./components/TableRating')
  },
  data: () => ({
    state: 0
  })
}
</script>

<style lang="scss">
html{
  box-sizing: border-box;
  user-select: none;
}
*,
*::before,
*::after{
  box-sizing: inherit;
}
body, ul{
  margin: 0;
  padding: 0;
}
#app {
  background-color: #eee;
  min-height: 100vh;
  font-family: Arial;
  font-weight: normal;
  font-style: normal;
  color: black;
  font-size: 11px;
}
.header{
  padding: 5px;
}
.menu{
  &__list-item{
    display: inline-block;
    border-radius: 3px;
    padding: 5px 10px;
    cursor: pointer;
    &:hover{
      background-color: #ccc;
    }
  }
  &__list-item_active{
    background-color: #ccc;
  }
  &__list-item + &__list-item{
    margin-left: 5px;
  }
}
.container{
  margin: 5px 15px;
}
</style>
