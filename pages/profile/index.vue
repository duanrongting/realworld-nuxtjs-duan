<template>
    <div class="profile-page">

        <div class="user-info">
            <div class="container">
                <div class="row">

                    <div class="col-xs-12 col-md-10 offset-md-1">
                        <img :src="user.image" class="user-img" />
                        <h4>{{ user.username }}</h4>
                        <!-- <p>
                            Cofounder @GoThinkster, lived in Aol's HQ for a few months, kinda looks like Peeta from the
                            Hunger Games
                        </p> -->
                        <button class="btn btn-sm btn-outline-secondary action-btn"
                            :class="{
                                active: user.following
                            }"
                        >
                            <i class="ion-plus-round"></i>
                            &nbsp;
                            Follow {{ user.username }}
                        </button>
                    </div>

                </div>
            </div>
        </div>

        <div class="container">
            <div class="row">

                <div class="col-xs-12 col-md-10 offset-md-1">
                    <div class="articles-toggle">
                        <ul class="nav nav-pills outline-active">
                            <li class="nav-item">
                                <nuxt-link class="nav-link" 
                                    :class="{
                                        active: tab === 'author'
                                    }"
                                    exact
                                    :to="{
                                        name: 'profile',
                                        query: {
                                            author: user.username,
                                            tab: 'author'
                                        }
                                    }">My Articles</nuxt-link>
                            </li>
                            <li class="nav-item">
                                <nuxt-link class="nav-link"
                                    :class="{
                                        active: tab === 'favorited'
                                    }"
                                    exact 
                                    :to="{
                                         name: 'profile',
                                         query: {
                                             favorited: user.username,
                                             tab: 'favorited'
                                         }
                                    }"
                                    >Favorited Articles</nuxt-link>
                            </li>
                        </ul>
                    </div>

                    <div class="article-preview" v-for="article in articles" :key="article.slug">
                        <div class="article-meta">
                            <nuxt-link :to="{
							  name: 'profile',
							  params: {
								  username: article.author.username
							  }
						  }">
                                <img :src="article.author.image" />
                            </nuxt-link>
                            <div class="info">
                                <nuxt-link class="author" :to="{
								  name: 'profile',
								  params: {
									  username: article.author.username
								  }
							  }">{{ article.author.username }}</nuxt-link>
                                <span class="date">{{ article.createdAt | date('MMMM DD, YYYY')}}</span>
                            </div>
                            <button class="btn btn-outline-primary btn-sm pull-xs-right" :class="{
							  active: article.favorited
							}" @click="onFavorite(article)" :disabled="article.favoriteDisabled">
                                <i class="ion-heart"></i> {{ article.favoritesCount }}
                            </button>
                        </div>
                        <nuxt-link class="preview-link" :to="{
						  name: 'article',
						  params: {
							  slug: article.slug
						  }
					  }">
                            <h1>{{ article.title }}</h1>
                            <p>{{ article.description }}</p>
                            <span>Read more...</span>
                        </nuxt-link>
                    </div>


                </div>

            </div>
        </div>

    </div>
</template>

<script>
import {
    getProfile
} from '@/api/profile'
import {
    getArticles
} from '@/api/article'

export default {
	middleware: 'authenticated',
    name: 'UserProfile',
    
    data () {
        return {
            user: {},
        }
    },
    mounted () {
        this.initProfile(this.$route.params.username)
        
    },
    methods: {
        async initProfile (name) {
            const { data } = await getProfile(name)
            this.user = data.profile
        }
    },
    async asyncData ({ query }) {
        const limit = 20;
        const page = Number.parseInt(query.page || 1);
        const tab = query.tab || 'author'
        let reqData = {}
        if(query.author){
            reqData = {
                limit,
                offset: (page - 1) * limit,
                author: query.author || user.username
            }
        }else{
            reqData = {
                limit,
                offset: (page - 1) * limit,
                favorited: query.favorited
            }
        }
        let { data } = await getArticles(reqData)
        let { articles } = data
        console.log(tab)
        console.log(articles)
        return {
            articles,
            tab,
            page
        }
    },
    watchQuery: ['page','tab'],
}
</script>

<style>

</style>