<template>
    <div>
        <form class="card comment-form">
            <div class="card-block">
                <textarea class="form-control" placeholder="Write a comment..." rows="3"></textarea>
            </div>
            <div class="card-footer">
                <img src="http://i.imgur.com/Qr71crq.jpg" class="comment-author-img" />
                <button class="btn btn-sm btn-primary">
                    Post Comment
                </button>
            </div>
        </form>

        <div class="card" v-for="comment of comments" :key="comment.id">
            <div class="card-block">
                <p class="card-text">{{ comment.body }}
                </p>
            </div>
            <div class="card-footer">
                <nuxt-link :to="{
                    name: 'profile',
                    params: {
                        username: comment.author.username
                    }
                }" class="comment-author">
                    <img :src="comment.author.image" class="comment-author-img" />
                </nuxt-link>
                &nbsp;
                <nuxt-link :to="{
                    name: 'profile',
                    params: {
                        username: comment.author.username
                    }
                }" class="comment-author">{{ comment.author.username }}</nuxt-link>
                <span class="date-posted">{{ comment.createdAt | date('MMMM DD,YYYY') }}</span>
            </div>
        </div>
    </div>
</template>

<script>
import { getComments } from '@/api/article'
export default {
    name: 'ArticleComments',
    props: {
        article: {
            type: Object,
            required: true
        }
    },
    data () {
        return {
            comments: [],
        }
    },
    mounted () {
        this.initComments(this.article.slug)
    },
    methods: {
        async initComments (slug) {
            const { data } = await getComments(slug)
            this.comments = data.comments;
            console.log(this.comments)
        }
    }
}
</script>

<style>

</style>