<script >
import { RouterLink, RouterView } from 'vue-router'
export default {
    data() {
        return {
            //v-show
            page: 0,
            //create問卷
            id: 0,
            title: "",
            description: "",
            published: false,
            startDate: "",
            endDate: "",
            currentDate: "",
            // 問題
            arr: [],
            quId: 0,
            qTitle: "",
            optionType: "",
            necessary: false,
            opTion: "",
            qnId:0,
            //分頁
            perpage: 6,
            currentPage: 1,
            questionarr: []
        }
    },
    mounted() {
        const serializedObject = this.$route.params.id;
        const receivedObject = JSON.parse(serializedObject);
        this.qnId = receivedObject.id
        this.id = receivedObject.id
        this.title = receivedObject.title
        this.description = receivedObject.description
        this.startDate = receivedObject.startDate
        this.endDate = receivedObject.endDate

        console.log(receivedObject);
        fetch('http://localhost:8080/api/quiz/searchQuestionList?qnId=' + this.id, {
            method: "GET",
            headers: new Headers({
                "Accept": "application/json",
                "Content-Type": "application/json"
            }),
        })
            .then(res => res.json())
            .then(data => {
                // this.arr = data
                let newArray = [];
                if (Array.isArray(data.questionList)) {
                    for (let i = 0; i < data.questionList.length; i++) {
                        newArray.push({
                            quId: data.questionList[i].quId,
                            QTitle: data.questionList[i].qTitle,
                            OptionType: data.questionList[i].optionType,
                            Necessary: data.questionList[i].necessary,
                            OpTion: data.questionList[i].option,
                        });
                    }
                }
                this.arr = newArray;
                console.log(this.arr);

            })
    },
    methods: {
        changepage() {
            this.page = 0
        },
        changepage1() {
            this.page = 1
        },
        changepage2() {
            this.page = 2
        },
        changepage3() {
            this.page = 3
        },
        changepage4() {
            this.page = 4
        },
        checkBox(questionnaireId) {
            const Qnid = this.questionarr.indexOf(questionnaireId);
            if (Qnid !== -1) {
                this.questionarr.splice(Qnid, 1);
            } else {
                this.id = questionnaireId
                this.questionarr.push(questionnaireId);
            }
            console.log(this.questionarr)
        },
        createquestion() {
            // 時間判斷
            if (this.title.trim() === "") {
                alert('請輸入問卷名稱');
                return;
            } else if (this.description.trim() === "") {
                alert('請輸入內容');
                return;
            } else {
                }
            this.page = 1
        },
        createfetch() {
            var createQn = {
                "questionnaire": {
                    "id": this.id,
                    "title": this.title,
                    "description": this.description,
                    "published": true,
                    "startDate": this.startDate,
                    "endDate": this.endDate
                },
                "question_list": []
            };
            for (let i = 0; i < this.arr.length; i++) {
                createQn.question_list.push({
                    "quId": this.arr[i].quId,
                    "qnId": this.id,
                    "qTitle": this.arr[i].QTitle,
                    "optionType": this.arr[i].OptionType,
                    "necessary": this.arr[i].Necessary,
                    "option": this.arr[i].OpTion,
                })
            }
            // console.log(createQn)
            fetch('http://localhost:8080/api/quiz/update', {
                method: "POST",
                headers: new Headers({
                    "Accept": "application/json",
                    "Content-Type": "application/json"
                }),
                body: JSON.stringify(
                    createQn
                )
            })
                .then(res => res.json())
                .then(data => {
                    console.log(data)
                })
                .catch(error => {
                    console.error('Fetch error', error);
                })
        },
        arrIn() {
            //     if (this.qTitle.trim() === "") {
            //         alert('請輸入問卷名稱');
            //         return;
            //     } else if (this.optionType.trim() === "") {
            //         alert('請輸入問題要多選、單選還是文字');
            //         return;
            //     } else if (this.opTion.trim() === "") {
            //         alert('請輸入問題的選項');
            //         return;
            //     };
            let createquestionobj = {
                quId: this.arr.length + 1,
                QTitle: this.qTitle,
                OptionType: this.optionType,
                Necessary: this.necessary,
                OpTion: this.opTion
            }
            console.log(createquestionobj)
            this.arr.push(createquestionobj)
        },
        setPage(page) {
            if (page <= 0 || page > this.totalPage) {
                return
            }
            this.currentPage = page
        },
        setAllQn() {
            var createQn = {
                "questionnaire": {
                    "id": this.id,
                    "title": this.title,
                    "description": this.description,
                    "published": true,
                    "startDate": this.startDate,
                    "endDate": this.endDate
                },
                "question_list": []
            };
            for (let i = 0; i < this.arr.length; i++) {
                createQn.question_list.push({
                    "quId": this.arr[i].quId,
                    "qnId": this.id,
                    "qTitle": this.arr[i].QTitle,
                    "optionType": this.arr[i].OptionType,
                    "necessary": this.arr[i].Necessary,
                    "option": this.arr[i].OpTion,
                })
            }
            // console.log(createQn)
            fetch('http://localhost:8080/api/quiz/update', {
                method: "POST",
                headers: new Headers({
                    "Accept": "application/json",
                    "Content-Type": "application/json"
                }),
                body: JSON.stringify(
                    createQn
                )
            })
                .then(res => res.json())
                .then(data => {
                    console.log(data)
                })
                .catch(error => {
                    console.error('Fetch error', error);
                })
        },
        //刪除問題
        deleteQustion() {
            fetch('http://localhost:8080/api/quiz/deleteQustion', {
                method: "POST",
                headers: new Headers({
                    "Accept": "application/json",
                    "Content-Type": "application/json"
                }),
                body: JSON.stringify({
                    "qnId" : this.qnId,
                    "quList" : this.questionarr
                }
                )
            })
                .then(res => res.json())
                .then(data => {
                    console.log(data);
                    window.location.reload() 
                })
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
            return Math.ceil(this.arr.length / this.perpage)
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
            <button @click="changepage" class="topbut"> 問題</button>
            <button @click="changepage1" class="topbut"> 題目</button>
        </nav>

        <div class="question" v-show="page == 0">
            <div class="questionSecond">
                <p>
                    問卷名稱
                    <input type="text" v-model="this.title">
                </p>
                <p>
                    內容
                    <textarea style="height: 20vh;width: 15vw;" v-model="this.description"></textarea>
                </p>
                <p>
                    開始日期
                    <input type="date" v-model="this.startDate" :mix="currentDate">
                </p>
                <p>
                    結束日期
                    <input type="date" v-model="this.endDate" :min="startDate">
                </p>
            </div>
            <div class="questionLast">
                <button>
                    <RouterLink to="/BakeQuestion" style="text-decoration:none ; color: black;">取消</RouterLink>
                </button>
                <button @click="createquestion()">下一頁</button>
            </div>
        </div>

        <div class="topic" v-show="page == 1">
            <div class="topicSecond">
                <div class="secondQu">
                    <p>問卷標題
                        <input type="text" class="Qu" v-model="qTitle">

                        <select v-model="optionType">
                            <option>文字</option>
                            <option>單選</option>
                            <option>多選</option>
                        </select>
                    <p>
                        <input type="checkbox" v-model="necessary">
                        (是否必填)
                    </p>
                    </p>

                    <p>選項
                        <input type="text" class="Qu1" v-model="opTion">
                        <button class="but" @click="arrIn()">加入</button>
                    </p>
                </div>
            </div>

            <div class="topicLast">
                <div class="lastTop">
                    <p style="width: 15%;"><i class="fa-solid fa-trash" @click="changepage4()"></i></p>
                    <p>#</p>
                    <p>問卷</p>
                    <p>種類</p>
                    <p>必填</p>
                </div>
                <div class="lastSecond" v-for=" (item, index) in arr.slice(pageStart, pageEnd)">
                    <span style="width: 15%;">
                        <input type="checkbox" v-model="item.checkbox" @change="checkBox(item.quId)">
                    </span>
                    <span>
                        {{ item.quId }}
                    </span>
                    <span>{{ item.QTitle }}</span>
                    <span>{{ item.OptionType }}</span>
                    <span><input type="checkbox" v-model="item.Necessary" disabled="disabled"></span>
                </div>
            </div>
            <!-- 分頁 -->
            <div class="page" style="display: flex; align-items: center; justify-content: center;">
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
            <div class="topicButton">
                <button @click="changepage()">上一頁</button>
                <button @click="changepage2()">確認</button>
            </div>
        </div>

        <div class="Listvo" v-show="page == 2">
            <h1>{{ title }}</h1>
            <div class="listTitle">
                <h3>{{ description }}</h3>
            </div>

            <hr style="width: 100%;">

            <div class="listName">
                <label>姓名:
                    <input type="text" class="in">
                </label>
                <label>手機:
                    <input type="text" class="in">
                </label>
                <label>E-mail:
                    <input type="text" class="in">
                </label>
                <label>年齡:
                    <input type="text" class="in">
                </label>
            </div>

            <hr style="width: 100%;">

            <div class="ListTest" v-for=" item in arr">
                <div class="ListOption" v-if="item.OptionType == '文字'">
                    題目 : {{ item.QTitle }}
                    <div class="test2" v-for=" opTionIn in item.OpTion.split(';') ">
                        {{ opTionIn }}
                        <input type="text" disabled="disabled">
                    </div>
                    <hr style="width: 100%;">
                </div>

                <div class="ListOption" v-if="item.OptionType == '單選'">
                    題目 : {{ item.QTitle }}

                    <div class="test2" v-for=" opTionIn in item.OpTion.split(';') ">
                        <input type="radio" disabled="disabled">
                        {{ opTionIn }}
                    </div>
                    <hr style="width: 100%;">
                </div>

                <div class="ListOption" v-if="item.OptionType == '多選'">
                    題目 : {{ item.QTitle }}
                    <div class="test2" v-for=" opTionIn in item.OpTion.split(';') ">
                        <input type="checkbox" disabled="disabled">
                        {{ opTionIn }}
                    </div>
                    <hr style="width: 100%;">
                </div>
            </div>
            <div class="Listbut">
                <RouterLink to="/BakeQuestion">
                    <button @click="setAllQn()">暫存</button>
                </RouterLink>
                <RouterLink to="/BakeQuestion">
                    <button @click="createfetch()">送出</button>
                </RouterLink>
            </div>
        </div>

        <div class="material" v-show="page == 3">
            <div class="materiallast">
                <div class="lastTop">
                    <p>#</p>
                    <p>姓名</p>
                    <p>填寫日期</p>
                    <p>觀看細節</p>
                </div>
                <div class="lastSecond" v-for=" (item, index) in arr">
                    <span>
                        {{ item.Id }}
                    </span>
                    <span>{{ item.QTitle }}</span>
                    <span>{{ item.OptionType }}</span>

                    <span><input type="checkbox"></span>

                </div>
            </div>
        </div>

        <!-- 刪除小分頁 -->
        <div class="changePage" v-show="page == 4">
            <i class="fa-solid fa-xmark" @click="changepage1"></i>
            <div class="changePageTop">
                <h1>確定刪除:</h1>
                <span>{{ questionarr }}</span>
                <button class="changePageBut" @click="deleteQustion">刪除</button>
            </div>
        </div>
    </div>
</template>

<style lang="scss" scoped>
.all {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    align-items: center;
    background-color: #BEDAE5;
    // position: relative;
}

.topNav {
    width: 70%;
    height: 10%;
    display: flex;
    justify-content: space-around;
    background-color: #EECB27;
    font-size: 24pt;
    color: white;

    .topbut {
        background: none;
        border: none;
        transition: 1s;
        color: red;
        font-size: 20pt;

        &:hover {
            font-size: 26pt;
        }
    }
}

.question {
    height: 80vh;
    width: 70%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    background-color: #EECB27;

    .questionSecond {
        height: 70%;
        width: 100%;
        display: flex;
        flex-direction: column;
        justify-content: space-around;
        align-items: center;

        p {
            width: 60%;
            display: flex;
            justify-content: space-around;
            align-items: center;
        }
    }

    .questionLast {
        height: 5%;
        width: 35%;
        display: flex;
        margin-top: 5%;
        margin-left: 80%;

        button {
            margin-right: 20%;
        }
    }
}

.topic {
    height: 80vh;
    width: 70%;
    display: flex;
    flex-direction: column;
    background-color: #EECB27;

    .topicSecond {
        height: 25%;
        width: 100%;
        margin-top: 1%;
        display: flex;
        flex-direction: column;
        justify-content: space-between;

        .secondQu {
            height: 100%;
            width: 100%;
            display: flex;
            flex-direction: column;
            justify-content: space-around;

            p {
                display: flex;
                margin-left: 10%;
                font-size: 15pt;

                .Qu {
                    margin-right: 10%;
                }

                .Qu1 {
                    margin-left: 4.5%;
                    margin-right: 40%;
                }
            }
        }
    }

    .topicLast {
        margin-top: 5%;
        height: 55%;
        width: 100;
        display: flex;
        flex-direction: column;

        .lastTop {
            height: 10%;
            width: 100%;
            display: flex;
            justify-content: space-around;
            border: 1px solid black;

            p {
                height: 100%;
                width: 30%;
                border: 1px solid black;
            }
        }

        .lastSecond {
            height: 10%;
            width: 100%;
            display: flex;
            justify-content: space-around;
            margin-top: 1%;

            span {
                height: 10%;
                width: 30%;
            }

            // border: 1px solid black;
        }
    }

    .topicButton {
        height: 6%;
        width: 30%;
        margin-left: 70%;
        display: flex;
        justify-content: space-around;
    }
}

.Listvo {
    height: 250vh;
    width: 50%;
    margin-top: 3%;
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    align-items: center;
    background-color: #EECB27;

    // position: absolute;
    .listTitle {
        height: 15%;
        display: flex;
        flex-direction: column;
        justify-content: space-around;
    }

    .listName {
        height: 15%;
        width: 50%;
        display: flex;
        flex-direction: column;
        justify-content: space-around;
        align-items: center;

        label {
            height: 15%;
            width: 60%;
        }
    }

    .ListTest {
        height: 10%;
        width: 50%;
        display: flex;
        flex-direction: column;
        justify-content: space-between;

        .ListOption {
            height: 100%;
            width: 100%;
            display: flex;
            flex-direction: column;
            justify-content: space-around;
            align-items: center;
        }
    }

    .Listbut {
        width: 30%;
        display: flex;
        justify-content: space-around;
        align-items: center;
    }
}

.material {
    height: 80vh;
    width: 70%;
    background-color: #EECB27;

    .materiallast {
        margin-top: 5%;
        height: 55%;
        width: 100;
        display: flex;
        flex-direction: column;
        justify-content: center;

        .lastTop {
            height: 10%;
            width: 100%;
            display: flex;
            justify-content: space-around;
            border: 1px solid black;
        }

        .lastSecond {
            height: 50%;
            width: 100%;
            display: flex;
            border: 1px solid black;
        }
    }
}

.total {
    height: 80vh;
    width: 70%;
    background-color: #EECB27;

}

.changePage {
    height: 100vh;
    width: 70vw;
    display: flex;
    flex-direction: column;
    color: white;
    background-color: #6d6d6e;

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
</style>
