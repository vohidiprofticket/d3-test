<script setup>
import SwapComponent from './SwapComponent.vue';
import { ref } from 'vue'
const isconnect = ref(false)
const connectToken = ref("")
const loading = ref(false)

//wallet connect metamax
async function connectMetamax() {
    try {
        //wallet connection
        loading.value=true
        setTimeout(() => {
            isconnect.value = true
            connectToken.value = "0x003e441e702fcc259f"
            loading.value=false
        }, 500)

    } catch (e) {
        console.log(e)
    }

}
</script>

<template>
    <div class="main">
        <div class="header">
            <div class="metamax">
                <div class="info">

                </div>
                <div class="connect">
                    <button @click="connectMetamax">
                        {{ loading? 'loading...' :(!isconnect ? 'connect' : connectToken)  }}
                    </button>
                    
                </div>
            </div>
        </div>
        <div class="content">

            <swap-component />

        </div>
    </div>
</template>
<style lang="scss">
.header {
    margin-bottom: 15px;
}

.metamax {
    display: flex;
    align-items: center;
    justify-content: flex-end;

    .connect {
        button {
            background: #646cff;
            color: #fff;
            letter-spacing: 0.2px;
            display: flex;
            align-items: center;
            justify-content: center;

            &:hover {
                transition: all 0.3s ease-in;
                opacity: 0.8;
            }
        }
    }
}</style>