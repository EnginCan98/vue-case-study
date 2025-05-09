<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')

// eslint-disable-next-line vue/no-side-effects-in-computed-properties
const todos_asc = computed(() => todos.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))


watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

const addTodo = () => {
	if (input_content.value.trim() === '') {
		return
	}

	todos.value.push({
		content: input_content.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})

  input_content.value = ''
}

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

</script>

<template>
	<main class="app">
		<section class="create-todo">
			<h2 class="text-center mt-5">GÖREV LİSTESİ</h2>
      <div class="text-center mt-5">
			<form id="new-todo-form" @submit.prevent="addTodo">	
				<input type="text" name="content" id="content" placeholder="Görev giriniz" v-model="input_content" />
				<input type="submit" value="Kaydet" />
			</form>
    </div>
		</section>

		<section class="todo-list">
      <h3 v-if="!todos_asc.length" class="text-center mt-5">Hiç Görev Yok</h3>
			<h3 v-else class="text-center mt-5">Mevcut Görevler</h3>
			<div class="list" id="todo-list">
        <div class="text-center mt-5"> 
          
				<div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`" :key="todo">
					<div class="todo-content">
						<input type="text" v-model="todo.content" />
            <button type="button" class="btn btn-danger rounded-3" @click="removeTodo(todo)">Sil</button>
					</div>
				</div>
        </div>
			</div>
		</section>
	</main>
</template>


