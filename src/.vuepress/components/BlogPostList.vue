<script>
export default {
    name: 'BlogPostList',
    props: {
        pages: {
            type: Array,
            default: []
        },
        pageSize: {
            type: Number,
            default: 5
        },
        startPage: {
            type: Number,
            default: 0
        }
    },
    data() {
        return {
            currentPage: Math.ceil(this.startPage / this.pageSize),
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
                    // check if tags contain all of the selected tags
                    const hasTags = !!item.frontmatter.tags && this.selectedTags.every((tag) => item.frontmatter.tags.includes(tag))

                    if (!isBlogPost || (this.selectedTags.length > 0 && !hasTags) || !isCurrentLocale){
                        return false
                    }

                    return true

                }).sort((a, b) => new Date(b.frontmatter.date) - new Date(a.frontmatter.date))
            }
        },

        totalPages() {

            return Math.ceil(this.filteredList.length / this.pageSize)
        },
    },

    mounted() {
        this.currentPage =  Math.min(Math.max(this.currentPage, 0), this.totalPages - 1)
    },

    methods: {
        nextPage() {
            this.currentPage = this.currentPage >= this.totalPages - 1 ? this.totalPages - 1 : this.currentPage + 1
        },
        previousPage() {
            this.currentPage = this.currentPage < 0 ? 0 : this.currentPage - 1
        },
        addTag(tag) {
            const tagExists = this.selectedTags.some(item => {
                return item === tag
            })

            if (!tagExists){
                this.selectedTags = this.selectedTags.concat(tag)
            }
        },
        removeTag(tag) {
            this.selectedTags.filter(t => t != tag)
        },
        resetTags(){
            this.selectedTags = []
        }
    }
}
</script>

<template>
	<div>
        <div
            v-if="selectedTags.length > 0"
            class="filtered-heading"
        >
            <h2>
                <strong>タグ</strong> {{ selectedTags.join(',') }}
            </h2>
            <button
                type="button"
                @click="resetTags"
                class="btn clear-filter-btn"
            >
                クリア
            </button>
        </div>
        <ul class="blog-list">
            <li v-for="(item, index) in filteredList" class="blog-list__item">
                <BlogPostPreview
                    :item="item"
                    :addTag="addTag"
                />
            </li>
        </ul>

        <!-- <div class="pagination">
            <button v-show="currentPage > 0"
                @click="previousPage"
                class="button--pagination"
                type="button"
            >
                Previous
            </button>
            <button v-show="currentPage < totalPages - 1"
                @click="nextPage"
                class="button--pagination"
                type="button"
            >
                Next
            </button>
        </div> -->
    </div>
</template>

<style scoped>
.blog-list {
	padding: 0;
	margin: 0;
  border: 1px solid #eee;
  border-radius: 8px;
  box-shadow: 0px 4px 8px 2px rgba(0,0,0,0.04);
}

.blog-list__item {
	list-style-type: none;
  border-bottom: 1px solid #eee;
  padding: 2rem;
}
.blog-list__item:last-child{
  border-bottom: none;
}

.button--pagination {
	background-color: #32c8cf;
	border-radius: 4px;
	color: #fff;
	font-size: 0.8rem;
	padding: 0.5rem 0.75rem;
	text-transform: uppercase;
	font-weight: 700;
	box-shadow: 0 0;
	transition: background-color 0.2s ease-in, color 0.2s ease-in;
}

.button--pagination:hover {
    background-color: #fff;
    border: 1px solid #32c8cf;
    border-radius: 4px;
    color: #32c8cf;
}

.clear-filter-btn {
    align-self: center;
    margin-left: 20px;
}

.filtered-heading {
    display: flex;
}

.pagination {
    text-align: center;
}
</style>
