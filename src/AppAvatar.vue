<template>
    <div class="avatar">
        <img :src="avatar">
    </div>
</template>

<script>
    export default {
        props: {
            avatar: String
        }

    }
</script>

<style scoped>

</style>
