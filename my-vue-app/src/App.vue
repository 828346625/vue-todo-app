<script setup>
import { ref, computed, onMounted } from 'vue'

const todoInput = ref('')
const todos = ref([])

// 从 localStorage 加载数据
onMounted(() => {
  const saved = localStorage.getItem('vue-todos')
  if (saved) {
    todos.value = JSON.parse(saved)
  }
})

// 添加待办事项
const addTodo = () => {
  if (!todoInput.value.trim()) {
    alert('请输入待办事项！')
    return
  }
  
  todos.value.push({
    id: Date.now(),
    text: todoInput.value.trim(),
    completed: false,
    createdAt: new Date().toLocaleString()
  })
  
  todoInput.value = ''
  saveToLocalStorage()
}

// 删除待办事项
const deleteTodo = (id) => {
  if (confirm('确定要删除吗？')) {
    todos.value = todos.value.filter(todo => todo.id !== id)
    saveToLocalStorage()
  }
}

// 切换完成状态
const toggleComplete = (id) => {
  const todo = todos.value.find(todo => todo.id === id)
  if (todo) {
    todo.completed = !todo.completed
    saveToLocalStorage()
  }
}

// 保存到 localStorage
const saveToLocalStorage = () => {
  localStorage.setItem('vue-todos', JSON.stringify(todos.value))
}

// 计算已完成数量
const completedCount = computed(() => {
  return todos.value.filter(todo => todo.completed).length
})

// 清空所有
const clearAll = () => {
  if (todos.value.length === 0) return
  if (confirm('确定要清空所有待办事项吗？')) {
    todos.value = []
    localStorage.removeItem('vue-todos')
  }
}
</script>

<template>
  <div class="todo-container">
    <header class="header">
      <h1>📝 Todo App</h1>
      <p class="subtitle">简单实用的待办事项管理</p>
    </header>

    <!-- 添加区域 -->
    <div class="input-section">
      <input
        v-model="todoInput"
        type="text"
        placeholder="请输入待办事项..."
        class="todo-input"
        @keyup.enter="addTodo"
      />
      <button @click="addTodo" class="add-btn">
        <span class="icon">+</span> 添加
      </button>
    </div>

    <!-- 统计信息 -->
    <div class="stats">
      <div class="stat-item">
        <span class="stat-label">总计</span>
        <span class="stat-value">{{ todos.length }}</span>
      </div>
      <div class="stat-item">
        <span class="stat-label">已完成</span>
        <span class="stat-value completed-stat">{{ completedCount }}</span>
      </div>
      <div class="stat-item">
        <button @click="clearAll" class="clear-btn">
          清空全部
        </button>
      </div>
    </div>

    <!-- 待办事项列表 -->
    <div class="todo-list">
      <div v-if="todos.length === 0" class="empty-state">
        <div class="empty-icon">📋</div>
        <p>还没有待办事项</p>
        <p class="empty-hint">添加你的第一个任务吧！</p>
      </div>

      <div
        v-for="todo in todos"
        :key="todo.id"
        class="todo-item"
        :class="{ completed: todo.completed }"
      >
        <div class="todo-content">
          <label class="checkbox-container">
            <input
              type="checkbox"
              :checked="todo.completed"
              @change="toggleComplete(todo.id)"
            />
            <span class="checkmark"></span>
          </label>
          <div class="todo-text">
            <span class="todo-title" :class="{ completed: todo.completed }">
              {{ todo.text }}
            </span>
            <span class="todo-time">{{ todo.createdAt }}</span>
          </div>
        </div>
        <button @click="deleteTodo(todo.id)" class="delete-btn">
          删除
        </button>
      </div>
    </div>

    <!-- 页脚 -->
    <footer class="footer">
      <p>使用 Vue 3 + Vite 构建 | 数据保存在浏览器本地</p>
    </footer>
  </div>
</template>

<style scoped>
.todo-container {
  width: 100%;
  max-width: 800px;
  margin: 20px;
  background: white;
  border-radius: 20px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.1);
  overflow: hidden;
  padding: 40px;
}

.header {
  text-align: center;
  margin-bottom: 30px;
}

.header h1 {
  font-size: 2.5rem;
  color: #333;
  margin-bottom: 10px;
}

.subtitle {
  color: #666;
  font-size: 1.1rem;
}

.input-section {
  display: flex;
  gap: 10px;
  margin-bottom: 30px;
}

.todo-input {
  flex: 1;
  padding: 15px 20px;
  border: 2px solid #e0e0e0;
  border-radius: 12px;
  font-size: 16px;
  transition: all 0.3s;
}

.todo-input:focus {
  outline: none;
  border-color: #7c3aed;
  box-shadow: 0 0 0 3px rgba(124, 58, 237, 0.1);
}

.add-btn {
  padding: 0 30px;
  background: linear-gradient(135deg, #7c3aed, #4f46e5);
  color: white;
  border: none;
  border-radius: 12px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s;
  display: flex;
  align-items: center;
  gap: 8px;
}

.add-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 20px rgba(124, 58, 237, 0.3);
}

.icon {
  font-size: 20px;
  font-weight: bold;
}

.stats {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: #f8fafc;
  padding: 20px;
  border-radius: 12px;
  margin-bottom: 30px;
}

.stat-item {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.stat-label {
  font-size: 14px;
  color: #64748b;
  margin-bottom: 5px;
}

.stat-value {
  font-size: 24px;
  font-weight: bold;
  color: #334155;
}

.completed-stat {
  color: #10b981;
}

.clear-btn {
  padding: 10px 20px;
  background: #f1f5f9;
  color: #64748b;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s;
}

.clear-btn:hover {
  background: #f8fafc;
  color: #ef4444;
}

.todo-list {
  margin-bottom: 30px;
}

.empty-state {
  text-align: center;
  padding: 60px 20px;
  color: #94a3b8;
}

.empty-icon {
  font-size: 60px;
  margin-bottom: 20px;
  opacity: 0.5;
}

.empty-hint {
  margin-top: 10px;
  font-size: 14px;
  color: #cbd5e1;
}

.todo-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px;
  background: #f8fafc;
  border-radius: 12px;
  margin-bottom: 15px;
  transition: all 0.3s;
}

.todo-item:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.05);
}

.todo-item.completed {
  opacity: 0.7;
  background: #f1f5f9;
}

.todo-content {
  display: flex;
  align-items: center;
  gap: 15px;
  flex: 1;
}

.checkbox-container {
  display: block;
  position: relative;
  cursor: pointer;
}

.checkbox-container input {
  position: absolute;
  opacity: 0;
  cursor: pointer;
  height: 0;
  width: 0;
}

.checkmark {
  position: relative;
  height: 24px;
  width: 24px;
  background-color: white;
  border: 2px solid #cbd5e1;
  border-radius: 6px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.checkbox-container input:checked ~ .checkmark {
  background-color: #10b981;
  border-color: #10b981;
}

.checkmark:after {
  content: "";
  position: absolute;
  display: none;
}

.checkbox-container input:checked ~ .checkmark:after {
  display: block;
  left: 7px;
  top: 3px;
  width: 6px;
  height: 10px;
  border: solid white;
  border-width: 0 2px 2px 0;
  transform: rotate(45deg);
}

.todo-text {
  flex: 1;
}

.todo-title {
  display: block;
  font-size: 16px;
  color: #334155;
  margin-bottom: 5px;
}

.todo-title.completed {
  text-decoration: line-through;
  color: #94a3b8;
}

.todo-time {
  font-size: 12px;
  color: #94a3b8;
}

.delete-btn {
  padding: 8px 16px;
  background: #fee2e2;
  color: #dc2626;
  border: none;
  border-radius: 8px;
  font-size: 14px;
  cursor: pointer;
  transition: all 0.3s;
}

.delete-btn:hover {
  background: #fecaca;
}

.footer {
  text-align: center;
  padding-top: 20px;
  border-top: 1px solid #e2e8f0;
  color: #94a3b8;
  font-size: 14px;
}

@media (max-width: 768px) {
  .todo-container {
    margin: 10px;
    padding: 20px;
  }
  
  .header h1 {
    font-size: 2rem;
  }
  
  .input-section {
    flex-direction: column;
  }
  
  .add-btn {
    justify-content: center;
  }
  
  .stats {
    flex-direction: column;
    gap: 15px;
  }
  
  .todo-item {
    flex-direction: column;
    align-items: stretch;
    gap: 15px;
  }
  
  .delete-btn {
    align-self: flex-end;
  }
}
</style>