<template>
  <div class="editor-page">
    <div class="container page">
      <div class="row">

        <div class="col-md-10 offset-md-1 col-xs-12">
          <form @submit.prevent="publishArticle">
            <fieldset>
              <fieldset class="form-group">
                  <input required v-model="title" type="text" class="form-control form-control-lg" placeholder="Article Title">
              </fieldset>
              <fieldset class="form-group">
                  <input required v-model="description" type="text" class="form-control" placeholder="What's this article about?">
              </fieldset>
              <fieldset class="form-group">
                  <textarea required v-model="body" class="form-control" rows="8" placeholder="Write your article (in markdown)"></textarea>
              </fieldset>
              <fieldset class="form-group">
                  <input v-model="tags" type="text" class="form-control" placeholder="Enter tags,more then one use ' , ' split"><div class="tag-list"></div>
              </fieldset>
              <button class="btn btn-lg pull-xs-right btn-primary" type="submit">
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
import {getArticle, publishArticleReq, updateArticleReq} from "../../api/article";

export default {
  // 在路由匹配组件渲染之前会先执行中间件处理
  middleware: 'authenticated',
  name: 'EditorIndex',
  data(){
    return {
      title: "",
      description: "",
      body: "",
      tags:"",
      // "tagList": ["reactjs", "angularjs", "dragons"],
      slug:'',
    }
  },
  methods:{
    async publishArticle(){

      let opt = {
        article:{
          title:this.title,
          description:this.description,
          body:this.body,
          tagList:this.tags.split(/,|，/)
        }
      }
      let data = null
      if(this.slug){
        // 更新
        data = await updateArticleReq(this.slug,opt)
        alert('更新成功')
      }else{
        //发布
        data = await publishArticleReq(opt)
        alert('发布成功')
      }
      this.$router.push({
        name: 'article',
        params: {
          slug: data.data.article.slug
        }
      })
    }
  },
  async mounted() {
    const { params } = this.$route
    if(params?.slug){
      const { data } = await getArticle(params.slug)
      const { article={} } = data
      this.slug = params.slug
      this.title = article.title
      this.description = article.description
      this.body = article.body
      this.tags = article.tagList.join(',')
    }
  }
}
</script>

<style>

</style>
