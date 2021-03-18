<template>
    <div class="form-control">
        <label :for="id">{{ label }}</label>
        <textarea
                rows="3"
                :value="modelValue"
                @input="change"
        ></textarea>
    </div>
</template>

<script>
    export default {
        emits: ['update:modelValue'],
        props: {
            modelValue: String,
            error: String,
            label: String,
            id: String
        },
        methods: {
            change() {
                this.$emit('update:modelValue', event.target.value)
            }
        }

    }
</script>

<style scoped>

</style>
