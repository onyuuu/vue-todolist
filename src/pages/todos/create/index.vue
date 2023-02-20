<template>
    <div>
        <div class="container">
            <h1>Create Todo</h1>
            <TodoForm />
        </div>
    </div>
</template>

<script>
import TodoForm from '@/components/TodoForm.vue';

export default{
    components: {
        TodoForm,
    },
}
</script>

<style>
</style>