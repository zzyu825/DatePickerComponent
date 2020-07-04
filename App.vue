<template>
    <div>
        <!-- <date-picker :date="date" @choose-date="date = $event"/> -->
        <date-picker v-model="date"/>
    </div>
</template>

<script>
import datePicker from './DatePicker'

export default {
    components: {
        datePicker
    },
    data() {
        return {
            date: new Date()
        }
    }
}
</script>