<template>
  <!-- <div> -->
    <div class="card article" v-if="!article.is_locked || whosArticle" @click="goDetail">
      <div class="card-header line-one">
        <div class="category">
          <span class="badge text-bg-dark">{{ article.article_bank_category.name }}</span>
          <span v-if="article.article_product_category.name === '일반'" class="badge text-bg-primary">{{ article.article_product_category.name }}</span>
          <span v-if="article.article_product_category.name === '예금'" class="badge text-bg-secondary">{{ article.article_product_category.name }}</span>
          <span v-if="article.article_product_category.name === '적금'" class="badge text-bg-success">{{ article.article_product_category.name }}</span>
          <span v-if="article.article_product_category.name === '환율'" class="badge text-bg-danger">{{ article.article_product_category.name }}</span>
        </div>
        <div>
          <p v-if="whosArticle">내 게시글</p>
          <p v-else>{{ article.user.username }}</p>
        </div>
      </div>
      <div class="card-body line-two">
        <h3>{{ article.title }}</h3>
          <p>
            <i class="fa-solid fa-heart"></i>
            {{ likeCounter }}
          </p>
      </div>
      <div class="info-box">
        <p class="time">{{ article.created_at.slice(0, 10) }} {{ article.created_at.slice(11, 19) }}</p>
      </div>
    </div>
  
  <div class="card article-locked" v-else>
    <div class="card-header line-one">
      <div class="category">
        <span class="badge text-bg-dark">{{ article.article_bank_category.name }}</span>
          <span v-if="article.article_product_category.name === '일반'" class="badge text-bg-primary">{{ article.article_product_category.name }}</span>
          <span v-if="article.article_product_category.name === '예금'" class="badge text-bg-secondary">{{ article.article_product_category.name }}</span>
          <span v-if="article.article_product_category.name === '적금'" class="badge text-bg-success">{{ article.article_product_category.name }}</span>
          <span v-if="article.article_product_category.name === '환율'" class="badge text-bg-danger">{{ article.article_product_category.name }}</span>
      </div>
    </div>
    <div class="title">
      <i id="locker" @click="showInput" class="fa-solid fa-lock"></i><br>
      <h3>{{ article.title }}</h3>
    </div>
    <form v-show="unlock" @submit.prevent="checkPassword">
      <input type="password" v-model="inputPwd">
      <input type="submit">
    </form>
  </div>

    <!-- <div class="article" v-if="!article.is_locked || whosArticle" @click="goDetail">
      <div class="line-one">
        <div class="category">
          <span class="category-item">{{ article.article_product_category.name }}</span>
          <span class="category-item">{{ article.article_bank_category.name }}</span>
        </div>
        <div>
          <p v-if="whosArticle">내 게시글</p>
          <p v-else>{{ article.user.username }}</p>
        </div>
      </div>
      <div class="line-two">
        <h3>{{ article.title }}</h3>
        <div>
          <p>
            <i class="fa-solid fa-heart"></i>
            {{ likeCounter }}
          </p>
        </div>
      </div>
      <div class="info-box">
        <p class="time">{{ article.created_at.slice(0, 10) }} {{ article.created_at.slice(11, 19) }}</p>
      </div>
    </div> -->

    <!-- <div class="article-locked" v-else>
      <div class="line-one">
        <div class="category">
          <span class="category-item">{{ article.article_product_category.name }}</span>
          <span class="category-item">{{ article.article_bank_category.name }}</span>
        </div>
      </div>
      <div class="title">
        <i id="locker" @click="showInput" class="fa-solid fa-lock"></i><br>
        <h3>{{ article.title }}</h3>
      </div>
      <form v-show="unlock" @submit.prevent="checkPassword">
        <input type="password" v-model="inputPwd">
        <input type="submit">
      </form>
    </div>
    <hr>
  </div> -->
</template>

<script setup>
import { useRouter } from 'vue-router'
import { ref, computed } from 'vue'
import { useLoginStore } from '@/stores/login'

const loginStore = useLoginStore()
const router = useRouter()
const unlock = ref(false)
const inputPwd = ref(null)

const props = defineProps({
  article: Object
})

const goDetail = function () {
  router.push({ name: 'ArticleDetail', params: { articleId: props.article.id } })
}

const showInput = function () {
  unlock.value = !unlock.value
}

const checkPassword = function () {
  if (inputPwd.value === props.article.password) {
    router.push({ name: 'ArticleDetail', params: { articleId: props.article.id } })
  }
}

// 게시글 소유자 확인
const whosArticle = computed(() => {
  if (loginStore.myName === props.article.user.username) {
    return true
  } else {
    return false
  }
})

// 좋아요 개수
const likeCounter = computed(() => {
    return props.article.like_users.length
})

</script>

<style scoped>

.line-one {
  display: flex;
  justify-content: space-between;
  margin: 0px;
}
.line-two {
  display: flex;
  justify-content: space-between;
  margin-left: 5px;
  padding: 5px;
  height: 30px;
}

.category-item {
  margin-right: 4px;
}
.title {
  display: flex;
  align-items: center;
  margin: 8px 0;
}
.time {
  font-size: 14px;
  padding: 5px;
}
#locker {
  cursor: pointer;
  margin-right: 8px;
}
h3 {
  margin: 0;
  height: 30px;
}
hr {
  margin: 0;
}
p {
  margin: 0;
}
.article-locked {
  height: 150px;
  background-color: white;
  margin: 8px 0;
}
.article {
  height: 150px;
  background-color: white;
  transition: all 0.5s;
  cursor: pointer;
  margin: 15px 0;
}
.article:hover {
  background-color: rgb(235, 235, 235);
}
.info-box {
  display: flex;
  justify-content: end;
}
</style>