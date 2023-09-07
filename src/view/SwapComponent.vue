<template>
    <div class="transactions">
        <div class="transactions-data">
            <div class="total-txns">
                A total of {{ totalTXNS }} transactions found
            </div>
            <table>
                <thead>
                    <tr>
                        <td>
                            <div class="table-heading">
                                transaction
                            </div>
                        </td>
                        <td>
                            <div class="table-heading">
                                sender
                            </div>
                        </td>
                        <td>
                            <div class="table-heading">
                                reciever
                            </div>
                        </td>
                        <td>
                            <div class="table-heading">
                                amount
                            </div>
                        </td>
                        <td>
                            <div class="table-heading">
                                timestamp
                            </div>
                        </td>
                    </tr>
                </thead>
                <tbody>
                    <tr v-if="err">
                        <td>
                            <span> error ...</span>
                        </td>
                    </tr>
                    <tr v-else v-for="swap in displaytrxns" :key="swap.id">
                        <td>
                            <div class="table-body" :title="swap.id">
                                {{ formatText(swap.id) }}
                            </div>
                        </td>
                        <td>
                            <div class="table-body" :title="swap.tokenIn">
                                {{ formatText(swap.tokenIn) }}
                            </div>
                        </td>
                        <td>
                            <div class="table-body" :title="swap.tokenOut">
                                {{ formatText(swap.tokenOut) }}
                            </div>
                        </td>
                        <td>
                            <div class="table-body" :title="swap.tokenAmountIn">
                                {{ formatText(swap.tokenAmountIn) }}
                            </div>
                        </td>
                        <td>
                            <div class="table-body" :title="formatTime(swap.timestamp)">
                                {{ formatTime(swap.timestamp) }}
                            </div>
                        </td>

                    </tr>
                </tbody>
            </table>
            <div class="button-more">
                <button :disabled="disabeButtonMore" @click="loadMore">More</button>
            </div>
        </div>
    </div>
</template>
<script setup>
import gql from 'graphql-tag'
import { useQuery } from '@vue/apollo-composable'
import { onMounted, reactive, ref, toRefs, toRef, unref } from 'vue'
const err = ref(false)
const load = ref(false)
const totalTXNS = ref(0)
const increment=ref(1)
const disabeButtonMore=ref(false)
let alltransactions = reactive([])
let displaytrxns = reactive([])
const QUERY = gql`
query{
 swaps { 
   id
  timestamp
  tokenIn
  tokenOut
  userAddress {
    id
  }
  tokenAmountIn
  tokenAmountOut
  tokenInSym
  tokenOutSym
 }
}
`
async function getTransactionFromServer() {
    try {
        const { error, loading, result } = useQuery(QUERY)
        err.value = unref(error)
        load.value = unref(loading)
        setTimeout(() => {
            unref(result).swaps.forEach((elem, index) => {
                alltransactions.push(elem)
                if(index<5){
                    displaytrxns.push(elem)
                }
            });
        }, 1000)
    } catch (e) {

        console.log(e)
    }
}
function formatText(text) {
    let newtext
    text.length > 20 ? newtext = text.slice(0, 20) + '...' : newtext = text
    return newtext
}
function formatTime(time) {
    return new Date(time)
}
function loadMore(){
    let newarr= alltransactions.slice(increment.value*5, (increment.value*5+4)>alltransactions.length? alltransactions.length : increment.value*5+4)
    newarr.forEach(elem=>{
        displaytrxns.push(elem)
    })
    increment.value++

    if (increment.value*5>alltransactions.length){
        disabeButtonMore.value=true
    }
}
onMounted(async () => {
    await getTransactionFromServer()
})



</script>
<style lang="scss">
.button-more{
    margin-top: 20px;
}
.table-heading {
    font-weight: 600;
    font-size: 16px;
    padding: 3px 5px;
}

.table-body {
    font-size: 14px;
}

.transactions-data {
    padding: 0.75rem;
    border-radius: 5px;
    box-shadow: 0 0.5rem 1.2rem rgba(189, 197, 209, .2);
    background-color: #fff;
    background-clip: border-box;
    border: 1px solid #e7eaf3;


    table {
        max-width: 100%;
    }

    thead {
        background-color: #e7eaf3;

        td {
            border-color: #000;
        }
    }

    tbody {
        td {
            border-bottom: 1px solid #e7eaf3;
        }
    }

    td {
        width: 200px;
        max-width: 200px;
        margin: 0;
        padding: 0;
        border: 0;

        &:focus-visible {
            outline: -webkit-focus-ring-color auto 0;
        }
    }

}
</style>