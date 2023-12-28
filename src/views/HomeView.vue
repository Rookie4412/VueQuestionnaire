<script>
import { RouterLink, RouterView } from "vue-router";
import Header from "../components/Header.vue";
export default {
  data() {
    return {
      //總共問卷
      allQuestionnaire: [],
      //搜尋
      searchTitle: "",
      searchStartDate: "",
      searchEndDate: "",
      //分頁
      perpage: 7,
      currentPage: 1,
      //換頁
      page: 0,
      //搜尋問題
      searchQuestion: [],
      // searchQuestionId: {},
    };
  },
  methods: {
    searchAll() {
      fetch("http://localhost:8080/api/quiz/search?title=" + this.searchTitle + "&starDate=" + this.searchStartDate + "&endDate=" + this.searchEndDate)
        .then(res => res.json())
        .then(data => {
          this.allQuestionnaire = data;
          console.log(this.allQuestionnaire);
          this.allQuestionnaire = this.allQuestionnaire.quizVoList.reverse();

          // console.log(data);
          // console.log(this.allQuestionnaire);
          // this.arr = this.allQuestionnaire;
          // this.arr = this.arr[this.id].questionnaire;
          // console.log(this.arr);
        })
    },

    // searchQuestionList() {
    //   // const IdValue = this.$route.params.questionnaireId;
    //   // console.log(IdValue)
    // },
    //分頁
    setPage(page) {
      if (page <= 0 || page > this.totalPage) {
        return
      }
      this.currentPage = page
    },
  },
  computed: {
    // 分頁
    totalPage() {
      //Math.ceil()取最小整數
      return Math.ceil(this.allQuestionnaire.length / this.perpage)
    },
    pageStart() {
      //取得該頁第一個值的index
      return (this.currentPage - 1) * this.perpage
    },
    pageEnd() {
      return this.currentPage * this.perpage
      //取得該頁最後一個值的index
    }
  },
  mounted() {
    this.searchAll()
  },
  components: {
    RouterLink,
    Header,
  }
}
</script>

<template>
  <div class="all" v-show="page == 0">

    <div class="top">
      <div class="topQu">
        <p>問卷標籤</p>
        <input type="text" class="Qu" v-model="this.searchTitle">
      </div>
      <div class="topStart">
        <p>開始</p>
        <input type="date" class="Start" v-model="this.searchStartDate">
        <p>結束</p>
        <input type="date" class="Start" v-model="this.searchEndDate">
        <button class="but" @click="searchAll()">搜尋</button>
      </div>
    </div>


    <div class="second">
      <div class="secondTop">
        <p style="width: 44%;">#</p>
        <p>問卷</p>
        <p>狀態</p>
        <p>開始時間</p>
        <p>結束時間</p>
        <p>觀看統計</p>
      </div>
      <div class="secondLast" v-for=" (item, index) in allQuestionnaire.slice(pageStart, pageEnd)">
        <span style="width: 45%;">{{ item.questionnaire.id }}</span>
        <RouterLink :to="'/vote/' + JSON.stringify(item)" style="width: 50%;" v-if="item.questionnaire.published">{{
          item.questionnaire.title }}
        </RouterLink>
        <span v-else style="height: 100%; width: 50%; cursor: not-allowed; display: inline-block;">
          {{item.questionnaire.title }}
        </span>
        <span v-if="item.questionnaire.published == true" style="color: rgb(31, 161, 31);">開放中 </span>
        <span v-else style="color: red;">未開放</span>
        <span>{{ item.questionnaire.startDate }}</span>
        <span>{{ item.questionnaire.endDate }}</span>
        <RouterLink v-if="item.questionnaire.published" :to="'/confirm/' + JSON.stringify(item.questionnaire)"
          style="height: 100%; width: 50%; cursor: pointer;">
          <i class="fa-solid fa-chart-pie"></i>
        </RouterLink>

        <span v-else style="height: 100%; width: 50%; cursor: not-allowed; display: inline-block;">
          <i class="fa-solid fa-chart-pie"></i>
        </span>
      </div>
    </div>
    <!-- 分頁 -->
    <div class="page">
      <ul class="pagination">
        <li class="page-item" @click.prevent="setPage(currentPage - 1)">
          <a class="page-link" href="#" aria-label="Previous">
            <span aria-hidden="true">&laquo;</span>
          </a>
        </li>
        <li class="page-item" :class="{ 'active': (currentPage === (n)) }" v-for="(n, index) in totalPage" :key="index"
          @click.prevent="setPage(n)">
          <a class="page-link" href="#">{{ n }}</a>
        </li>
        <li class="page-item" @click.prevent="setPage(currentPage + 1)">
          <a class="page-link" href="#" aria-label="Next">
            <span aria-hidden="true">&raquo;</span>
          </a>
        </li>
      </ul>
    </div>
    <RouterLink to="/BakeQuestion">
      <i class="fa-solid fa-pen-to-square"></i>
    </RouterLink>
  </div>
</template>

<style lang="scss" scoped>
.all {
  height: 100vh;
  width: 100vw;
  background-color: #BEDAE5;
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  align-items: center;
}

.top {
  height: 30%;
  width: 50%;
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  background-color: #EECB27;

  .topQu {
    height: 20%;
    width: 40%;
    display: flex;
    justify-content: space-around;

    p {
      font-size: 16pt;
      color: #1F1762;
    }

    .Qu {
      height: 80%;
    }
  }

  .topStart {
    height: 20%;
    width: 80%;
    display: flex;
    justify-content: space-around;

    p {
      font-size: 16pt;
      color: #1F1762;
    }

    .Start {
      height: 80%;
    }

    .but {
      height: 80%;
      width: 10%;
      background-color: #E81E25;
      color: #1F1762;
      border-radius: 50%;
      border: none;
    }
  }
}

.second {
  height: 40%;
  width: 50%;
  display: flex;
  flex-direction: column;
  background-color: #EECB27;

  .secondTop {
    height: 10%;
    width: 100%;
    display: flex;
    justify-content: space-around;
    position: relative;

    p {
      height: 100%;
      width: 50%;
      border: 1px solid black;
    }

  }

  .secondLast {
    height: 10%;
    width: 100%;
    display: flex;
    margin-top: 1%;
    justify-content: space-around;

    span {
      height: 100%;
      width: 50%;
    }
  }
}

.fa-pen-to-square {
  font-size: 26pt;
  bottom: 20px;
  right: 15px;
  color: white;
  justify-content: space-around;
  position: fixed;
  transition: 1s;

  &:hover {
    color: palevioletred;
    font-size: 30pt;
  }
}
</style>
