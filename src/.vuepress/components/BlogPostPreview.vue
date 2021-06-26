<script>
export default {
    name: 'BlogPostPreview',
    props: {
        item: {
            type: Object,
            required: true
        },
        addTag: {
          type: Function,
          required: true
        }
    },
    computed: {
        formatPublishDate() {
            const dateFormat = new Date(this.item.frontmatter.date)
            const options = {
                year: 'numeric',
                month: 'long',
                day: 'numeric'
            }

            return dateFormat.toLocaleDateString('ja', options)
        }
    }
}
</script>

<template>
	<section>
        <router-link class="link" :to="item.path">
          <h3 class="blog-post__title">{{ item.frontmatter.title }}</h3>
        </router-link>
        <time>{{ formatPublishDate }}</time>
        <span v-for="tag in item.frontmatter.tags" :key="tag.key" class="blog-list__tags">
            <a class="blog-list__btn" @click="addTag(tag)">{{ tag }}, </a>
        </span>
    </section>
</template>

<style lang="stylus" scoped>
.blog-post__title
  margin: 0.5rem 0;
  font-size: 1.5rem;
  @media (max-width: $MQMobileNarrow)
    font-size 1.2rem

.blog-list__tags
    list-style: none;
    font-size: 1.2rem;

.blog-list__btn
  cursor: pointer;

</style>
