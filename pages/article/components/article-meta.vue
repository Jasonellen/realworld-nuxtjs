<template>
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
      }">
        {{ article.author.username }}
      </nuxt-link>
      <span class="date">{{ article.createdAt | date('MMM DD, YYYY') }}</span>
    </div>


    <span v-if="isSelf" class="ng-scope">
      <nuxt-link
          class="btn btn-outline-secondary btn-sm"
          :to="{
            name: 'editor-slug',
            params: {
              slug: article.slug
            }
          }"
      >
        <i class="ion-edit"></i> Edit Article
      </nuxt-link>
      <button
          class="btn btn-outline-danger btn-sm"
          :class="{disabled: isDeleting}"
          @click="deleteArticle">
        <i class="ion-trash-a"></i> Delete Article
      </button>
    </span>

    <span v-else>
      <button
          class="btn btn-sm btn-outline-secondary"
          :style="{
            background: article.author.following?'#5cb85c':'lightgrey',
            color: article.author.following?'#fff':'#333',
          }"
          @click="toFollowUser"
      >
          <i v-show="!article.author.following" class="ion-plus-round"></i>
          Follow{{article.author.following?'ed':''}} {{article.author.username}}
        </button>
        &nbsp;&nbsp;
        <button
            class="btn btn-sm btn-outline-primary"
            :class="{
            active: article.favorited
          }"
            @click="toFavorite"
        >
          <i class="ion-heart"></i>
          &nbsp;
          Favorite Post <span class="counter">({{article.favoritesCount}})</span>
        </button>
    </span>

  </div>
</template>

<script>
import {mapState} from "vuex";
import {addFavorite, delArticle, deleteFavorite, followUser, unfollowUser} from "../../../api/article";

export default {
  name: 'ArticleMeta',
  data(){
    return {
      isDeleting:false,
    }
  },
  props: {
    article: {
      type: Object,
      required: true
    }
  },

  computed: {
    ...mapState(['user']),
    isSelf: function(){
      return this.article.author.username === this.user.username
    }
  },
  methods:{
    async deleteArticle(){
        this.isDeleting = true
        await delArticle(this.article.slug)
        alert("删除成功")
        this.$router.go(-1)
    },
    async toFollowUser(){
      if(this.article.author.following){
        //已关注  要取消
        let { data } = await unfollowUser(this.article.author.username)
        this.article.author = data.profile
        alert('取消关注成功')
      }else{
        //要关注
        let { data } = await followUser(this.article.author.username)
        this.article.author = data.profile
        alert('关注成功')
      }
    },
    async toFavorite(){
      const { slug,favorited } = this.article
      let data = null
      if(favorited){
        // 已喜欢 要取消
        data = await deleteFavorite(slug)
      }else{
        //+喜欢
        data = await addFavorite(slug)
      }
      this.article = data.data.article
      alert('成功！')
    }
  }
}
</script>

<style>

</style>
