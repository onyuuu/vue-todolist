<template>
    <div class="container">
        <h2>TODO-List</h2>
        <button class="btn btnB" @click="moveToCreatePage">create todo</button>
        <input type="text" placeholder="검색" class="form-control" v-model="searchText">
        <!-- <TodoSimpleForm @add-todo="addTodo" /> -->
        <div v-if="!todos.length">
            찾는 할 일이 없습니다.
        </div>
        <TodoList :todos="todos" @delete-todo="deleteTodo" @toggle-todo="toggleTodo" />
        <br>
        <nav class="nav">
            <ul class="pagination">
                <li v-if="currentPage !==1" class="page-item">
                    <a href="#" class="page-link" @click="getTodos(currentPage-1)">
                        <i class="fa-solid fa-chevron-left"></i>
                    </a>
                </li>
                <li v-for="page in numberOfPages" :key="page" class="page-item" :class="currentPage===page ? 'active' : ''">
                    <a href="#" @click="getTodos(page)" class="page-link" :class="currentPage===page ? 'active' : ''">{{ page }}</a>
                </li>
                <li v-if="numberOfPages!== currentPage" class="page-item">
                    <a href="#" class="page-link" @click="getTodos(currentPage+1)">
                        <i class="fa-solid fa-chevron-right"></i>
                    </a>
                </li>
            </ul>
        </nav>
    </div>
</template>

<script>
import { computed, ref, watch } from 'vue';
/* import TodoSimpleForm from '@/components/TodoSimpleForm.vue'; */  // @는 src기준
import TodoList from '@/components/TodoList.vue';
import axios from 'axios';
import { useRouter } from 'vue-router';


export default {
    components: {
        /* TodoSimpleForm, */
        TodoList,
    },
    setup(){
        const router = useRouter();
        const todos = ref([]);
        const searchText = ref('');
        const error = ref('');
        const limit = 5;
        const numberOfTodos = ref(0);  //전체페이지
        const currentPage = ref(1);
        const numberOfPages = computed(() => {   //한 페이지
            return Math.ceil(numberOfTodos.value/limit)
        });

        const getTodos = async (page = currentPage.value) => {
            currentPage.value = page;
            try{
                const res = await axios.get(`http://localhost:3000/todos?_sort=id&_order=desc&subject_like=${searchText.value}&_page=${page}&_limit=${limit}`);  //ref는 value를 써줘야함
                //console.log(res.headers);
                numberOfTodos.value = res.headers['x-total-count'];
                todos.value = res.data;
            }catch(err){
                console.log(err);
                error.value='찾는 할 일이 없습니다.'
            }
        };
        getTodos();

        /* const todoStyle = {
            color: 'gray',
            textDecoration: 'line-through'
        } */

        const moveToCreatePage = () => {
            router.push({
                name: 'TodoCreate',
            });
        }

        const addTodo = async (todo) => {
            error.value = '';
            try{  //성공적으로 값이 들어오면
                await axios.post('http://localhost:3000/todos', {
                    subject: todo.subject,
                    completed: todo.completed,
                });
                getTodos(1);
            }catch (err){
                console.log(err);
                error.value='잘못된 입력입니다.'
            }
        }

        watch(searchText, (/* current, prev */) => {
            //console.log(current, prev)
            getTodos(1);
        })

        /* const addTodo = (todo) => {
            //todos.value.push(todo);
            error.value = '';
            axios.post('http://localhost:3000/todos', {
                subject: todo.subject,
                completed: todo.completed,
            }).then((res) => {
                console.log(res);
                todos.value.push(res.data);
            })
            .catch((err) => {
                console.log(err);
                error.value='잘못된 입력입니다.'
            });
        } */

        const deleteTodo = async (idx) => {  //내가 선택한 게 뭔지 모르니까 idx 이름을 준 것
            error.value='';
            const id = todos.value[idx].id;
            try{
                await axios.delete('http://localhost:3000/todos/' + id);
                /* todos.value.splice(idx, 1); */
                getTodos(1);
            }catch(err){
                console.log(err);
                error.value='찾는 할 일이 없습니다.'
            }
        }

        const toggleTodo = async (idx, checked) => {
            //console.log(idx)

            error.value='';
            const id = todos.value[idx].id;

            try{
                await axios.patch('http://localhost:3000/todos/' + id, {  //데이터 수정
                    completed: checked
                });
                todos.value[idx].completed = checked;  //보이는 화면 수정
            }catch(err){
                console.log(err);
                error.value='찾는 할 일이 없습니다.'
            }
        }

        /* const filteredTodos = computed(() => {  //검색
            if(searchText.value){
                return todos.value.filter(todo => {
                    return todo.subject.includes(searchText.value);
                });
            }
            return todos.value;
        }); */

        return {
            todos,
            /* todoStyle, */
            deleteTodo,
            addTodo,
            toggleTodo,
            searchText,
            /* filteredTodos, */
            getTodos,
            numberOfPages,
            currentPage,
            moveToCreatePage,
        }
    }
}
</script>

<style>
    *{margin: 0; padding: 0; box-sizing: border-box;}
    .container{width: 100%; max-width: 1024px; margin: 0 auto; padding: 0 20px;}
    h2{text-align: center; color: #7eaace; margin-bottom: 50px; margin-top: 50px;}
    .d-flex{display: flex;}
    .flex-grow-1{flex-grow: 1;}
    .flex-grow-1 input{width: 98%; padding: 10px 20px; outline: none; border: 1px solid #ddd; border-radius: 20px;}
    .btn{padding: 11px 30px; border: none; background: #7eaace; color: #fff; border-radius: 20px;}
    form{margin-bottom: 50px;}
    .card{margin: 10px 0;}
    .card-body{border: 1px solid #ddd; padding: 10px 20px; display: flex; justify-content: space-between; align-items: center; border-radius: 10px;}
    .form-check-input{margin-right: 15px;}
    .todoStyle{text-decoration: line-through; color: gray;}
    .btnRed{padding: 5px 20px; background: #b9b9b9; color: #fff; outline: none; border: none; border-radius: 20px;}
    .form-control{outline: none; border: 1px solid #ddd; width: 100%; margin-bottom: 10px; padding: 10px 20px; border-radius: 20px;}
    .pagination{list-style: none; display: flex; justify-content: center;}
    .page-item{padding: 10px; margin: 0 2px;}
    .page-link{text-decoration: none; color: #666;}
    .active{font-weight: bold; color: #5e84a3;}
    .btnB{margin-bottom: 10px;}
</style>