<script>
import { Colors } from 'chart.js';
import Chart from 'chart.js/auto';

import { RouterLink, RouterView } from "vue-router";
export default {
    data() {
        return {
            qnId: 0,
            title: "",
            opTion: "",
            phone: "",
            email: "",
            age: "",
            arr: [],
            // 統計
            answer: [],
            answerText: [],
            user: [],
            radionum_a: 0,
            x: [],
            xText: [],
            y: [],
            page: 0,

        };
    },
    methods: {
        //單多選
        fetchuser(QuestionList) {
            //統計 單多選
            fetch('http://localhost:8080/api/quiz/getUser?qnId=' + this.qnId, {
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
                    // if (Array.isArray(data.userList)) {
                    for (let i = 0; i < data.userList.length; i++) {
                        answer.push({
                            ans: data.userList[i].ans,
                            id: data.userList[i].qId,
                        });
                    }
                    this.user = answer
                    var useransList = []
                    for (let k = 0; k < QuestionList.length; k++) {
                        var userans = []
                        for (let i = 0; i < data.userList.length; i++) {
                            if (QuestionList[k].quId == data.userList[i].qId) {
                                userans.push({
                                    ans: data.userList[i].ans,
                                });
                            }
                        }
                        useransList.push(userans)
                    }
                    // }
                    // console.log(useransList);
                    // console.log(QuestionList);

                    this.radio(QuestionList, useransList)
                })
        },
        //單多選
        radio(QuestionList, useransList) {
            var datayList = []
            for (let k = 0; k < useransList.length; k++) {
                var datay = ""
                for (let i = 0; i < useransList[k].length; i++) {
                    // console.log(useransList[k][i]);
                    datay = datay + useransList[k][i].ans + ";"
                }
                datayList.push(datay)
            }
            // console.log(datayList);
            // 統計x
            for (let i = 0; i < QuestionList.length; i++) {
                this.x.push(QuestionList[i].opTion.split(';'))
            }
            console.log(this.x);
            // data資料
            var charY = []
            for (let i = 0; i < datayList.length; i++) {
                charY.push({
                    ans: datayList[i].split(';')
                })
            }
            // console.log(charY[0].ans[0]);
            // console.log(this.x[0][0]);
            // console.log(this.x);
            // console.log(charY);
            let Xaxis = []
            let Yaxis = []
            for (let i = 0; i < this.x.length; i++) {
                let innerYaxis = []; // Inner array for Yaxis values corresponding to each set of options
                for (let j = 0; j < this.x[i].length; j++) {
                    let count = 0;
                    for (let k = 0; k < charY[i].ans.length; k++) {
                        if (this.x[i][j] == charY[i].ans[k]) {
                            count++;
                        }
                    }
                    innerYaxis.push(count); // Push count for each option into the inner array
                    Xaxis.push(this.x[i][j]); // Push option into the Xaxis array
                }
                Yaxis.push(innerYaxis); // Push the inner array into Yaxis
            }

            // console.log(Xaxis);
            // console.log(Yaxis);

            // this.long(Yaxis);
            this.pie1(Yaxis);
        },
        //單多選
        pie1(Yaxis) {
            for (let i = 0; i < this.x.length; i++) {

                const ctx = document.getElementById("myChart" + i);

                new Chart(ctx, {
                    type: 'doughnut',
                    data: {
                        labels: this.x[i],
                        datasets: this.createDatasets(Yaxis[i])
                    },
                    // options: {
                    //     scales: {
                    //         y: {
                    //             beginAtZero: true
                    //         }
                    //     }
                    // }
                });
            }
        },
        //文字
        fetchuserText(QuestionList) {
            //統計
            fetch('http://localhost:8080/api/quiz/getUser?qnId=' + this.qnId, {
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
                            });
                        }
                        // this.user = answer
                        var useransList = []
                        for (let k = 0; k < QuestionList.length; k++) {
                            var userans = []
                            for (let i = 0; i < data.userList.length; i++) {
                                if (QuestionList[k].quId == data.userList[i].qId) {
                                    userans.push({
                                        ans: data.userList[i].ans,
                                    });
                                }
                            }
                            useransList.push(userans)
                        }
                    }
                    // console.log(useransList);
                    console.log(QuestionList);
                    this.answerText = QuestionList
                    console.log(this.answerText);
                    this.text(QuestionList, useransList)
                })
        },
        //文字
        text(QuestionList, useransList) {
            const datayList = useransList.map(userans => userans.map(ans => ans.ans).join(';'));

            console.log(datayList);

            // 判斷是否多題
            if (datayList.length > 1) {
                // 初始化 xText 和 Yaxis
                const xText = [];
                const Yaxis = [];

                // 統計 Yaxis for 多題
                datayList.forEach(datay => {
                    // 刪掉; 去除空字串 並存取
                    const options = [...new Set(datay.split(";").filter(Boolean))];
                    //先放入陣列 ，讓他一題一個陣列
                    xText.push(options);
                    // 這裡在計算這陣列內有幾個一樣的
                    Yaxis.push(options.map(option => datay.split(';').filter(ans => ans === option).length));

                });

                // console.log(xText);
                // console.log(Yaxis);

                this.long(xText, Yaxis);
            } else {
                // 單題的處理方式，這裡可以根據你的需求進行修改
                this.xText = datayList[0].split(";").filter(Boolean);
                const Yaxis = this.xText.map(option => datayList[0].split(';').filter(ans => ans === option).length);

                console.log(Yaxis);

                this.long(this.xText, [Yaxis]);
            }
        },
        long(xText, Yaxis) {
            console.log(xText);
            console.log(Yaxis);

            this.$nextTick(() => {
                if (!Array.isArray(xText[0])) {
                    for (let i = 0; i < xText.length; i++) {
                        const ctx = document.getElementById("myChartText" + i);

                        if (ctx) {
                            new Chart(ctx, {
                                type: 'bar',
                                data: {
                                    labels: xText,
                                    datasets: this.createDatasets(Yaxis[i])
                                },
                                options: {
                                    scales: {
                                        y: {
                                            beginAtZero: true
                                        }
                                    }
                                }
                            });
                        }
                    }
                } else {
                    // 多個問題的情況
                    for (let i = 0; i < xText.length; i++) {
                        const ctx = document.getElementById("myChartText" + i);

                        if (ctx) {
                            new Chart(ctx, {
                                type: 'bar',
                                data: {
                                    labels: xText[i],
                                    datasets: this.createDatasets(Yaxis[i])
                                },
                                options: {
                                    scales: {
                                        y: {
                                            beginAtZero: true
                                        }
                                    }
                                }
                            });
                        }
                    }
                }
            });
        },
        createDatasets(data) {
            return [{
                label: this.title,
                data: data, // Use the inner array as the data
                // borderWidth: 1,
                // hoverOffset: 50,
                // hoverBorderWidth: 3,
                // hoverBorderColor: 'purple',
            }];
        }
    },
    mounted() {
        const serializedObject = this.$route.params.ansewer;
        const receivedObject = JSON.parse(serializedObject);
        // this.x = receivedObject
        this.arr = receivedObject
        this.qnId = this.arr.id
        this.title = this.arr.title
        //題目
        fetch('http://localhost:8080/api/quiz/searchQuestionList?qnId=' + this.qnId, {
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
                var answerText = [];
                for (let i = 0; i < data.questionList.length; i++) {
                    if (data.questionList[i].optionType == '文字') {
                        answerText.push({
                            quId: data.questionList[i].quId,
                            qTitle: data.questionList[i].qTitle,
                            optionType: data.questionList[i].optionType,
                            necessary: data.questionList[i].necessary,
                            opTion: data.questionList[i].opTion,
                        });
                    } else {
                        answer.push({
                            quId: data.questionList[i].quId,
                            qTitle: data.questionList[i].qTitle,
                            optionType: data.questionList[i].optionType,
                            necessary: data.questionList[i].necessary,
                            opTion: data.questionList[i].opTion,
                        });
                    }
                }
                this.answer = answer
                this.fetchuser(answer)
                //Text
                this.fetchuserText(answerText)
                console.log(answerText);
                console.log(answer);
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
            <canvas :id="'myChart' + index" class="myChartLong" v-for="(item, index) in answer" :key="index"></canvas>
            <canvas :id="'myChartText' + index" class="myChartLongT" v-for="(item, index) in this.answerText"
                :key="index"></canvas>
        </div>
    </div>
</template>

<style lang="scss" scoped>
.all {
    height: 85vh;
    width: 100vw;
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    align-items: center;
    background-color: #BEDAE5;
    /* 添加垂直滾動條 */
}

.all2 {
    height: 100%;
    width: 60%;
    display: flex;
    flex-direction: column;
    overflow-y: auto;
}

.myChartLong {
    height: 10%;
    width: 10%;
    background-color: #f9f9f9;
}

.myChartLongT {
    height: 10%;
    width: 10%;
    background-color: #f9f9f9;

}
</style>
