<template>
    <div class="calculator">
        <div class="size_field">
            <span>Введіть розмірність матриці:</span>
            <input type="number" name="size" id="size" v-model="size" />
        </div>
        <div class="wrapper">
            <div>
                <div v-for="(el, id) in matrix" :key="id" class="table">
                    <div v-for="(el1, id1) in el" :key="id1" class="cell">
                        <input type="number" name="number" :id="id * size + id1" v-model="matrix[id][id1]"
                            class="cell" />
                    </div>
                </div>
            </div>
            <div class="elements">
                <div v-for="(el, ind) in vector" :key="el + ind * ind">
                    <input type="number" name="number" v-model="vector[ind]" class="cell" />
                </div>
            </div>
        </div>
        <button @click="det(matrix)">Обчислити детермінант</button>
        <button @click="method_zeidel">Метод Зейделя</button>
        <button @click="method_yacobi">Метод Якобі</button>
        <div>{{determinant}}</div>
    </div>
</template>
  
<script setup>
import { ref, watch } from 'vue';

const size = ref(3)
let matrix = ref([])
const determinant = ref(0)
let eps = ref(0.0001)
let vector = ref([])
function det(M) {
    if (M.length == 2) {
        determinant.value = (M[0][0] * M[1][1]) - (M[0][1] * M[1][0])
        return (M[0][0] * M[1][1]) - (M[0][1] * M[1][0]);
    }
    let answer = 0;
    for (var i = 0; i < M.length; i++) {
        answer += Math.pow(-1, i) * M[0][i] * det(deleteRowAndColumn(M, i));
    }
    determinant.value = answer;
}

function deleteRowAndColumn(M, index) {
    var temp = [];

    for (var i = 0; i < M.length; i++) { temp.push(M[i].slice(0)); }
    temp.splice(0, 1);
    for (i = 0; i < temp.length; i++) { temp[i].splice(index, 1); }
    return temp;
}

watch(size, () => {
    let arr = [];
    for (let i = 0; i < size.value; i++) {
        let row = [];
        for (let j = 0; j < size.value; j++) {
            row.push(0)
        }
        arr.push(row)
    }
    vector.value = new Array(size.value).fill(0)
    console.log(vector, 'suka')
    matrix.value = arr
    return arr
}, { immediate: true })


function method_zeidel() {
    let x_vector = new Array(size.value).fill(0)
    const el = {}
    let converge = false
    let counter = 0
    while (!converge) {
        let x_old = Object.assign({}, x_vector)
        for (let i = 0; i < size.value; i++) {
            let x_new = Object.assign({}, x_vector)
            x_new[i] = 1
            el[`s${i}`] = matrix.value[i].map((element) => element * -1)
            el[`s${i}`][i] = vector.value[i]
            console.log(el[`s${i}`])
            el[`s${i}`] = el[`s${i}`].map((element) => element / matrix.value[i][i])
            console.log(vector.value)
            console.log(el[`s${i}`])
            el[`s${i}_sum`] = sum_array(el[`s${i}`].map((element, index) => element * x_new[index]))
            x_vector[i] = el[`s${i}_sum`].toFixed(3)
            console.log(el[`s${i}_sum`])
        }
        converge = Array.from(x_old).map((element, index) => {
            return Math.abs(element - x_vector[index]) < eps.value
        })
        console.log(counter, "COUNTER")
    }
}
function sum_array(arr) {
    let summ = 0
    for (let i = 0; i < arr.length; i++) {
        summ += arr[i]
    }
    return summ
}
function method_yacobi() {
    let x_vector = new Array(size.value).fill(0)
    const el = {}
    let converge = false
    let counter = 0
    while (!converge) {
        let test = []
        for (let i = 0; i < size.value; i++) {
            let x_new = Object.assign({}, x_vector)
            x_new[i] = 1
            el[`s${i}`] = matrix.value[i].map((element) => element * -1)
            el[`s${i}`][i] = vector.value[i]
            console.log(el[`s${i}`])
            el[`s${i}`] = el[`s${i}`].map((element) => element / matrix.value[i][i])
            console.log(vector.value)
            console.log(el[`s${i}`])
            el[`s${i}_sum`] = sum_array(el[`s${i}`].map((element, index) => element * x_new[index]))
            test[i] = el[`s${i}_sum`].toFixed(3)
            console.log(el[`s${i}_sum`])
        }
        x_vector = test
        counter = counter + 1
        if (counter == 2) {
            converge = true
        }
        console.log(counter, "COUNTER")
    }
}
</script>


<style scoped>
.table {
    display: flex;
}

.wrapper {
    display: flex;
    height: 200px;
}

.cell {
    width: 50px;
}

.calculator {
    display: flex;
    flex-direction: column;
    align-items: center;
}


.size_field {
    display: flex;
    flex-direction: column;
    justify-content: center;
    margin-bottom: 10px;
}

.elements {
    margin-left: 20px;
}
</style>


