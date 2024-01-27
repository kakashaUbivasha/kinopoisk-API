<template>
  <div class="k-films" v-if="!IS_LOADING">
    <h1 class="font-bold text-4xl"  style="margin-bottom: 20px; text-align: center">Фильмы</h1>
    <a-select
        :title="genres"
        :options = 'categories'
        @select="sortedByCategories"
        :selected = selected
        class="a-select"
    />
    <a-select
        :title="yearsTitle"
        :options = 'years'
        @select="sortedByYears"
        :selected = selectedYear
        class="select-year"
    />
    <div class="k-films-list">
    <k-serials-item
    v-for="film in GET_FILMS"
    :films_docs = film
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
          :container-class="'flex items-center'"
          :page-class="'paginate_button text-black  p-2 rounded-lg border border-light_purple mx-1 h-8 w-8 cursor-pointer flex items-center justify-center '"
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
import aSelect from "@/layout/a-select.vue";
import kSerialsItem from '@/components/serials/k-serials-item.vue'
import {mapActions, mapGetters} from "vuex";
import aPage from '@/layout/a-page.vue'
import Paginate from "vuejs-paginate-next";
import Loading from "@/layout/loading.vue";

export default {
  components: {aSelect, kSerialsItem,aPage,Paginate,Loading},
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
        {name:'Всё', value:'dr'},
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
      selectedYear: 'Выберите год',
      isLoading: false,
    }
  },
  methods:{
    changeNumber(pageNumber){
      this.page=pageNumber
    },
    sortedByCategories(category){
      this.selected = category.name
    },
    sortedByYears(year){
      this.selectedYear = year.name
    },
    ...mapActions(['SET_CATEGORIES_TO_API','GET_FILMS_FROM_API','SET_YEARS_TO_API','GET_PAGE_VALUE','GET_LOADING_STATUS']),
    reworkPage(){
      this.GET_PAGE_VALUE(this.page)
    }
  },
  computed:{
    ...mapGetters(['GET_FILMS','IS_LOADING','ALL_PAGES'])
  },
  watch:{
    selected(){
      this.SET_CATEGORIES_TO_API(this.selected.toLowerCase())
      this.GET_FILMS_FROM_API(this.selected.toLowerCase())

    },
    selectedYear(){
      this.SET_YEARS_TO_API(this.selectedYear)
      this.GET_FILMS_FROM_API(this.selected)
    },
    page(){
      this.reworkPage()
      this.GET_FILMS_FROM_API(this.selected)
    },
    ALL_PAGES(){
      this.totalPage = this.ALL_PAGES
    }
  },
  mounted() {

    this.selected = 'Выберите жанр'
    this.page=1
    this.reworkPage()
    this.selectedYear='Выберите год'
    this.GET_FILMS_FROM_API(this.selected)
  }
}
</script>

<style lang="scss">
.a-select{
  z-index: 20;
  position: absolute;
  top: 200px;
  right: 100px;
}
.k-films-list{
  display: grid;
  grid-template-columns: repeat(2,1fr);
  gap: 60px;
}
.k-films{
  margin-top: 60px;
  margin-bottom: 60px;
}
.select-year{
  position: absolute;
  top: 300px;
  z-index: 10;
}
.active{
  background: #181818;
  color: white;
}
</style>