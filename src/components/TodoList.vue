<template>
    <div>
        <div v-for="(todo, idx) in todos" :key="todo.id" class="card">
            <div class="card-body" @click="moveToPage(todo.id)">
                <div class="form-check">
                    <input type="checkbox" class="form-check-input" :checked="todo.completed" @change="toggleTodo(idx, $event)" @click.stop>
                    <!-- <label class="form-check-label" :style="todo.completed ? todoStyle : {}" > -->
                    <label class="form-check-label" :class="{todoStyle:todo.completed}">
                        {{ todo.subject }}
                    </label>
                </div>
                <div>
                    <button class="btnRed" @click.stop="deleteTodo(idx)">삭제</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { useRouter } from 'vue-router'
export default {
    props: {
        todos: {
            type: Array,
            required: true,
        }
    },
    emits: ['toggle-todo', 'delete-todo'],
    setup(props, {emit}){   //{emit} 구조분해할당. 아래에 context.emit{} 이라고 안 쓰고 emit만 씀

        const router = useRouter();

        const toggleTodo = (idx, event) => {
            emit('toggle-todo', idx, event.target.checked)  //바뀌는 값을 부모한테 전달해줘야하니까 context 사용
        }
        const deleteTodo = (idx) => {
            emit('delete-todo', idx)
        }

        const moveToPage = (todoId) => {
            //console.log(todoId);
            //router.push('/todos/' + todoId);
            router.push({
                name: 'Todo',
                params: {
                    id: todoId
                }
            })
        }

        return {
            deleteTodo,
            toggleTodo,
            moveToPage,
        }
    }
}
</script>