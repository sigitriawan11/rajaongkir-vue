<template>
    <select n></select>
</template>

<script>
import { reactive, toRefs, ref } from 'vue'

export default {
    setup() {
        const couriers = reactive({
            lists: [
                {
                    val: "jne",
                    name: "JNE"
                },
                {
                    val: "jnt",
                    name: "JNT"
                }
            ]
        })

        return {
            ...toRefs(couriers)
        }
    },
}
</script>