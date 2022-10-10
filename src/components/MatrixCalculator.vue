<template>
    <div class="calculator">
        <div class="size_field">
            <span>Введіть розмірність матриці:</span>
            <input type="number" name="size" id="size" v-model="size" />
            <span>Введіть eps:</span>
            <input type="number" name="eps" id="eps" v-model="eps" />

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
        <button @click="method_zeidel">Метод Зейделя</button>
        <button @click="method_yacobi">Метод Якобі</button>


        <div class="history">
            <div v-for="(el, index) in history" :key="index">
                N ={{index+1}}
                <div v-for="(element, indx) in el" :key="indx + 24" style="margin-right:15px">
                    x{{indx+1}}={{element}}
                </div>
            </div>
        </div>
    </div>
</template>
  
<script setup>
import { ref, watch } from 'vue';

const size = ref(3)
let matrix = ref([])
let eps = ref(0.0001)
let history = ref([])
let vector = ref([])

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
    history.value = []
    let x_vector = new Array(size.value).fill(0)
    const el = {}
    let converge = false
    let counter = 0
    while (!converge) {
        let x_old;
        if (counter == 0) {
            x_old = new Array(size.value).fill(0)
        }
        else {
            x_old = Object.assign([], x_vector)
        }
        for (let i = 0; i < size.value; i++) {
            let x_new = Object.assign({}, x_vector)
            x_new[i] = 1
            el[`s${i}`] = matrix.value[i].map((element) => element * -1)
            el[`s${i}`][i] = vector.value[i]
            el[`s${i}`] = el[`s${i}`].map((element) => element / matrix.value[i][i])
            el[`s${i}_sum`] = sum_array(el[`s${i}`].map((element, index) => element * x_new[index]))
            x_vector[i] = el[`s${i}_sum`].toFixed(3)
        }
        history.value.push(Object.assign([], x_vector))
        converge = Math.abs(sum_array(x_vector) - sum_array(x_old)) <= eps.value
        counter++
    }
}
function sum_array(arr) {
    let summ = 0
    for (let i = 0; i < arr.length; i++) {
        summ += Number(arr[i])
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
        converge = Math.abs(sum_array(x_vector) - sum_array(test)) <= eps.value
        x_vector = test
        history.value.push(x_vector)
        counter = counter + 1

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

.history {
    display: flex;
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


