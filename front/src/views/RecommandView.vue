<template>
  <div class="all">
    <h1>{{ myProfile.username }} 님을 위한 추천 상품</h1>
    <section class="selector">
      <h2 @click="selection = 'fav'">Favorite!</h2>
      <h2 @click="selection = 'age'">나이</h2>
      <h2 @click="selection = 'mon'">자산</h2>
      <h2 @click="selection = 'sal'">연봉</h2>
      <div :class="['selection', {'fav': selection === 'fav', 'age': selection === 'age', 'mon': selection === 'mon', 'sal': selection === 'sal', }]">
      </div>
    </section>
    <section class="dep-sav-box">
      <!-- 관심사로 추천 -->
      <div class="dep-sav-fav" v-show="myProfile.age && selection === 'fav'">
        <div class="title-box">
          <div class="class">
            {{ sendFav.aka }}
          </div>
          <h3>당신을 위한 추천 상품</h3>
        </div>


        <div class="write" v-if="depOpt">
          <section class="line">
            <div class="underline">
              {{ depOpt.product.fin_prdt_nm }}
            </div>
            <p>에</p>
          </section>
          <section class="line">
            <input type="text" class="money-input" v-model="moneyInput">
            <p>원 저축하면</p>
          </section>
          <section class="line">
            <p>1년에 이자</p>
            <div class="underline">
              {{ interest }}
            </div>
            <p>원 받을 수 있어요.</p>
          </section>
          <section class="line">
            <div class="underline">
              {{ sendFav.gift }}
            </div>
            <p>{{ sendFav.end }}</p>
            <div class="underline">
              {{ count }}
            </div>
            <p>개!</p>
          </section>
        </div>


      </div>

      <!-- 나이로 추천 -->
      <div class="dep-sav" v-show="myProfile.age && selection === 'age'">
        <div class="items">
          <h3>예금</h3>
          <RecommandDep
            v-for="(prod, idx) in depFavoriteAge"
            :key="prod.id"
            :prod-id="prod.id"
            :idx="idx"
          />
        </div>
        <div class="items">
          <h3>적금</h3>
          <RecommandSav
            v-for="(prod, idx) in savFavoriteAge"
            :key="prod.id"
            :prod-id="prod.id"
            :idx="idx"
          />
        </div>
      </div>

      <!-- 자산으로 추천 -->
      <div class="dep-sav" v-show="myProfile.money && selection === 'mon'">
        <div class="items">
          <h3>예금</h3>
          <RecommandDep
            v-for="(prod, idx) in depFavoriteMoney"
            :key="prod.id"
            :prod-id="prod.id"
            :idx="idx"
          />
        </div>
        <div class="items">
          <h3>적금</h3>
          <RecommandSav
            v-for="(prod, idx) in savFavoriteMoney"
            :key="prod.id"
            :prod-id="prod.id"
            :idx="idx"
          />
        </div>
      </div>

      <!-- 연봉으로 추천 -->
      <div class="dep-sav" v-show="myProfile.salary && selection === 'sal'">
        <div class="items">
          <h3>예금</h3>
          <RecommandDep
            v-for="(prod, idx) in depFavoriteSalary"
            :key="prod.id"
            :prod-id="prod.id"
            :idx="idx"
          />
        </div>
        <div class="items">
          <h3>적금</h3>
          <RecommandSav
            v-for="(prod, idx) in savFavoriteSalary"
            :key="prod.id"
            :prod-id="prod.id"
            :idx="idx"
          />
        </div>
      </div>
    </section>
  </div>
</template>

<script setup>
import { onMounted, computed, ref, watch } from 'vue'
import { useLoginStore } from '@/stores/login'
import RecommandDep from '@/components/Recommand/RecommandDep.vue'
import RecommandSav from '@/components/Recommand/RecommandSav.vue'
import RecommandFav from '@/components/Recommand/RecommandFav.vue'
import axios from 'axios'

const loginStore = useLoginStore()
onMounted(() => {
  getProfile()
})

const myProfile = ref([])

const selection = ref('fav')


const favList = [
  {aka: '얼리어답터인', gift: '샤오미 보조배터리 20000mAh', price: 34000, end: '가'},
  {aka: 'OOTD 기록러인', gift: '유니클로 히트텍', price: 14000, end: '이'},
  {aka: '배움을 찾는', gift: '인프런 강의', price: 33000, end: '가'},
  {aka: '침대보다 여행인', gift: '휴족시간', price: 3000, end: '이'},
  {aka: '스포츠 정신을 가진', gift: '나이키 스포츠 양말', price: 19800, end: '이'},
  {aka: '주인님 모시려고 일하는 집사인', gift: '고양이 모래 18kg', price:15000, end: '가'},
  {aka: '1일 : N카페인인', gift: '바나프레소 아메리카노', price: 1800, end: '가'},
  {aka: '맛집 등록하려고 지도 앱 쓰는', gift: '롯데리아 새우버거', price: 4700, end: '가'},
  {aka: '자동차만 보면 눈이 뒤집히는', gift: '베어브릭 차량용 방향제', price: 19000, end: '이'},
  {aka: '집꾸미기를 좋아하는', gift: '다이소 발매트', price: 4000, end: '가'},
  {aka: 'Music is My Life', gift: '유튜브 뮤직 한달 구독권', price: 8690, end: '이'},
  {aka: '효심이 깊은', gift: '임영웅 콘서트 티켓', price: 165000, end: '이'},
]

const sendFav = ref('')
const API_URL = 'http://127.0.0.1:8000/api/v1/accounts'

const getProfile = function () {
    axios({
      method: 'get',
      url: `${API_URL}/profile/`,
      headers: {
        Authorization: `Token ${loginStore.token}`
      }
    })
      .then((res) => {
        console.log(res)
        myProfile.value = res.data
        console.log('!!!!!!!!!!!!', res.data.favorite)
        usersFavorite(res.data.favorite)
        moneyReco(res.data.money)
        salaryReco(res.data.salary)
        ageReco(res.data.age)
      })
      .catch((err) => {
        console.log(err)
      })
  }


// 관심사 보내기
const usersFavorite = function (array) {
  let favoriteId = null
  const myfavoritesArr = array
  console.log(array, 'hhhhhhhhhhhhhhhhhhhhhhhh')
  if ( myfavoritesArr ) {
    let myfavorites = []
    array.forEach(element => {
      myfavorites.push(element.id)
      const randomFavoriteId = Math.floor(Math.random() * myfavorites.length)
      console.log(randomFavoriteId, 'randomFavoriteId')
      favoriteId = myfavorites[randomFavoriteId] - 1
      console.log(favoriteId, 'favoriteId')
      sendFav.value = favList[favoriteId]
      getUsersFavorite(favoriteId)
    })
  }
  loginStore.usersFavorite(favoriteId)
}

const depFavoriteMoney = ref([])
const savFavoriteMoney = ref([])
// 자산 보내기
const moneyReco = function (money) {
  // const money = ref(0)
  // money.value = myProfile.value.money
  // console.log(myProfile.value)
  // console.log('!!!!!', money.value)
  axios({
    method: 'get',
    url: `${API_URL}/users_money/${money}/`,
    headers: {
      Authorization: `Token ${loginStore.token}`
    }
  })
    .then((res) => {
      depFavoriteMoney.value = res.data.most_financial_options_dep
      savFavoriteMoney.value = res.data.most_financial_options_sav
    })
    .catch((err) => {
      console.log(err.data)
    })
}

const depFavoriteSalary = ref([])
const savFavoriteSalary = ref([])
// 연봉 보내기
const salaryReco = function (salary) {
  // const salary = ref(0)
  // salary.value = myProfile.value.salary
  axios({
    method: 'get',
    url: `${API_URL}/users_salary/${salary}/`,
    headers: {
      Authorization: `Token ${loginStore.token}`
    }
  })
    .then((res) => {
      depFavoriteSalary.value = res.data.most_financial_options_dep
      savFavoriteSalary.value = res.data.most_financial_options_sav
    })
    .catch((err) => {
      console.log(err)
    })
}

const depFavoriteAge = ref([])
const savFavoriteAge = ref([])
// 자산 보내기
const ageReco = function (age) {
  // const age = ref(0)
  // age.value = myProfile.value.age
  axios({
    method: 'get',
    url: `${API_URL}/users_age/${age}/`,
    headers: {
      Authorization: `Token ${loginStore.token}`
    }
  })
    .then((res) => {
      depFavoriteAge.value = res.data.most_financial_options_dep
      savFavoriteAge.value = res.data.most_financial_options_sav
      getDepOpt()
    })
    .catch((err) => {
      console.log(err)
    })
}

const moneyInput = ref(10000000)
const interest = ref(0)
const count = ref(0)

const watcher = watch(() => (moneyInput.value), (newValue) => {
  interest.value = Math.floor((depOpt.value.intr_rate2 / 100) * moneyInput.value)
  count.value = parseInt(interest.value / sendFav.value.price)
})



const depOpt = ref(null)
const API_URL2 = 'http://127.0.0.1:8000/api/v1/products'

const getDepOpt = function () {
  axios({
    method: 'get',
    url: `${API_URL2}/one-dep-opt/${loginStore.depLankfromFavorite[0].id}/`
  })
    .then((res) => {
      console.log(res.data)
      depOpt.value = res.data
      interest.value = Math.floor((depOpt.value.intr_rate2 / 100) * moneyInput.value)
      count.value = parseInt(interest.value / sendFav.value.price)
    })
    .catch((err) => {
      console.log(err.data)
    })
}

const depLankfromFavorite = ref([])
const getUsersFavorite = function (favoriteId) {
    axios({
      method: 'get',
      url: `${API_URL}/users_favorite/${favoriteId}/`,
      headers: {
        Authorization: `Token ${loginStore.token}`
      }
    })
    .then ((res) => {
      console.log(res.data)
      depLankfromFavorite.value = res.data.most_financial_options_dep
    })
    .catch((err) => {
      console.log('favorit 추천 오류', err)
    })
  }
</script>

<style scoped>
img {
  width: 100%;
}
.title-box {
  display: flex;
  align-items: center;
  margin: 20px 0;
}
.class {
  font-size: 24px;
  font-weight: 700;
  background-color: var(--sub-color);
  color: var(--main-color);
  padding: 8px 16px;
  border-radius: 32px;
}
h3 {
  font-size: 20px;
  font-weight: 700;
  margin: 20px 8px;
}
.dep-sav {
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
}
.dep-sav-fav {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
}
.items {
  width: 48%;
  min-width: 350px;
}
.selector {
  border: 4px solid var(--sub-color);
  border-radius: 48px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 480px;
  position: relative;
  padding: 8px 24px;
}
.selection {
  z-index: -1;
  position: absolute;
  height: 40px;
  border-radius: 20px;
  background-color: var(--main-color);
  transition: all 0.5s ease-in-out;
}
.selector h2 {
  margin: 0;
  font-size: 24px;
  font-weight: 400;
  cursor: pointer;
}
.selector h2:hover {
  color: var(--main-color);
}
.fav {
  width: 144px;
  left: 3px;
}
.age {
  width: 88px;
  left: 164px;
}
.mon {
  width: 88px;
  left: 272px;
}
.sal {
  width: 88px;
  left: 380px;
}
.all {
  margin-top: 52px;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.all h1 {
  font-size: 36px;
  margin-bottom: 40px;
}
.dep-sav-box {
  width: 100%;
  margin: 24px 0;
  border-bottom: 4px solid var(--sub-color);
  border-top: 4px solid var(--sub-color);
  padding: 12px 0;
  transition: all 0.5s;
}
.write {
  width: 90%;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}
.underline {
  border-bottom: 8px solid var(--main-color);
  font-size: 28px;
  font-weight: 700;
  margin: 0 4px;
  padding: 0 20px;
  text-align: center;
}
.line {
  display: flex;
  flex-direction: flex-start;
  align-items: baseline;
  margin: 16px 12px;
}
.money-input {
  outline: 0;
  border: 0;
  border-bottom: 8px solid var(--main-color);
  text-align: center;
  font-size: 28px;
  font-weight: 700;
  width: 210px;
}
p {
  font-size: 28px;
  font-weight: 400;
}
</style>