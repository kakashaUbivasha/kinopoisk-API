<template>
  <div class="k-serials" v-if="!IS_LOADING">
    <h1 class="font-bold text-4xl" style="margin-bottom: 20px; text-align: center">Сериалы</h1>
    <a-select
        :title="genres"
        :options = 'categories'
        @select="sortedByCategories"
        :selected = selected
        class="v-select"
    />
    <a-select
        :title="yearsTitle"
        :options = 'years'
        @select="sortedByYears"
        :selected = selectedYear
        class="select-year"
    />
    <div class="k-serials-list">
  <k-serials-item
  v-for="serial in SERIALS"
  :films_docs="serial"
  />
    </div>
    <div class="mt-5 flex ml-20">
      <paginate
          v-model="page"
          @click="reworkPage()"
          :next-text="nextText"
          :prev-text="prevText"
          :page-range="3"
          :margin-pages="2"
          :page-count="totalPage"
          :container-class="'flex items-center p-2'"
          :page-class="'paginate_button text-black rounded-lg border border-light_purple mx-1 h-8 w-8 cursor-pointer flex items-center justify-center '"
      />
    </div>
  </div>
  <div v-else>
    <loading/>
    <div class="mt-[100px] uppercase font-bold text-3xl">
      загрузка
    </div>
  </div>
</template>

<script>
import {mapActions, mapGetters} from "vuex";
import kSerialsItem from "@/components/serials/k-serials-item.vue";
import aSelect from '@/layout/a-select.vue'
import aPage from "@/layout/a-page.vue";
import Paginate from "vuejs-paginate-next";
import Loading from "@/layout/loading.vue";
export default {
  data(){
    return{
      nextText: `<span class="material-symbols-outlined cursor-pointer">
arrow_forward_ios
</span>`,
      prevText:`<span class="material-symbols-outlined cursor-pointer">
arrow_back_ios_new
</span>`,
      page: 1,
      totalPage: 120,
      categories: [
        {name:'Драма', value:'dr'},
        {name:'Комедия', value:'cm'},
        {name:'Криминал', value:'cm'},
        {name:'Аниме', value:'cm'},
        {name:'Мелодрама', value:'cm'},
      ],
      selected: 'Выберите жанр',
      genres: 'Жанры',
      yearsTitle: 'Год выхода',
      years: [
        {name:'2023', value:'1'},
        {name:'2022', value:'2'},
        {name:'2021', value:'3'},
        {name:'2020', value:'4'},
        {name:'2019', value:'5'},
      ],
      selectedYear: 'Выберите год'
    }
  },
  components: {Loading, Paginate, aPage, kSerialsItem,aSelect},
  computed:{
    ...mapGetters(['SERIALS','IS_LOADING'])
  },
  methods:{
    reworkPage(){
      this.GET_PAGE_VALUE(this.page)
    },
    sortedByCategories(category){
      this.selected = category.name
    },
    sortedByYears(year){
      this.selectedYear = year.name
    },
    ...mapActions(['GET_SERIALS','SET_CATEGORIES_TO_API','SET_YEARS_TO_API','GET_PAGE_VALUE'])
  },

  watch:{
  selected(){
    this.SET_CATEGORIES_TO_API(this.selected.toLowerCase())
    this.GET_SERIALS()
  },
    selectedYear(){
      this.SET_YEARS_TO_API(this.selectedYear)
      this.GET_SERIALS(this.selected)
    },
    page(){
      this.reworkPage()
      this.GET_SERIALS(this.selected)
    }
  },
  mounted() {
    this.selected = 'Выберите жанр'
    this.selectedYear='Выберите год'
    this.page=1
    this.reworkPage()
    this.GET_SERIALS(this.selected)
  }
}
</script>

<style lang="scss">
.k-serials-list{
  display: grid;
  grid-template-columns: repeat(2,1fr);
  gap: 60px;
}
.k-serials{
  margin-top: 60px;
}
.v-select{
  z-index: 20;
  position: absolute;
  top: 200px;
  right: 100px;
}
.page-link{
  padding: 9px;
}

</style>