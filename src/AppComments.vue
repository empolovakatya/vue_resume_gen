<template>
    <div class="container">
    <p>
        <button class="btn primary"
                @click="$emit('load')"
        >Загрузить комментарии</button>
    </p>
        <div class="card">
            <h2>{{ label }}</h2>
            <ul class="list" v-for="comment in comments" :key="comment.id">
                <li class="list-item">
                    <div>
                        <p><strong>{{ comment.email }}</strong></p>
                        <small>{{ comment.body }}</small>
                    </div>
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
    export default {
        emits: ['load'],
        props: ['label', 'comments']

    }
</script>

<style scoped>

</style>
