<script>
export default {
    name: 'ArchiveList',
    props: {
        pages: {
            type: Array,
            default: []
        },
    },
    data() {
        return {
            selectedTags: []
        }
    },
    computed: {
        filteredList() {
            if (this.pages) {

                return this.pages.filter(item => {
                    const isBlogPost = !!item.frontmatter.blog

                    // check for locales
                    let isCurrentLocale = true;
                    if(this.$site.locales) {
                        const localePath = this.$route.path.split('/')[1] || "";
                        isCurrentLocale = item.relativePath.startsWith(localePath);
                    }
                    // check if tags contain any of the selected tags
                    // const hasTags = item.frontmatter.tags && item.frontmatter.tags.some(tag => this.selectedTags.includes(tag))
                    // check if tags contain all of the selected tags
                    const hasTags = !!item.frontmatter.tags && this.selectedTags.every((tag) => item.frontmatter.tags.includes(tag))

                    if (!isBlogPost ||  (this.selectedTags.length > 0 && !hasTags) || !isCurrentLocale){
                        return false
                    }

                    return true

                })
                .sort((a, b) => new Date(b.frontmatter.date) - new Date(a.frontmatter.date))
            }
        },
    },
    methods: {
        getYears: function() {
            return [...new Set(this.filteredList.map(item => new Date(item.frontmatter.date).getFullYear()))]
        },

        getMonths: function(year) {
            return [...new Set(this.filteredList.filter(item => new Date(item.frontmatter.date).getFullYear() == year).map( item => new Date(item.frontmatter.date).getMonth()))]
        },

        postsByDate(year, month) {
            return this.filteredList.filter(item => {
                const date = new Date(item.frontmatter.date)
                return date.getFullYear() == year && date.getMonth() == month
            })
        },
    },
    filters: {
       // Filter definitions
        monthToLongName(value) {
            const months = [ "1月", "2月", "3月", "4月", "5月", "6月",
           "7月", "8月", "9月", "10月", "11月", "12月" ];

           return months[value]
        }
    }
}
</script>

<template>
    <div class="archive">
      <div class="archive-wrap">
        <h3>ブログの投稿ログ</h3>
        <div class="archive-item" v-for="year in getYears()">
            <div class="archive-year">{{year}}年</div>
            <div v-for="month in getMonths(year)">
                <div class="archive-month">{{month | monthToLongName}}</div>
                <ul class="archive-list">
                    <li v-for="(item, index) in postsByDate(year, month)" class="archive-list__item">
                        {{new Date(item.frontmatter.date).getDate()}} 日<br />
                         <router-link :to="item.path">{{item.title}}</router-link>
                    </li>
                </ul>
            </div>
        </div>
      </div>
    </div>
</template>

<style lang="stylus">
@import '../styles/config.styl'

.archive
  width 30%;
  padding $navbarHeight 0 0
  @media (max-width: $MQNarrow)
    display none
  .archive-wrap
    padding 0 1rem
    border 1px solid #e5e5e5
    border-radius 8px
    margin 1.8rem 2rem 0 0
    box-shadow: 0px 4px 8px 0px rgba(0,0,0,0.04);
    h3
      margin 1rem 0
      font-size 1.4rem;
  .archive-item
    margin-bottom: 1rem;
  .archive-list
    padding 0
    margin 0

  .archive-year
    font-weight bold
    font-size  24px
    margin-bottom 0.4rem

  .archive-month
    font-weight bold
    font-size  20px
    margin-bottom 0.2rem

  .archive-list__item
    list-style-type none
    margin-bottom .6em
    font-size 1.2rem
    border-bottom 1px solid #e5e5e5
    a
      &:hover
        color #35495d
</style>
