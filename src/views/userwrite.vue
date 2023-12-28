
<script>
import { RouterLink, RouterView } from "vue-router";
export default {
    data() {
        return {
            arr: [],
            // user
            age: 0,
            ans: "",
            email: "",
            id: 0,
            name: "",
            phoneNumber: "",
            questionnaireId: 0,
            questionnaireTitle: "",
            questionList: [],
            description: "",
            user: [],
            ans: "",
        };
    },
    methods: {

    },
    mounted() {
        const serializedObject = this.$route.params.user;
        const receivedObject = JSON.parse(serializedObject);
        this.arr = receivedObject
        this.age = this.arr.age
        this.ans = this.arr.ans
        this.email = this.arr.email
        this.id = this.arr.id
        this.name = this.arr.name
        this.phoneNumber = this.arr.phoneNumber
        this.questionnaireId = this.arr.questionnaireId
        this.questionnaireTitle = this.arr.questionnaireTitle
        this.description = this.arr.description
        this.questionList = this.arr.question_list
        // this.allArr = this.arr
        console.log(this.arr);
        console.log(this.questionList);

        fetch('http://localhost:8080/api/quiz/getUser?qnId=' + this.questionnaireId, {
            method: "GET",
            headers: new Headers({
                "Accept": "application/json",
                "Content-Type": "application/json"
            }),
        })
            .then(res => res.json())
            .then(data => {
                // console.log(data);
                var answer = [];
                if (Array.isArray(data.userList)) {
                    for (let i = 0; i < data.userList.length; i++) {
                        answer.push({
                            ans: data.userList[i].ans,
                            id: data.userList[i].qId,
                            username: data.userList[i].name,
                        });
                    }
                }
                this.user = answer
                console.log(this.user);
            })
    },
    components: {
        RouterLink
    },


}
</script>

<template>
    <div class="all">
        <div class="all2">
            <div class="contop">
                <h1>
                    {{ questionnaireTitle }}
                </h1>
            </div>

            <div class="consecond">
                <h3>{{ description }}</h3>
            </div>

            <div class="conthree">
                <p>姓名: {{ name }}</p>
                <p>手機: {{ phoneNumber }}</p>
                <p>E-mail: {{ email }}</p>
                <p>年齡: {{ age }}</p>
            </div>

            <h3 style="margin-top: 3%;">投票項目</h3>
            <div v-for="item in questionList" class="answerBox">
                <p>{{ item.quId }}.</p>
                <p>{{ item.qTitle }} :</p>
                <div v-for="itemuser in this.user">
                    <div v-if="itemuser.username == this.name && itemuser.id == item.quId">
                        <label
                            style="color: rgb(240, 124, 124); font-size: 30pt; display: flex; justify-content: center;">{{
                                itemuser.ans }}</label>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style lang="scss" scoped>
.all {
    height: 150vh;
    width: 100vw;
    background-color: #BEDAE5;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}

.all2 {
    height: 80%;
    width: 70%;
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    align-items: center;
    background-color: #EECB27;
    margin-top: 5%;
}

.contop {
    height: 20%;
}

.consecond {
    height: 20%;
}

.conthree {
    height: 30%;
    width: 70%;
    display: flex;
    flex-direction: column;

    p {
        display: flex;
        align-items: center;
        font-size: 16pt;
    }
}

.answerBox {
    height: 25%;
    width: 70%;
    display: flex;
    align-items: center;

    p {
        height: 100%;
        color: rgb(0, 0, 0);
        font-size: 26pt;
        display: flex;
        align-items: center;
        // justify-content: center;
    }
}
</style>
