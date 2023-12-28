<script >
import { RouterLink, RouterView } from 'vue-router'
export default {
    data() {
        return {
            arr: [],
            user: [],
            //分頁
            perpage: 7,
            currentPage: 1,
        }
    },
    mounted() {
        const serializedObject = this.$route.params.all;
        const receivedObject = JSON.parse(serializedObject);

        this.arr = receivedObject
        console.log(this.arr);
        // this.title = this.arr.questionnaire.title;
        // this.description = this.arr.questionnaire.description;
        // this.id = this.arr.questionnaire.id;

        fetch('http://localhost:8080/api/quiz/getUser?qnId=' + this.arr.questionnaire.id, {
            method: "GET",
            headers: new Headers({
                "Accept": "application/json",
                "Content-Type": "application/json"
            }),
        })
            .then(res => res.json())
            .then(data => {
                console.log(data);
                var answer = [];
                if (Array.isArray(data.userList)) {
                    const uniqueNames = new Set();
                    for (let i = 0; i < data.userList.length; i++) {
                        const user = data.userList[i];
                        if (!uniqueNames.has(user.name)) {
                            answer.push({
                                num: user.num,
                                name: user.name,
                                phoneNumber: user.phoneNumber,
                                email: user.email,
                                age: user.age,
                                ans: user.ans,
                                id: user.qId,
                                datetime: user.datetime.split('T'),
                                questionnaireId: this.arr.questionnaire.id,
                                questionnaireTitle: this.arr.questionnaire.title,
                                question_list: this.arr.question_list,
                                description: this.arr.questionnaire.description
                            });

                            uniqueNames.add(user.name);
                        }
                    }
                }
                this.user = answer
                console.log(this.user);
            })
    },
    methods: {
        //分頁
        setPage(page) {
            if (page <= 0 || page > this.totalPage) {
                return
            }
            this.currentPage = page
        },
    },
    //日期
    created() {
        const date = new Date()
        const year = date.getFullYear()
        const month = date.getMonth() + 1
        const day = date.getDate()
        this.currentDate = `${year}-${month}-${day}`
        console.log(this.currentDate)
    },
    components: {
        RouterLink
    },
    computed: {
        // 分頁
        totalPage() {
            //Math.ceil()取最小整數
            return Math.ceil(this.user.length / this.perpage)
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

}
</script>

<template>
    <div class="all">
        <nav class="topNav">
            <h1>問卷回饋</h1>
        </nav>
        <div class="second">
            <div class="secondTop">
                <p>編號</p>
                <p>姓名</p>
                <p>填寫時間</p>
                <p>查看回復</p>
            </div>
            <div class="secondLast" v-for=" (item, index) in user.slice(pageStart, pageEnd)">
                <span>
                    {{ index + 1 }}
                </span>

                <span>
                    {{ item.name }}
                </span>
                <span>
                    {{ item.datetime }}
                </span>
                <span>
                    <RouterLink :to="/userwrite/ + JSON.stringify(item)">
                        前往
                    </RouterLink>
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
}

.second {
    height: 45%;
    width: 80%;
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
            border: 1px solid black;
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
            color: rgb(62, 105, 213);

            &:hover {
                font-size: 15pt;
                color: #E81E25;
            }
        }

        .fa-pen {
            transition: 1s;
            color: green;

            &:hover {
                font-size: 15pt;
                color: #E81E25;
            }
        }
    }
}
</style>

