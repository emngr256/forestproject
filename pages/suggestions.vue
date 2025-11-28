<template>
  <div class="min-h-screen bg-gradient-to-br from-green-50 to-emerald-100 py-8">
    <div class="container mx-auto px-4 max-w-4xl">
      
      <!-- Заголовок и панель пользователя -->
      <div class="text-center mb-12">
        <h1 class="text-4xl font-bold text-gray-800 mb-4">Предложения по защите лесов</h1>
        
        <!-- Панель пользователя -->
        <div class="flex justify-center items-center gap-4 mb-6">
          <div v-if="user" class="flex items-center gap-3 bg-white rounded-full px-4 py-2 shadow-lg">
            <div class="w-8 h-8 bg-green-500 rounded-full flex items-center justify-center text-white font-bold">
              {{ user.name.charAt(0).toUpperCase() }}
            </div>
            <span class="text-gray-700 font-medium">{{ user.name }}</span>
            <button 
              @click="logout"
              class="text-sm text-gray-500 hover:text-red-500 transition-colors"
            >
              Выйти
            </button>
          </div>
          
          <div v-else>
            <button 
              @click="showLoginModal = true"
              class="bg-green-500 hover:bg-green-600 text-white font-semibold py-2 px-6 rounded-full transition-all duration-300"
            >
              Войти для комментирования
            </button>
          </div>
        </div>

        <!-- Кнопка возврата -->
        <div>
          <a 
            href="/" 
            class="inline-flex items-center gap-2 bg-green-600 hover:bg-green-700 text-white font-semibold py-2 px-6 rounded-lg transition-all duration-300"
          >
            ← Назад к главной странице
          </a>
        </div>
      </div>

      <!-- Модальное окно входа -->
      <div v-if="showLoginModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 p-4">
        <div class="bg-white rounded-2xl p-6 w-full max-w-md">
          <h2 class="text-xl font-semibold mb-4">Вход для комментирования</h2>
          
          <form @submit.prevent="login" class="space-y-4">
            <div>
              <label class="text-gray-700 text-sm font-medium mb-2 block">Ваше имя *</label>
              <input 
                v-model="loginForm.name"
                type="text" 
                placeholder="Как к вам обращаться?"
                class="w-full px-4 py-3 rounded-lg border border-gray-300 focus:border-green-400 focus:ring-2 focus:ring-green-400/20"
                required
              >
            </div>
            
            <div>
              <label class="text-gray-700 text-sm font-medium mb-2 block">Email *</label>
              <input 
                v-model="loginForm.email"
                type="email" 
                placeholder="your@email.com"
                class="w-full px-4 py-3 rounded-lg border border-gray-300 focus:border-green-400 focus:ring-2 focus:ring-green-400/20"
                required
              >
            </div>

            <div class="flex gap-3 pt-2">
              <button 
                type="button"
                @click="showLoginModal = false"
                class="flex-1 px-4 py-3 border border-gray-300 text-gray-700 rounded-lg hover:bg-gray-50 transition-colors"
              >
                Отмена
              </button>
              <button 
                type="submit"
                :disabled="isLoggingIn"
                class="flex-1 bg-green-500 hover:bg-green-600 text-white font-semibold py-3 px-6 rounded-lg transition-colors disabled:opacity-50"
              >
                {{ isLoggingIn ? 'Вход...' : 'Войти' }}
              </button>
            </div>
          </form>
        </div>
      </div>

      <!-- Форма для создания предложения -->
      <div class="bg-white rounded-2xl shadow-lg p-6 mb-8">
        <h2 class="text-xl font-semibold mb-4">💡 Добавить новое предложение</h2>
        <form @submit.prevent="submitSuggestion" class="space-y-4">
          <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
            <div>
              <label class="text-gray-700 text-sm font-medium mb-2 block">Ваше имя *</label>
              <input 
                v-model="newSuggestion.name"
                type="text" 
                placeholder="Как к вам обращаться?"
                class="w-full px-4 py-3 rounded-lg border border-gray-300 focus:border-green-400 focus:ring-2 focus:ring-green-400/20"
                required
              >
            </div>
            
            <div>
              <label class="text-gray-700 text-sm font-medium mb-2 block">Категория *</label>
              <select 
                v-model="newSuggestion.category"
                class="w-full px-4 py-3 rounded-lg border border-gray-300 focus:border-green-400 focus:ring-2 focus:ring-green-400/20"
                required
              >
                <option value="">Выберите категорию</option>
                <option value="planting">🌱 Посадка деревьев</option>
                <option value="protection">🛡️ Защита леса</option>
                <option value="cleaning">🧹 Уборка территории</option>
                <option value="fire_safety">🔥 Пожарная безопасность</option>
                <option value="infrastructure">🏗️ Инфраструктура</option>
                <option value="education">📚 Просвещение</option>
                <option value="other">💡 Другое</option>
              </select>
            </div>
          </div>

          <div>
            <label class="text-gray-700 text-sm font-medium mb-2 block">Ваше предложение *</label>
            <textarea 
              v-model="newSuggestion.message"
              placeholder="Опишите вашу идею по улучшению лесов Петропавловска..."
              rows="4"
              class="w-full px-4 py-3 rounded-lg border border-gray-300 focus:border-green-400 focus:ring-2 focus:ring-green-400/20 resize-none"
              required
            ></textarea>
          </div>

          <button 
            type="submit"
            :disabled="isSubmitting"
            class="w-full bg-green-600 hover:bg-green-700 text-white font-semibold py-3 px-6 rounded-lg transition-all duration-300 transform hover:scale-105 disabled:opacity-50 disabled:cursor-not-allowed"
          >
            <span v-if="isSubmitting">⏳ Отправка предложения...</span>
            <span v-else>💡 Опубликовать предложение</span>
          </button>
        </form>

        <!-- Успешное сообщение -->
        <div v-if="showSuccess" class="mt-4 p-3 bg-green-500/90 text-white rounded-lg text-center">
          ✅ Спасибо! Ваше предложение опубликовано.
        </div>

        <!-- Сообщение об ошибке -->
        <div v-if="showError" class="mt-4 p-3 bg-red-500/90 text-white rounded-lg text-center">
          ❌ Ошибка при отправке. Попробуйте еще раз.
        </div>
      </div>

      <!-- Панель админа -->
      <div class="bg-white rounded-2xl shadow-lg p-6 mb-8">
        <div class="flex justify-between items-center">
          <div>
            <h2 class="text-xl font-semibold">Панель управления предложениями</h2>
            <p class="text-gray-600">Гости могут ставить лайки, админ - управлять предложениями</p>
          </div>
          
          <div v-if="!isAdmin" class="flex gap-4">
            <button 
              @click="showAdminLogin = !showAdminLogin"
              class="px-4 py-2 bg-green-500 hover:bg-green-600 text-white rounded-lg transition-colors"
            >
              {{ showAdminLogin ? 'Скрыть вход' : 'Вход для админа' }}
            </button>
          </div>
          
          <div v-else class="flex gap-4 items-center">
            <span class="text-green-600 font-semibold">👑 Администратор лесов</span>
            <button 
              @click="adminLogout"
              class="px-4 py-2 bg-red-500 hover:bg-red-600 text-white rounded-lg transition-colors"
            >
              Выйти
            </button>
          </div>
        </div>

        <!-- Форма входа админа -->
        <div v-if="showAdminLogin && !isAdmin" class="mt-4 p-4 bg-gray-50 rounded-lg">
          <h3 class="font-semibold mb-3">Вход для администратора</h3>
          <form @submit.prevent="adminLogin" class="space-y-3">
            <input
              v-model="adminLoginForm.login"
              type="text"
              placeholder="Логин"
              class="w-full p-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-green-500"
              required
            />
            <input
              v-model="adminLoginForm.password"
              type="password"
              placeholder="Пароль"
              class="w-full p-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-green-500"
              required
            />
            <button
              type="submit"
              class="w-full bg-green-500 hover:bg-green-600 text-white font-semibold py-2 px-4 rounded-lg transition-all duration-300"
            >
              Войти
            </button>
          </form>
        </div>
      </div>

      <!-- Статистика (видна всем) -->
      <div class="bg-white rounded-2xl shadow-lg p-6 mb-8">
        <h2 class="text-xl font-semibold mb-4">🌳 Статистика сообщества</h2>
        <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
          <div class="text-center p-4 bg-green-50 rounded-lg">
            <div class="text-3xl font-bold text-green-600">{{ suggestions.length }}</div>
            <div class="text-sm text-gray-600">Всего предложений</div>
          </div>
          <div class="text-center p-4 bg-blue-50 rounded-lg">
            <div class="text-3xl font-bold text-blue-600">{{ totalLikes }}</div>
            <div class="text-sm text-gray-600">Всего лайков</div>
          </div>
          <div class="text-center p-4 bg-purple-50 rounded-lg">
            <div class="text-3xl font-bold text-purple-600">{{ uniqueAuthors }}</div>
            <div class="text-sm text-gray-600">Участников</div>
          </div>
          <div class="text-center p-4 bg-orange-50 rounded-lg">
            <div class="text-3xl font-bold text-orange-600">{{ todaySuggestions }}</div>
            <div class="text-sm text-gray-600">Сегодня</div>
          </div>
        </div>
      </div>

      <!-- Категории предложений -->
      <div class="bg-white rounded-2xl shadow-lg p-6 mb-8">
        <h2 class="text-xl font-semibold mb-4">📋 Категории предложений</h2>
        <div class="grid grid-cols-2 md:grid-cols-4 gap-3">
          <button 
            v-for="category in categories" 
            :key="category.id"
            @click="filterByCategory(category.id)"
            class="p-3 rounded-lg border-2 text-center transition-all duration-300"
            :class="activeCategory === category.id 
              ? 'border-green-500 bg-green-50 text-green-700' 
              : 'border-gray-200 bg-gray-50 text-gray-600 hover:border-green-300'"
          >
            <div class="text-2xl mb-1">{{ category.icon }}</div>
            <div class="text-sm font-medium">{{ category.name }}</div>
            <div class="text-xs text-gray-500">{{ category.count }} предлож.</div>
          </button>
        </div>
      </div>

      <!-- Список предложений -->
      <div class="space-y-6">
        <div 
          v-for="suggestion in filteredSuggestions" 
          :key="suggestion.id"
          class="bg-white rounded-2xl shadow-lg p-6 relative hover:shadow-xl transition-all duration-300 border-l-4"
          :class="[
            getSuggestionBorder(suggestion.status),
            suggestion.is_pinned ? 'ring-2 ring-yellow-400 bg-yellow-50' : '',
            suggestion.priority === 'high' ? 'bg-red-50' : ''
          ]"
        >
          <!-- Верхняя панель с метками -->
          <div class="flex flex-wrap gap-2 mb-3">
            <!-- Закреплено -->
            <span v-if="suggestion.is_pinned" class="px-2 py-1 bg-yellow-100 text-yellow-800 rounded-full text-xs font-medium">
              📌 Закреплено
            </span>
            
            <!-- Приоритет -->
            <span v-if="suggestion.priority === 'high'" class="px-2 py-1 bg-red-100 text-red-800 rounded-full text-xs font-medium">
              🔥 Высокий приоритет
            </span>
            
            <!-- Категория -->
            <span v-if="suggestion.category" class="px-2 py-1 bg-blue-100 text-blue-800 rounded-full text-xs font-medium">
              {{ getCategoryIcon(suggestion.category) }} {{ getCategoryName(suggestion.category) }}
            </span>
            
            <!-- Статус -->
            <span class="px-2 py-1 rounded-full text-xs font-medium ml-auto" 
                  :class="getStatusBadge(suggestion.status)">
              {{ getStatusText(suggestion.status) }}
            </span>
          </div>

          <!-- Контент предложения -->
          <div class="mb-4">
            <h3 class="text-lg font-semibold text-gray-800 mb-1">{{ suggestion.name }}</h3>
            <p class="text-sm text-gray-500 mb-3">{{ formatDate(suggestion.date) }}</p>
            <p class="text-gray-700 leading-relaxed whitespace-pre-line">{{ suggestion.message }}</p>
          </div>

          <!-- Комментарии -->
          <div v-if="suggestion.comments && suggestion.comments.length > 0" class="mb-4">
            <div class="border-t pt-3">
              <h4 class="text-sm font-semibold text-gray-700 mb-2">💬 Комментарии ({{ suggestion.comments.length }}):</h4>
              <div v-for="comment in suggestion.comments" :key="comment.id" class="bg-gray-50 rounded-lg p-3 mb-2">
                <div class="flex justify-between items-start mb-1">
                  <span class="font-medium text-sm">{{ comment.author }}</span>
                  <span class="text-xs text-gray-500">{{ formatDate(comment.date) }}</span>
                </div>
                <p class="text-sm text-gray-700">{{ comment.text }}</p>
              </div>
            </div>
          </div>

          <!-- Форма комментария (только для авторизованных) -->
          <div v-if="user" class="mb-4">
            <div class="flex gap-2">
              <input
                v-model="commentTexts[suggestion.id]"
                type="text"
                :placeholder="`Написать комментарий как ${user.name}...`"
                class="flex-1 px-3 py-2 border border-gray-300 rounded-lg text-sm focus:outline-none focus:ring-2 focus:ring-green-500"
                @keypress.enter="addComment(suggestion.id)"
              />
              <button
                @click="addComment(suggestion.id)"
                class="px-3 py-2 bg-green-500 hover:bg-green-600 text-white rounded-lg text-sm transition-colors"
                :disabled="!commentTexts[suggestion.id]?.trim()"
                :class="!commentTexts[suggestion.id]?.trim() ? 'opacity-50 cursor-not-allowed' : ''"
              >
                💬 Отправить
              </button>
            </div>
          </div>

          <!-- Сообщение для неавторизованных -->
          <div v-else class="mb-4 p-4 bg-yellow-50 border border-yellow-200 rounded-lg text-center">
            <p class="text-yellow-800 text-sm">
              🔒 Чтобы оставить комментарий, 
              <button @click="showLoginModal = true" class="text-green-600 hover:text-green-700 font-medium underline">
                войдите в систему
              </button>
            </p>
          </div>

          <!-- Нижняя панель действий -->
          <div class="flex justify-between items-center pt-3 border-t">
            <!-- Лайки -->
            <button 
              @click="likeSuggestion(suggestion.id)"
              :disabled="hasLiked(suggestion.id)"
              class="flex items-center gap-2 px-4 py-2 rounded-lg transition-colors"
              :class="hasLiked(suggestion.id) 
                ? 'bg-red-100 text-red-700 cursor-not-allowed' 
                : 'bg-gray-100 hover:bg-gray-200 text-gray-700'"
            >
              <span>❤️</span>
              <span>{{ suggestion.likes || 0 }}</span>
              <span v-if="hasLiked(suggestion.id)">✓</span>
            </button>

            <!-- Кнопки админа -->
            <div v-if="isAdmin" class="flex gap-2 flex-wrap">
              <button 
                @click="togglePin(suggestion.id)"
                class="px-3 py-1 bg-yellow-100 text-yellow-700 rounded-lg text-sm hover:bg-yellow-200 transition-colors"
              >
                {{ suggestion.is_pinned ? '📌 Открепить' : '📌 Закрепить' }}
              </button>
              <button 
                @click="setPriority(suggestion.id)"
                class="px-3 py-1 bg-red-100 text-red-700 rounded-lg text-sm hover:bg-red-200 transition-colors"
              >
                {{ suggestion.priority === 'high' ? '🚫 Снять приоритет' : '🔥 Приоритет' }}
              </button>
              <button 
                @click="changeStatus(suggestion.id, 'reviewed')"
                class="px-3 py-1 bg-blue-100 text-blue-700 rounded-lg text-sm hover:bg-blue-200 transition-colors"
              >
                📋 Рассмотрено
              </button>
              <button 
                @click="changeStatus(suggestion.id, 'planned')"
                class="px-3 py-1 bg-green-100 text-green-700 rounded-lg text-sm hover:bg-green-200 transition-colors"
              >
                📅 В планах
              </button>
              <button 
                @click="deleteSuggestion(suggestion.id)"
                class="px-3 py-1 bg-red-100 text-red-700 rounded-lg text-sm hover:bg-red-200 transition-colors"
              >
                🗑️ Удалить
              </button>
            </div>
          </div>
        </div>

        <!-- Если предложений нет -->
        <div v-if="filteredSuggestions.length === 0" class="text-center py-12">
          <div class="text-6xl mb-4">🌲</div>
          <h3 class="text-xl font-semibold text-gray-600 mb-2">Пока нет предложений в этой категории</h3>
          <p class="text-gray-500">Будьте первым, кто предложит идею по защите лесов!</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
// Типы для TypeScript
interface Comment {
  id: number
  author: string
  text: string
  date: string
}

interface Suggestion {
  id: number
  name: string
  message: string
  category: string
  date: string
  likes: number
  status: string
  is_pinned: boolean
  priority?: string
  comments?: Comment[]
}

interface Category {
  id: string
  name: string
  icon: string
  count: number
}

interface User {
  id: number
  email: string
  name: string
}

// Реактивные данные
const suggestions = ref<Suggestion[]>([])
const user = ref<User | null>(null)
const authToken = ref<string>('')
const isAdmin = ref(false)
const showLoginModal = ref(false)
const showAdminLogin = ref(false)
const isLoggingIn = ref(false)

const activeCategory = ref<string>('all')
const commentTexts = ref<Record<number, string>>({})

// Формы
const loginForm = ref({
  name: '',
  email: ''
})

const adminLoginForm = ref({
  login: '',
  password: ''
})

const newSuggestion = ref({
  name: '',
  message: '',
  category: ''
})

const isSubmitting = ref(false)
const showSuccess = ref(false)
const showError = ref(false)

// Храним лайки пользователя в localStorage
const userLikes = ref<number[]>([])

const API_BASE = 'http://localhost:3001/api'

// Категории предложений
const categories = ref<Category[]>([
  { id: 'all', name: 'Все', icon: '🌳', count: 0 },
  { id: 'planting', name: 'Посадка', icon: '🌱', count: 0 },
  { id: 'protection', name: 'Защита', icon: '🛡️', count: 0 },
  { id: 'cleaning', name: 'Уборка', icon: '🧹', count: 0 },
  { id: 'fire_safety', name: 'Пожарная безопасность', icon: '🔥', count: 0 },
  { id: 'infrastructure', name: 'Инфраструктура', icon: '🏗️', count: 0 },
  { id: 'education', name: 'Просвещение', icon: '📚', count: 0 },
  { id: 'other', name: 'Другое', icon: '💡', count: 0 }
])

// Загружаем данные при загрузке страницы
onMounted(() => {
  checkAuth()
  loadSuggestions()
  loadUserLikes()
})

// Проверка аутентификации
const checkAuth = async () => {
  try {
    if (authToken.value) {
      const data = await $fetch(`${API_BASE}/auth/me`, {
        headers: {
          'Authorization': `Bearer ${authToken.value}`
        }
      })
      user.value = data.user
    }
  } catch (error) {
    user.value = null
    authToken.value = ''
  }
}

// Вход через email
const login = async () => {
  if (isLoggingIn.value) return
  
  isLoggingIn.value = true
  try {
    const data = await $fetch(`${API_BASE}/auth/login`, {
      method: 'POST',
      body: loginForm.value
    })
    
    user.value = data.user
    authToken.value = data.token
    showLoginModal.value = false
    loginForm.value = { name: '', email: '' }
  } catch (error: any) {
    alert('Ошибка входа: ' + (error.data?.error || error.message))
  } finally {
    isLoggingIn.value = false
  }
}

// Выход
const logout = async () => {
  try {
    await $fetch(`${API_BASE}/auth/logout`, { 
      method: 'POST',
      body: { token: authToken.value }
    })
    user.value = null
    authToken.value = ''
  } catch (error) {
    console.error('Logout error:', error)
    user.value = null
    authToken.value = ''
  }
}

// Вход админа
const adminLogin = async () => {
  if (adminLoginForm.value.login === 'admin' && adminLoginForm.value.password === 'admin') {
    isAdmin.value = true
    showAdminLogin.value = false
    adminLoginForm.value = { login: '', password: '' }
  } else {
    alert('Неверный логин или пароль')
  }
}

// Выход админа
const adminLogout = () => {
  isAdmin.value = false
}

// Функция отправки нового предложения
const submitSuggestion = async () => {
  if (isSubmitting.value) return
  
  isSubmitting.value = true
  showError.value = false

  try {
    const response = await $fetch(`${API_BASE}/suggestions`, {
      method: 'POST',
      body: newSuggestion.value
    })

    showSuccess.value = true
    newSuggestion.value = { name: '', message: '', category: '' }
    await loadSuggestions()
    
    setTimeout(() => {
      showSuccess.value = false
    }, 5000)
  } catch (error: any) {
    console.error('❌ Ошибка при отправке предложения:', error)
    showError.value = true
    setTimeout(() => {
      showError.value = false
    }, 3000)
  } finally {
    isSubmitting.value = false
  }
}

// Загрузка лайков пользователя
const loadUserLikes = () => {
  if (process.client) {
    const saved = localStorage.getItem('userLikes')
    userLikes.value = saved ? JSON.parse(saved) : []
  }
}

// Сохранение лайков пользователя
const saveUserLikes = () => {
  if (process.client) {
    localStorage.setItem('userLikes', JSON.stringify(userLikes.value))
  }
}

// Проверка, лайкал ли пользователь это предложение
const hasLiked = (suggestionId: number) => {
  return userLikes.value.includes(suggestionId)
}

// Загрузка предложений из бекенда
const loadSuggestions = async () => {
  try {
    const data = await $fetch<Suggestion[]>(`${API_BASE}/suggestions`)
    suggestions.value = data
    updateCategoryCounts()
  } catch (error) {
    console.error('Error loading suggestions:', error)
  }
}

// Обновление счетчиков категорий
const updateCategoryCounts = () => {
  categories.value.forEach(category => {
    if (category.id === 'all') {
      category.count = suggestions.value.length
    } else {
      category.count = suggestions.value.filter(s => s.category === category.id).length
    }
  })
}

// Фильтрация по категориям
const filterByCategory = (categoryId: string) => {
  activeCategory.value = categoryId
}

// Отфильтрованные предложения
const filteredSuggestions = computed(() => {
  let filtered = activeCategory.value === 'all' 
    ? suggestions.value 
    : suggestions.value.filter(s => s.category === activeCategory.value)
  
  return filtered.sort((a, b) => {
    if (a.is_pinned && !b.is_pinned) return -1
    if (!a.is_pinned && b.is_pinned) return 1
    if (a.priority === 'high' && b.priority !== 'high') return -1
    if (a.priority !== 'high' && b.priority === 'high') return 1
    return new Date(b.date).getTime() - new Date(a.date).getTime()
  })
})

// Лайк предложения
const likeSuggestion = async (id: number) => {
  if (hasLiked(id)) return
  
  try {
    await $fetch(`${API_BASE}/suggestions/${id}/like`, {
      method: 'POST'
    })
    
    userLikes.value.push(id)
    saveUserLikes()
    await loadSuggestions()
  } catch (error) {
    console.error('Error liking suggestion:', error)
  }
}

// Закрепить/открепить предложение
const togglePin = async (id: number) => {
  try {
    const suggestion = suggestions.value.find(s => s.id === id)
    const newPinStatus = !suggestion?.is_pinned
    
    console.log('📌 Закрепление:', { id, newPinStatus })
    
    const response = await $fetch(`${API_BASE}/suggestions/${id}/pin`, {
      method: 'PUT',
      body: { isPinned: newPinStatus }
    })

    console.log('✅ Закрепление успешно:', response)
    await loadSuggestions()
  } catch (error: any) {
    console.error('❌ Ошибка закрепления:', error)
    alert('Ошибка при закреплении: ' + (error.data?.error || error.message))
  }
}

// Добавить комментарий
const addComment = async (suggestionId: number) => {
  if (!user.value) {
    showLoginModal.value = true
    return
  }

  const text = commentTexts.value[suggestionId]?.trim()
  if (!text) {
    alert('Введите текст комментария')
    return
  }

  try {
    await $fetch(`${API_BASE}/suggestions/${suggestionId}/comments`, {
      method: 'POST',
      body: { text },
      headers: {
        'Authorization': `Bearer ${authToken.value}`
      }
    })

    commentTexts.value[suggestionId] = ''
    await loadSuggestions()
  } catch (error: any) {
    console.error('❌ Ошибка добавления комментария:', error)
    alert('Ошибка при добавлении комментария: ' + (error.data?.error || error.message))
  }
}

// Установить приоритет
const setPriority = async (id: number) => {
  try {
    const suggestion = suggestions.value.find(s => s.id === id)
    const newPriority = suggestion?.priority === 'high' ? null : 'high'
    
    await $fetch(`${API_BASE}/suggestions/${id}/priority`, {
      method: 'PUT',
      body: { priority: newPriority }
    })

    await loadSuggestions()
  } catch (error: any) {
    console.error('Error setting priority:', error)
    alert('Ошибка при установке приоритета: ' + (error.data?.error || error.message))
  }
}

// Изменение статуса
const changeStatus = async (id: number, newStatus: string) => {
  try {
    await $fetch(`${API_BASE}/suggestions/${id}/status`, {
      method: 'PUT',
      body: { status: newStatus }
    })

    await loadSuggestions()
  } catch (error: any) {
    console.error('Error changing status:', error)
    alert('Ошибка при изменении статуса: ' + (error.data?.error || error.message))
  }
}

// Удаление предложения
const deleteSuggestion = async (id: number) => {
  if (!confirm('Удалить это предложение?')) return

  try {
    await $fetch(`${API_BASE}/suggestions/${id}`, {
      method: 'DELETE'
    })

    await loadSuggestions()
  } catch (error: any) {
    console.error('Error deleting suggestion:', error)
    alert('Ошибка при удалении: ' + (error.data?.error || error.message))
  }
}

// Статистика
const totalLikes = computed(() => {
  return suggestions.value.reduce((sum, s) => sum + (s.likes || 0), 0)
})

const uniqueAuthors = computed(() => {
  const authors = new Set(suggestions.value.map(s => s.name))
  return authors.size
})

const todaySuggestions = computed(() => {
  const today = new Date().toDateString()
  return suggestions.value.filter(s => {
    const suggestionDate = new Date(s.date).toDateString()
    return suggestionDate === today
  }).length
})

// Функции для статусов
const getSuggestionBorder = (status: string) => {
  switch (status) {
    case 'new': return 'border-l-blue-500'
    case 'reviewed': return 'border-l-green-500'
    case 'planned': return 'border-l-purple-500'
    default: return 'border-l-gray-500'
  }
}

const getStatusBadge = (status: string) => {
  switch (status) {
    case 'new': return 'bg-blue-100 text-blue-800'
    case 'reviewed': return 'bg-green-100 text-green-800'
    case 'planned': return 'bg-purple-100 text-purple-800'
    default: return 'bg-gray-100 text-gray-800'
  }
}

const getStatusText = (status: string) => {
  switch (status) {
    case 'new': return 'Новое'
    case 'reviewed': return 'Рассмотрено'
    case 'planned': return 'В планах'
    default: return status
  }
}

// Функции для категорий
const getCategoryIcon = (categoryId: string) => {
  const category = categories.value.find(c => c.id === categoryId)
  return category ? category.icon : '💡'
}

const getCategoryName = (categoryId: string) => {
  const category = categories.value.find(c => c.id === categoryId)
  return category ? category.name : 'Другое'
}

// Форматирование даты
const formatDate = (dateString: string) => {
  return new Date(dateString).toLocaleString('ru-RU', {
    day: 'numeric',
    month: 'long',
    year: 'numeric',
    hour: '2-digit',
    minute: '2-digit'
  })
}
</script>