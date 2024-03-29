<template>
  <section id="mainboard" class="mainboard">
    <side-menu class="side-menu"></side-menu>
    <section class="main-section">
      <navigation-bar>
      </navigation-bar>
      <search-bar
        @getSearchText="getSearchText"
        @editListItem="editListItem"
      >
      </search-bar>
      <adjustment-bar
        :cursors-config="cursorsConfig"
        @changeRangeValue="changeRangeValue"
      >
      </adjustment-bar>
      <cursor-content
        :filtered-cursors-data="intersectCursorsData"
        :cursors-config="cursorsConfig"
      >
      </cursor-content>
      <footer-content></footer-content>
    </section>
  </section>
</template>

<script>
  import SideMenu from '@/components/sidemenu.vue'
  import NavigationBar from '@/components/navigation.vue'
  import SearchBar from '@/components/search-bar.vue'
  import AdjustmentBar from '@/components/adjustment-bar.vue'
  import CursorContent from '@/components/cursor-content.vue'
  import FooterContent from '@/components/footer.vue'

  import CursorsData from '@/json/cursors_data.json'
  
  export default {
    name: 'MainBoardPage',
    components: {
      'side-menu': SideMenu,
      'navigation-bar': NavigationBar,
      'search-bar': SearchBar,
      'adjustment-bar': AdjustmentBar,
      'cursor-content': CursorContent,
      'footer-content': FooterContent
    },
    data() {
      return {
        cursorsConfig: {
          from: 'mainboard',
          size: 0,
          delay: 100
        },
        searchFilterText: '',
        searchedCursorsData: CursorsData,
        filteredCursorsData: CursorsData
      }
    },
    computed: {
      /**
       * Intersection: 'searchedCursorsData' ∩ 'filteredCursorsData'
       * 
       * @see searchCursorsData()
       * @see filterCursorsData()
       */
      intersectCursorsData() {
        return Object.fromEntries(Object.entries(this.searchedCursorsData).filter(item => {
          return Object.entries(this.filteredCursorsData).map(item => item[0]).includes(item[0])
        }))
      }
    },
    methods: {
      /**
       * Change the property value of cursorsConfig by the data from the <range-bar>
       * 
       * @param {object} event
       * @event changeRangeValue
       */
      changeRangeValue(event) {
        switch (event.id) {
          case 'range-size':
            this.cursorsConfig.size = parseInt(event.value)
            break
          case 'range-delay':
            this.cursorsConfig.delay = parseInt(event.value)
            break
        }
      },
      /**
       * Get the search value from <search-bar>
       * 
       * @param {string} text
       * @event getSearchText
       */
      getSearchText(text) {
        this.searchFilterText = text
        this.searchCursorsData()
      },
      /**
       * Get the filter array from <filter-menu>
       * 
       * @param {array} list
       * @event editListItem
       */
      editListItem(list) {
        this.filterCursorsData(list)
      },
      /**
       * 1 | Convert CursorsData into an array
       * 2 | Search the items by the cursor name that matches the rule of the filter
       * 3 | Convert back into an object
       */
      searchCursorsData() {
        let filterRule = new RegExp(`${this.searchFilterText}`, 'gi')
        this.searchedCursorsData = Object.fromEntries(Object.entries(CursorsData).filter(([key, value]) => {
          if (key !== null && key !== undefined && key !== '') {
            return value.cursorName.replace(/\s/g, '').match(filterRule)
          }
        }))
      },
      /**
       * 1 | Convert CursorsData into an array
       * 2 | Filter the items by comparing two arrays
       * 3 | Compare if the 'CursorsData.features' array contains the item in 'taglist' array
       * 4 | Convert back into an object
       * 
       * @param {array} taglist
       * 
       * @example Two arrays comparing
       * 
       * CursorsData.features = ['Click', 'Rotate', 'Hover']
       * taglist = ['Click', 'Active', 'Rotate', 'Multiple']
       * 
       * The return value at the map function will be
       * [true, true, false]
       * If the array above contains 'true' value, put the item to 'filteredCursorsData'
       */
      filterCursorsData(taglist) {
        if (taglist.length !== 0) {
          this.filteredCursorsData = Object.fromEntries(Object.entries(CursorsData).filter(([key, value]) => {
            if (key !== null && key !== undefined && key !== '') {
              return value.features.map(tagItem => {
                return taglist.includes(tagItem)
              }).includes(true)
            }
          }))
        } else {
          this.filteredCursorsData = CursorsData
        }
        
      }
    },
    metaInfo: {
      title: 'Cursor Library',
      titleTemplate: '%s | Curzr'
    }
  }
</script>

<style lang="scss">
@import '../style/main.scss';
.mainboard {
  display: flex;
  min-height: 100vh;
  background-color: #fcfcfc;

  .main-section {
    width: 100%;
    padding-left: $--sidemenu-width;
    transition: 500ms;
  }
}

@media only screen and (max-width: 1024px) {
  .mainboard {

    .main-section {
      padding-left: 0;
    }
  }
}
</style>