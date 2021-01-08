<template>
    <div class="editor-page">
        <div class="container page">
            <div class="row">

                <div class="col-md-10 offset-md-1 col-xs-12">
                    <form>
                        <fieldset>
                            <fieldset class="form-group">
                                <input v-model="article.title" type="text" class="form-control form-control-lg" placeholder="Article Title">
                            </fieldset>
                            <fieldset class="form-group">
                                <input v-model="article.description" type="text" class="form-control" placeholder="What's this article about?">
                            </fieldset>
                            <fieldset class="form-group">
                                <textarea v-model="article.body" class="form-control" rows="8"
                                    placeholder="Write your article (in markdown)"></textarea>
                            </fieldset>
                            <fieldset class="form-group">
                                <input v-model="tag" type="text" class="form-control" placeholder="Enter tags" @keyup.enter="onSubmitTag">
                                <div class="tag-list">
                                    <span class="tag-default tag-pill ng-binding ng-scope" v-for="(tag,index) of article.tagList" :key="index">
                                        <i class="ion-close-round" @click="onDelTag(tag)"></i>
                                        {{ tag }}
                                    </span>
                                </div>
                            </fieldset>
                            <button class="btn btn-lg pull-xs-right btn-primary" type="button" @click="saveCreate" :disabled="disabled">
                                Publish Article
                            </button>
                        </fieldset>
                    </form>
                </div>

            </div>
        </div>
    </div>
</template>

<script>
import { creatArticle } from '@/api/article'
export default {
	middleware: 'authenticated',
    name: 'EditorIndex',
    data () {
        return {
            article: {
                title: '',
                description: '',
                body: '',
                tagList: []
            },
            tag:'',
            disabled: false
        }
    },
    methods: {
        onSubmitTag () {
            let tagList = this.article.tagList
            let isSame = false
            isSame = tagList.every(item => {
                return this.tag !== item
            })
            if(isSame){
                tagList.push(this.tag)
                this.tag = ''
            }
        },
        onDelTag (tag) {
            let tagList = this.article.tagList
            tagList.forEach((item,index) => {
                if(tag === item){
                    tagList.splice(index,1)
                }
            })
        },
        async saveCreate () {
            this.disabled = true
            const { data } = await creatArticle({
                article: this.article
            })
            this.disabled = true
            const slug = data.article.slug
            this.$router.push({
                name: 'article',
                params: {
                    slug: slug
                }
            })
        }
    }
}
</script>

<style>

</style>