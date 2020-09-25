<template>
  <div class="container">
    <div class="table">
      <h3 class="table__title">Рейтинг городских округов по уровню загрузки</h3>
        <div class="table__content">
          <div class="table__row">
            <div class="table__cell table__cell_title" v-for="i in headerTitles.length" :key="headerTitles[i-1]">
              {{headerTitles[i-1]}}
              <div class="table__title-img filter-img" @click="sortTable(i)">
                <div class="filter-img__up" :class="fstate == 'D' + i ? 'filter-img__up_active': ''"></div>
                <div class="filter-img__down" :class="fstate == 'U'+ i ? 'filter-img__down_active': ''"></div>
              </div>
            </div>
          </div>

          <div class="table__row" v-for="row of pageTable" :key="row.distSid">
            <div class="table__cell">{{row.name}}</div>
            <div class="table__cell">{{+row.day}}</div>
            <div class="table__cell">{{+row.week}}</div>
            <div class="table__cell">{{+row.month}}</div>
          </div>

          <div class="table__row" v-for="i in (rowsInPage - pageTable.length)" :key="-1 * i">
            <div class="table__cell"></div>
            <div class="table__cell"></div>
            <div class="table__cell"></div>
            <div class="table__cell"></div>
          </div>
        </div>

      <div class="pages">
        <div class="pages__btn" @click="prevPage()">&lt;</div>
        <div class="pages__btn" :class="page == pnum ? 'pages__btn_active' : ''" 
        v-for="pnum in countPages" :key="pnum"
        @click="setPage(pnum)"
        >
          {{pnum}}
        </div>
        <div class="pages__btn" @click="nextPage()">&gt;</div>
      </div>
      
    </div>
  </div>
</template>

<script>
export default {
  name: 'TableRating',
  data: () => ({
    headerTitles: ['Городской округ', 'Прошедшие сутки', 'Прошедшая неделя', 'Прошедший месяц'],
    table: [],
    page: 1,
    fstate: '',
    rowsInPage: 15
  }),
  async mounted() {
    let fileData = await require('../assets/table.json')
    this.table = fileData.districtTables
    this.sortU1()
  },
  computed: {
    pageTable() {
      return this.table.slice((this.page - 1) * this.rowsInPage, this.page * this.rowsInPage)
    },
    countPages() {
      return Math.floor(this.table.length / 15) + 1
    }
  },
  methods: {
    sortTable(col) {
      if (col == 1) {
        if (this.fstate == 'U1') this.sortD1()
        else this.sortU1()
      } else if (col == 2) {
        if (this.fstate == 'U2') this.sortD2()
        else this.sortU2()
      } else if (col == 3) {
        if (this.fstate == 'U3') this.sortD3()
        else this.sortU3()
      } else {
        if (this.fstate == 'U4') this.sortD4()
        else this.sortU4()
      }
    },
    sortU1() {
      this.table = this.table.sort((x1, x2) => {
        let name1=x1.name.toLowerCase(), name2=x2.name.toLowerCase()
        if (name1 < name2)
          return -1
        if (name1 > name2)
          return 1
        return 0
      })
      this.fstate = 'U1'
    },
    sortD1() {
      this.table = this.table.sort((x1, x2) => {
        let name1= x1.name.toLowerCase(), name2 = x2.name.toLowerCase()
        if (name1 > name2)
          return -1
        if (name1 < name2)
          return 1
        return 0
      })
      this.fstate = 'D1'
    },
    sortU2() {
      this.table = this.table.sort((x1, x2) => {
        return x1.day - x2.day
      })
      this.fstate = 'U2'
    },
    sortD2() {
      this.table = this.table.sort((x1, x2) => {
        return x2.day - x1.day
      })
      this.fstate = 'D2'
    },
    sortU3() {
      this.table = this.table.sort((x1, x2) => {
        return x1.week - x2.week
      })
      this.fstate = 'U3'
    },
    sortD3() {
      this.table = this.table.sort((x1, x2) => {
        return x2.week - x1.week
      })
      this.fstate = 'D3'
    },
    sortU4() {
      this.table = this.table.sort((x1, x2) => {
        return x1.month - x2.month
      })
      this.fstate = 'U4'
    },
    sortD4() {
      this.table = this.table.sort((x1, x2) => {
        return x2.month - x1.month
      })
      this.fstate = 'D4'
    },
    setPage(num) {
      this.page = num
    },
    prevPage() {
      if (this.page > 1) this.page--
    },
    nextPage() {
      if (this.page < this.countPages) this.page++
    }
  }
}
</script>

<style lang="scss">
.table{
  width: 550px;

  &__title{
    text-align: center;
    font-weight: 700;
  }
  &__title-img{
    margin-left: 5px;
  }

  &__content{
    padding: 1px;
    background: #ccc;
  }

  &__row{
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    grid-column-gap: 1px;
  }
  &__row + &__row {
    margin-top: 1px;
  }

  &__cell{
    display: flex;
    height: 35px;
    justify-content: center;
    align-items: center;
    background: #eee;
    &_title{
      font-weight: 700;
    }
  }
}

.pages{
  margin-top: 10px;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;

  &__btn{
    color: #0037ff;
    padding: 5px 0;
    text-align: center;
    width: 100%;
    border: 1px solid #ccc;
    cursor: pointer;
  }
  &__btn_active{
    color: white;
    background: #0037ff;
    box-shadow: 0px 0px 6px 2px #6767f4;
  }
}

.filter-img{
  cursor: pointer;
  
  &__up{
    border: 3px solid transparent; border-bottom: 4px solid #ccc;
    margin-bottom: 1px;
    &_active{
      border-bottom-color: black !important;
    }
  }
  &__down{
    border: 3px solid transparent; border-top: 4px solid #ccc;
    &_active{
      border-top-color: black !important;
    }
  }
}
</style>
