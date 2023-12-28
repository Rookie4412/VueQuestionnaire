<script>
import { RouterLink, RouterView } from "vue-router";
import Header from "../components/Header.vue";
import axios from 'axios';
export default {
    data() {
        return {
            //總共問卷
            allQuestionnaire: [],
            //搜尋
            searchTitle: "",
            searchStartDate: "",
            searchEndDate: "",
            //time
            currentDate: "",
            //分頁
            perpage: 7,
            currentPage: 1,
            //刪除
            arr: [],
            selectedQuestionIds: [],
            id: 0,
            //換頁
            page: 0,
        };
    },
    methods: {
        // //搜尋
        searchAll() {
            fetch("http://localhost:8080/api/quiz/search?title=" + this.searchTitle + "&starDate=" + this.searchStartDate + "&endDate=" + this.searchEndDate)
                .then(res => res.json())
                .then(data => {
                    this.allQuestionnaire = data;
                    console.log(this.allQuestionnaire);
                    this.allQuestionnaire = this.allQuestionnaire.quizVoList.reverse();

                    // console.log(data);
                    console.log(this.allQuestionnaire);
                    // this.arr = this.allQuestionnaire;
                    // this.arr = this.arr[this.id].questionnaire;
                    // console.log(this.arr);
                })
        },
        //分頁
        setPage(page) {
            if (page <= 0 || page > this.totalPage) {
                return
            }
            this.currentPage = page
        },
        // 刪除問題
        deleteQustionnaire() {
            fetch('http://localhost:8080/api/quiz/deleteQustionnaire', {
                method: "POST",
                headers: new Headers({
                    "Accept": "application/json",
                    "Content-Type": "application/json"
                }),
                body: JSON.stringify({ qnList: this.arr })
            })
                .then(res => res.json())
                .then(data => {
                    window.location.reload()
                })
        },
        //刪除
        checkBox(questionnaireId) {
            const Qnid = this.arr.indexOf(questionnaireId);
            if (Qnid !== -1) {
                this.arr.splice(Qnid, 1);
            } else {
                this.id = questionnaireId
                this.arr.push(questionnaireId);
            }
            console.log(typeof this.arr)
            console.log(this.arr)
        },
        //換頁
        changepage() {
            this.page = 1
        },
        changepage1() {
            this.page = 0
        },
    },
    mounted() {
        this.searchAll()
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
    //time
    created() {
        const date = new Date()
        const year = date.getFullYear()
        const month = date.getMonth() + 1
        const day = date.getDate()
        this.currentDate = `${year}-${month}-${day}`
        console.log(this.currentDate)
    },
    components: {
        RouterLink,
        Header
    }
}
</script>

<template>
    <div class="all">
        <div class="top">
            <div class="topQu">
                <p>問卷標籤</p>
                <input type="text" class="Qu" v-model="searchTitle">
            </div>
            <div class="topStart">
                <p>開始</p>
                <input type="date" class="Start" v-model="searchStartDate">

                <p>結束</p>
                <input type="date" class="Start" v-model="searchEndDate" :min="searchStartDate">

                <button class="but" @click="searchAll">搜尋</button>
            </div>
        </div>

        <div class="bet">
            <i class="fa-solid fa-trash" @click="changepage"></i>

            <RouterLink to="/List" style="margin-left: 7%;">
                <i class="fa-solid fa-plus"></i>
            </RouterLink>
        </div>
        <!-- 刪除小分頁 -->
        <div class="changePage" v-show="page == 1">
            <i class="fa-solid fa-xmark" @click="changepage1"></i>
            <div class="changePageTop">
                <h1>確定刪除:</h1>
                <span>{{ arr }}</span>
                <button class="changePageBut" @click="deleteQustionnaire">刪除</button>
            </div>
        </div>
        <!-- 題目 -->
        <div class="second">
            <div class="secondTop">
                <p style="width: 15%;"></p>
                <p>#</p>
                <p>問卷</p>
                <p>狀態</p>
                <p>開始時間</p>
                <p>結束時間</p>
                <p>統計&修改</p>
            </div>
            <div class="secondLast" v-for=" (item, index) in allQuestionnaire.slice(pageStart, pageEnd)">
                <span style="width: 15%;">
                    <input type="checkbox" v-model="item.checkbox" @change="checkBox(item.questionnaire.id)">
                </span>
                <span>{{ item.questionnaire.id }}</span>
                <span>
                    <RouterLink :to="/statistics/ + JSON.stringify(item)">
                        {{ item.questionnaire.title }}
                    </RouterLink>
                </span>
                <span v-if="item.questionnaire.published == true" style="color: rgb(31, 161, 31);">開放中 </span>
                <span v-else style="color: red;">未開放</span>
                <span>{{ item.questionnaire.startDate }}</span>
                <span>{{ item.questionnaire.endDate }}</span>
                <!-- <RouterLink :to="'/confirm/' + JSON.stringify(item.questionnaire)"
                    style="height: 100%; width: 50%; cursor: pointer;">
                    <i class="fa-solid fa-chart-pie"></i>
                </RouterLink> -->

                <RouterLink v-if="item.questionnaire.published == false"
                    :to="/confirm/ + JSON.stringify(item.questionnaire)" style="height: 100%; width: 25%;">
                    <i class="fa-solid fa-chart-pie"></i>
                </RouterLink>

                <span v-else style="height: 100%; width: 25%; cursor: not-allowed; display: inline-block;">
                    <i class="fa-solid fa-chart-pie"></i>
                </span>

                <RouterLink v-if="item.questionnaire.published == false"
                    :to="'/Update/' + JSON.stringify(item.questionnaire)" style="height: 100%; width: 25%;">
                    <i class="fa-solid fa-pen"></i>
                </RouterLink>

                <span v-else style="height: 100%; width: 25%; cursor: not-allowed; display: inline-block;">
                    <i class="fa-solid fa-pen"></i>
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
                <li class="page-item" :class="{ 'active': (currentPage === (n)) }" v-for="(n, index) in totalPage"
                    :key="index" @click.prevent="setPage(n)">
                    <a class="page-link" href="#">{{ n }}</a>
                </li>
                <li class="page-item" @click.prevent="setPage(currentPage + 1)">
                    <a class="page-link" href="#" aria-label="Next">
                        <span aria-hidden="true">&raquo;</span>
                    </a>
                </li>
            </ul>
        </div>
    </div>
</template>

<style lang="scss" scoped>
.all {
    height: 100vh;
    width: 100vw;
    background-color: #BEDAE5;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    position: relative;
}

.top {
    height: 30%;
    width: 50%;
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    background-color: #EECB27;
    margin-top: 5%;

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

.bet {
    height: 5vh;
    width: 50%;
    margin-top: 1%;
    display: flex;
    align-items: center;
    font-size: 16pt;
    color: black;

    .fa-trash {
        transition: 1s;
        margin-left: 7%;

        &:hover {
            font-size: 20pt;
            color: #E81E25;
        }
    }

    .fa-plus {
        font-size: 16pt;
        transition: 1s;

        &:hover {
            font-size: 20pt;
            color: #E81E25;
        }
    }


}

.changePage {
    height: 50%;
    width: 30%;
    display: flex;
    flex-direction: column;
    color: white;
    background-color: #6d6d6e;
    position: fixed;

    .fa-xmark {
        color: white;
        margin-left: 90%;
        font-size: 20pt;
        transition: 1s;

        &:hover {
            color: red;
        }
    }

    .changePageTop {
        height: 70%;
        display: flex;
        flex-direction: column;
        justify-content: space-around;
        align-items: center;

        .changePageBut {
            display: flex;
            justify-content: center;
            width: 20%;
            transition: 1s;

            &:hover {
                background-color: #eca79c;
            }
        }
    }
}

.second {
    height: 45%;
    width: 50%;
    display: flex;
    margin-top: 1%;
    flex-direction: column;
    background-color: #EECB27;

    .secondTop {
        height: 10%;
        width: 100%;
        display: flex;
        justify-content: space-around;
        border: 1px solid black;

        p {
            height: 100%;
            width: 50%;
        }

    }

    .secondLast {
        height: 10%;
        width: 100%;
        display: flex;
        justify-content: space-around;
        margin-top: 1%;
        // border: 1px solid rgb(66, 50, 205);

        span {
            height: 100%;
            width: 50%;
        }

        .fa-chart-pie {
            transition: 1s;
            // color: rgb(62, 105, 213);

            &:hover {
                font-size: 15pt;
                color: #E81E25;
            }
        }

        .fa-pen {
            transition: 1s;
            // color: green;

            &:hover {
                font-size: 15pt;
                color: #E81E25;
            }
        }
    }
}
</style>
