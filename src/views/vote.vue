<script>
import { RouterLink, RouterView } from "vue-router";
export default {
    data() {
        return {
            arr: [],
            id: 0,
            title: "",
            description: "",
            //問題
            quId: 0,
            QTitle: "",
            OptionType: "",
            Necessary: false,
            OpTion: "",
            //填寫
            name: "",
            phone: "",
            email: "",
            age: 0,
            ages: Array.from({ length: 91 }, (_, index) => index + 10),
            // 回答
            answer: [],
        };
    },
    methods: {
        useranswer() {
            var checkOk = true
            this.answer.forEach(item => {
                if (item.necessary == true && item.ans == "") {
                    alert("請輸入必填內容")
                    checkOk = false
                }
            });
            if (checkOk) {
                this.checkUser()
            }
            // console.log(this.answer);

        },
        checkUser() {
            var finnalans = "";

            for (let i = 0; i < this.answer.length; i++) {
                // console.log(this.answer[i]);
                if (this.answer[i].optionType == '多選') {
                    for (let k = 0; k < this.answer[i].ans.length; k++) {
                        // console.log(this.answer[i].ans[k]);
                        finnalans = finnalans + this.answer[i].ans[k] + ";"
                    }
                    // console.log(finnalans);
                    this.answer[i].ans = finnalans
                }

            }
            // console.log(finnalans);
            // console.log(this.answer);
            // console.log(this.ans);
            let user = {
                userList: []
            }
            for (let i = 0; i < this.answer.length; i++) {
                user.userList.push({
                    "qnId": this.id,
                    "qId": this.answer[i].quId,
                    "name": this.name,
                    "phoneNumber": this.phone,
                    "email": this.email,
                    "age": parseInt(this.age),
                    "ans": this.answer[i].ans
                });
            }
            fetch('http://localhost:8080/api/quiz/setUser', {
                method: "POST",
                headers: new Headers({
                    "Accept": "application/json",
                    "Content-Type": "application/json"
                }),
                body: JSON.stringify(
                    user
                )
            })
                .then(res => res.json())
                .then(data => {
                        window.location.href = "/";
                })
        }

    },
    mounted() {
        const serializedObject = this.$route.params.item;
        const receivedObject = JSON.parse(serializedObject);

        this.arr = receivedObject;
        this.title = this.arr.questionnaire.title;
        this.description = this.arr.questionnaire.description;
        this.id = this.arr.questionnaire.id;

        console.log(this.arr);
        if (Array.isArray(this.arr.question_list)) {
            for (let i = 0; i < this.arr.question_list.length; i++) {
                this.answer.push({
                    quId: this.arr.question_list[i].quId,
                    qTitle: this.arr.question_list[i].qTitle,
                    optionType: this.arr.question_list[i].optionType,
                    necessary: this.arr.question_list[i].necessary,
                    opTion: this.arr.question_list[i].opTion,
                    ans: []
                });
            }
        }
        console.log(this.answer);
    },
    components: {
        RouterLink
    }
}

</script>

<template>
    <div class="all">
        <div class="votetop">
            <br>
            <h1>{{ this.title }}</h1>
            <br>
            <div class="votesecond">
                <h3>{{ this.description }}</h3>
            </div>
            <br>

            <div class="votethree">
                <p>姓名:
                    <input type="text" class="in" v-model="this.name">
                </p>
                <p>手機:
                    <input type="text" class="in" v-model="this.phone">
                </p>
                <p>E-mail:
                    <input type="text" class="in" v-model="this.email">
                </p>
                <p>年齡:
                    <select v-model="this.age">
                        <option v-for="age in ages" :key="age">{{ age }}</option>
                    </select>
                </p>
            </div>

            <h3 style="margin-top: 3%;">投票項目</h3>
            <div class="ListTest" v-for=" item in answer">
                <div class="ListOption" v-if="item.optionType == '文字'">
                    <span>題目 : {{ item.qTitle }}
                        <span v-if="item.necessary == true" style="color: green; margin-left: 5%;">*必填</span>
                    </span>
                    <div class="test1" v-for=" (opTionIn, opTionIndex) in item.opTion.split()">
                        {{ opTionIn }}
                        <input type="text" v-model="item.ans">
                    </div>
                    <hr style="width: 100%;">
                </div>

                <div class="ListOption" v-if="item.optionType == '單選'">
                    <span>題目 : {{ item.qTitle }}
                        <span v-if="item.necessary == true" style="color: green; margin-left: 5%;">*必填</span>
                    </span>
                    <div class="test2" v-for="(opTionIn, opTionIndex) in item.opTion.split(';')" :key="opTionIndex">
                        {{ opTionIn }}
                        <input type="radio" v-model="item.ans" :value="opTionIn">
                    </div>
                    <hr style="width: 100%;">
                </div>

                <div class="ListOption" v-if="item.optionType == '多選'">
                    <span>題目 : {{ item.qTitle }}
                        <span v-if="item.necessary == true" style="color: green; margin-left: 5%;">*必填</span>
                    </span>
                    <div class="test2" v-for=" opTionIn in item.opTion.split(';') ">
                        {{ opTionIn }}
                        <input type="checkbox" v-model="item.ans" :value="opTionIn">
                    </div>
                    <hr style="width: 100%;">
                </div>
            </div>

            <div class="votelast">
                <button @click="useranswer()" style="border: none; background:none;">
                    送出
                </button>
            </div>
        </div>

    </div>
</template>

<style lang="scss" scoped>
.all {
    height: 150vh;
    width: 100vw;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    background-color: #BEDAE5;
}

.votetop {
    height: 90%;
    width: 60%;
    display: flex;
    flex-direction: column;
    align-items: center;
    background-color: #EECB27;
    position: relative;

}

.votesecond {
    height: 10%;
    width: 80%;
}

.votethree {
    height: 25%;
    width: 60%;
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    align-items: center;
    color: #1F1762;

    p {
        display: flex;
        justify-content: space-around;
        height: 30%;

        .in {
            display: flex;
        }
    }

}

.ListTest {
    height: 150vh;
    width: 50%;
    display: flex;
    flex-direction: column;
    justify-content: space-between;

    .ListOption {
        height: 90%;
        width: 100%;
        display: flex;
        flex-direction: column;
        // justify-content: space-around;
        // align-items: center;

        span {
            display: flex;
            // justify-content: center;
            color: rgb(255, 0, 0);
        }

        .test1 {
            height: 50%;
            width: 80%;
            display: flex;
            justify-content: space-around;
            align-items: center;
        }

        .test2 {
            width: 30%;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
    }
}

.Listbut {
    width: 30%;
    display: flex;
    justify-content: space-around;
    align-items: center;
}

.votelast {
    height: 3%;
    width: 35%;
    margin-top: 5%;
    margin-left: 70%;
    display: flex;
    justify-content: space-between;

    .button {
        height: 5%;
        width: 10%;
        bottom: 0;
        position: absolute;
        display: flex;
        align-items: center;
        justify-content: center;
        text-decoration: none;
        border: 1px solid rgb(247, 113, 113);
        border-radius: 55%;
        text-decoration: none;

    }

    .button1 {
        height: 5%;
        width: 10%;
        bottom: 0;
        position: absolute;
        display: flex;
        justify-content: center;
        align-items: center;
        text-decoration: none;
        margin-left: 20%;
        border: 1px solid rgb(247, 113, 113);
        border-radius: 55%;
    }
}
</style>
