<script setup>
import { ref, onMounted, computed, watch } from 'vue'
const todos = ref([])
const name = ref('')

const input_content = ref('')

const todos_asc = computed(() => todos.value.sort((a,b) =>{
    return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

const addTodo = () => {
	if (input_content.value.trim() === '' ) {
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
		
		<section class="greeting">
			<h2 class="title">
				Que tal?, <input type="text" id="name" placeholder="______" v-model="name">
			</h2>
		</section>

		<section class="create-todo">

			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4>To do list:</h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="Ingresar tarea"
					v-model="input_content" />

				<input type="submit" value="Add todo" />
			</form>
		</section>

		<section class="todo-list">
			<h3>TO DO LIST</h3>
			<div class="list" id="todo-list">

				<div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'business' 
								? 'business' 
								: 'personal'
						}`"></span>
					</label>

					<div class="todo-content">
						<input type="text" v-model="todo.content" />
					</div>

					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Delete</button>
					</div>
				</div>

			</div>
		</section>

	</main>
</template>