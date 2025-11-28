<template>
  <div class="min-h-screen bg-gradient-to-br from-green-50 to-emerald-100 py-8">
    <div class="container mx-auto px-4 max-w-6xl">
      
      <!-- Заголовок -->
      <div class="text-center mb-12">
        <h1 class="text-4xl font-bold text-gray-800 mb-4">Отчёты о проблемах в лесах</h1>
        <p class="text-xl text-gray-600 max-w-3xl mx-auto">
          Все сообщения о проблемах в лесах Петропавловска
        </p>
        
        <!-- Кнопка возврата -->
        <div class="mt-6 flex justify-center gap-4">
          <a 
            href="/" 
            class="inline-flex items-center gap-2 bg-green-600 hover:bg-green-700 text-white font-semibold py-2 px-6 rounded-lg transition-all duration-300"
          >
            ← Назад к форме отчёта
          </a>
          
          <!-- Кнопка админ-панели -->
          <button 
            v-if="!isAdmin"
            @click="showAdminLogin = true"
            class="inline-flex items-center gap-2 bg-red-600 hover:bg-red-700 text-white font-semibold py-2 px-6 rounded-lg transition-all duration-300"
          >
            🔐 Админ-панель
          </button>
          
          <!-- Кнопка выхода -->
          <button 
            v-else
            @click="logout"
            class="inline-flex items-center gap-2 bg-gray-600 hover:bg-gray-700 text-white font-semibold py-2 px-6 rounded-lg transition-all duration-300"
          >
            🚪 Выйти
          </button>
        </div>
      </div>

      <!-- Модальное окно авторизации -->
      <div v-if="showAdminLogin" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
        <div class="bg-white rounded-2xl shadow-xl p-8 max-w-md w-full mx-4">
          <h2 class="text-2xl font-bold text-gray-800 mb-6">🔐 Авторизация администратора</h2>
          
          <form @submit.prevent="login" class="space-y-4">
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">Логин</label>
              <input 
                v-model="adminCredentials.login"
                type="text" 
                class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-red-500 focus:border-transparent"
                placeholder="Введите логин"
                required
              >
            </div>
            
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">Пароль</label>
              <input 
                v-model="adminCredentials.password"
                type="password" 
                class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-red-500 focus:border-transparent"
                placeholder="Введите пароль"
                required
              >
            </div>
            
            <div class="flex gap-3 pt-4">
              <button 
                type="submit"
                class="flex-1 bg-red-600 hover:bg-red-700 text-white font-semibold py-3 px-4 rounded-lg transition-all duration-300"
              >
                Войти
              </button>
              <button 
                type="button"
                @click="showAdminLogin = false"
                class="flex-1 bg-gray-500 hover:bg-gray-600 text-white font-semibold py-3 px-4 rounded-lg transition-all duration-300"
              >
                Отмена
              </button>
            </div>
          </form>
        </div>
      </div>

      <!-- Статистика -->
      <div class="bg-white rounded-2xl shadow-lg p-6 mb-8">
        <h2 class="text-xl font-semibold mb-4">📊 Статистика отчётов</h2>
        <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
          <div class="text-center p-4 bg-green-50 rounded-lg">
            <div class="text-3xl font-bold text-green-600">{{ reports.length }}</div>
            <div class="text-sm text-gray-600">Всего отчётов</div>
          </div>
          <div class="text-center p-4 bg-red-50 rounded-lg">
            <div class="text-3xl font-bold text-red-600">{{ criticalReports }}</div>
            <div class="text-sm text-gray-600">Критических</div>
          </div>
          <div class="text-center p-4 bg-orange-50 rounded-lg">
            <div class="text-3xl font-bold text-orange-600">{{ highPriorityReports }}</div>
            <div class="text-sm text-gray-600">Высокий приоритет</div>
          </div>
          <div class="text-center p-4 bg-blue-50 rounded-lg">
            <div class="text-3xl font-bold text-blue-600">{{ todayReports }}</div>
            <div class="text-sm text-gray-600">Сегодня</div>
          </div>
        </div>
        
        <!-- Кнопки массового удаления для админа -->
        <div v-if="isAdmin" class="mt-6 pt-6 border-t border-gray-200">
          <h3 class="text-lg font-semibold text-gray-800 mb-4">⚙️ Действия администратора</h3>
          <div class="flex flex-wrap gap-3">
            <button 
              @click="deleteAllReports"
              class="bg-red-600 hover:bg-red-700 text-white font-semibold py-2 px-4 rounded-lg transition-all duration-300"
            >
              🗑️ Удалить все отчёты
            </button>
            <button 
              @click="deleteCriticalReports"
              class="bg-orange-600 hover:bg-orange-700 text-white font-semibold py-2 px-4 rounded-lg transition-all duration-300"
            >
              🔥 Удалить критические
            </button>
            <button 
              @click="deleteOldReports"
              class="bg-purple-600 hover:bg-purple-700 text-white font-semibold py-2 px-4 rounded-lg transition-all duration-300"
            >
              📅 Удалить старые (7+ дней)
            </button>
          </div>
        </div>
      </div>

      <!-- Фильтры -->
      <div class="bg-white rounded-2xl shadow-lg p-6 mb-8">
        <h2 class="text-xl font-semibold mb-4">🔍 Фильтры</h2>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
          <select v-model="filters.forest" class="p-3 border border-gray-300 rounded-lg">
            <option value="">Все леса</option>
            <option value="Борковский лес">Борковский лес</option>
            <option value="Сосновый бор">Сосновый бор</option>
            <option value="Заречный лесопарк">Заречный лесопарк</option>
            <option value="Городской парк">Городской парк</option>
            <option value="Пригородный лес">Пригородный лес</option>
          </select>
          
          <select v-model="filters.urgency" class="p-3 border border-gray-300 rounded-lg">
            <option value="">Все уровни срочности</option>
            <option value="critical">🔴 Критическая</option>
            <option value="high">🟠 Высокая</option>
            <option value="medium">🟡 Средняя</option>
            <option value="low">🟢 Низкая</option>
          </select>
          
          <select v-model="filters.report_type" class="p-3 border border-gray-300 rounded-lg">
            <option value="">Все типы проблем</option>
            <option value="Незаконная вырубка">🚫 Незаконная вырубка</option>
            <option value="Лесной пожар">🔥 Лесной пожар</option>
            <option value="Загрязнение мусором">🗑️ Загрязнение мусором</option>
            <option value="Болезнь деревьев">🌳 Болезнь деревьев</option>
            <option value="Вредители">🐛 Вредители</option>
            <option value="Другое">❓ Другое</option>
          </select>
        </div>
      </div>

      <!-- Список отчётов -->
      <div class="space-y-6">
        <div 
          v-for="report in filteredReports" 
          :key="report.id"
          class="bg-white rounded-2xl shadow-lg p-6 border-l-4 hover:shadow-xl transition-all duration-300 relative"
          :class="getUrgencyBorder(report.urgency_level)"
        >
          <!-- Кнопка удаления для админа -->
          <button 
            v-if="isAdmin"
            @click="deleteReport(report.id)"
            class="absolute top-4 right-4 bg-red-500 hover:bg-red-600 text-white p-2 rounded-lg transition-all duration-300"
            title="Удалить отчёт"
          >
            🗑️
          </button>
          
          <div class="flex justify-between items-start mb-4 pr-10">
            <div class="flex-1">
              <div class="flex items-center gap-3 mb-2">
                <h3 class="text-lg font-semibold text-gray-800">{{ report.forest_name }}</h3>
                <span class="px-2 py-1 rounded-full text-xs font-medium" 
                      :class="getUrgencyBadge(report.urgency_level)">
                  {{ getUrgencyText(report.urgency_level) }}
                </span>
              </div>
              <p class="text-sm text-gray-500">{{ formatDate(report.date) }}</p>
            </div>
            <div class="text-right">
              <span class="px-3 py-1 bg-blue-100 text-blue-800 rounded-full text-xs font-medium">
                {{ report.report_type }}
              </span>
            </div>
          </div>
          
          <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-4">
            <div>
              <p class="text-sm text-gray-600 mb-1">📍 Район:</p>
              <p class="font-medium">{{ report.location }}</p>
            </div>
            <div>
              <p class="text-sm text-gray-600 mb-1">👤 Сообщил:</p>
              <p class="font-medium">{{ report.reporter_name || 'Аноним' }}</p>
            </div>
          </div>
          
          <div class="mb-4">
            <p class="text-sm text-gray-600 mb-2">📝 Описание проблемы:</p>
            <p class="text-gray-700 leading-relaxed">{{ report.description }}</p>
          </div>
          
          <div class="flex justify-between items-center">
            <span class="text-sm text-gray-500">
              ID: {{ report.id }}
            </span>
            <span class="text-sm text-gray-500">
              {{ formatTimeAgo(report.date) }}
            </span>
          </div>
        </div>

        <!-- Если отчётов нет -->
        <div v-if="filteredReports.length === 0" class="text-center py-12">
          <div class="text-6xl mb-4">🌲</div>
          <h3 class="text-xl font-semibold text-gray-600 mb-2">Пока нет отчётов</h3>
          <p class="text-gray-500">Отчёты появятся здесь после отправки с главной страницы</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
const reports = ref<any[]>([])
const filters = ref({
  forest: '',
  urgency: '',
  report_type: ''
})

// Админ-функционал
const isAdmin = ref(false)
const showAdminLogin = ref(false)
const adminCredentials = ref({
  login: '',
  password: ''
})

const API_BASE = '/api'

// Проверяем авторизацию при загрузке
onMounted(() => {
  checkAdminAuth()
  loadReports()
})

// Проверка авторизации в localStorage
const checkAdminAuth = () => {
  const auth = localStorage.getItem('adminAuth')
  if (auth === 'true') {
    isAdmin.value = true
  }
}

// Авторизация администратора
const login = () => {
  if (adminCredentials.value.login === 'admin' && adminCredentials.value.password === 'admin') {
    isAdmin.value = true
    showAdminLogin.value = false
    localStorage.setItem('adminAuth', 'true')
    adminCredentials.value.login = ''
    adminCredentials.value.password = ''
  } else {
    alert('Неверный логин или пароль!')
  }
}

// Выход из системы
const logout = () => {
  isAdmin.value = false
  localStorage.removeItem('adminAuth')
}

// Загрузка отчётов из бекенда
const loadReports = async () => {
  try {
    const response = await fetch(`${API_BASE}/forest-reports`)
    if (response.ok) {
      reports.value = await response.json()
    }
  } catch (error) {
    console.error('Error loading reports:', error)
  }
}

// Удаление одного отчёта
const deleteReport = async (reportId: string) => {
  if (!confirm('Вы уверены, что хотите удалить этот отчёт?')) {
    return
  }

  try {
    const response = await fetch(`${API_BASE}/forest-reports/${reportId}`, {
      method: 'DELETE'
    })
    
    if (response.ok) {
      reports.value = reports.value.filter(r => r.id !== reportId)
    } else {
      alert('Ошибка при удалении отчёта')
    }
  } catch (error) {
    console.error('Error deleting report:', error)
    alert('Ошибка при удалении отчёта')
  }
}

// Удаление всех отчётов
const deleteAllReports = async () => {
  if (!confirm('Вы уверены, что хотите удалить ВСЕ отчёты? Это действие нельзя отменить!')) {
    return
  }

  try {
    const response = await fetch(`${API_BASE}/forest-reports`, {
      method: 'DELETE'
    })
    
    if (response.ok) {
      reports.value = []
      alert('Все отчёты успешно удалены')
    } else {
      alert('Ошибка при удалении отчётов')
    }
  } catch (error) {
    console.error('Error deleting all reports:', error)
    alert('Ошибка при удалении отчётов')
  }
}

// Удаление критических отчётов
const deleteCriticalReports = async () => {
  const criticalReports = reports.value.filter(r => r.urgency_level === 'critical')
  if (criticalReports.length === 0) {
    alert('Нет критических отчётов для удаления')
    return
  }

  if (!confirm(`Удалить ${criticalReports.length} критических отчётов?`)) {
    return
  }

  try {
    for (const report of criticalReports) {
      await fetch(`${API_BASE}/forest-reports/${report.id}`, {
        method: 'DELETE'
      })
    }
    
    reports.value = reports.value.filter(r => r.urgency_level !== 'critical')
    alert('Критические отчёты успешно удалены')
  } catch (error) {
    console.error('Error deleting critical reports:', error)
    alert('Ошибка при удалении критических отчётов')
  }
}

// Удаление старых отчётов (старше 7 дней)
const deleteOldReports = async () => {
  const sevenDaysAgo = new Date()
  sevenDaysAgo.setDate(sevenDaysAgo.getDate() - 7)
  
  const oldReports = reports.value.filter(r => new Date(r.date) < sevenDaysAgo)
  
  if (oldReports.length === 0) {
    alert('Нет отчётов старше 7 дней')
    return
  }

  if (!confirm(`Удалить ${oldReports.length} отчётов старше 7 дней?`)) {
    return
  }

  try {
    for (const report of oldReports) {
      await fetch(`${API_BASE}/forest-reports/${report.id}`, {
        method: 'DELETE'
      })
    }
    
    reports.value = reports.value.filter(r => new Date(r.date) >= sevenDaysAgo)
    alert('Старые отчёты успешно удалены')
  } catch (error) {
    console.error('Error deleting old reports:', error)
    alert('Ошибка при удалении старых отчётов')
  }
}

// Статистика
const criticalReports = computed(() => {
  return reports.value.filter(r => r.urgency_level === 'critical').length
})

const highPriorityReports = computed(() => {
  return reports.value.filter(r => r.urgency_level === 'high').length
})

const todayReports = computed(() => {
  const today = new Date().toLocaleDateString('ru-RU')
  return reports.value.filter(r => {
    const reportDate = new Date(r.date).toLocaleDateString('ru-RU')
    return reportDate === today
  }).length
})

// Фильтрация отчётов
const filteredReports = computed(() => {
  let filtered = [...reports.value]
  
  if (filters.value.forest) {
    filtered = filtered.filter(r => r.forest_name === filters.value.forest)
  }
  
  if (filters.value.urgency) {
    filtered = filtered.filter(r => r.urgency_level === filters.value.urgency)
  }
  
  if (filters.value.report_type) {
    filtered = filtered.filter(r => r.report_type === filters.value.report_type)
  }
  
  return filtered.sort((a, b) => new Date(b.date).getTime() - new Date(a.date).getTime())
})

// Функции для оформления
const getUrgencyBorder = (urgency: string) => {
  switch (urgency) {
    case 'critical': return 'border-l-red-500'
    case 'high': return 'border-l-orange-500'
    case 'medium': return 'border-l-yellow-500'
    case 'low': return 'border-l-green-500'
    default: return 'border-l-gray-500'
  }
}

const getUrgencyBadge = (urgency: string) => {
  switch (urgency) {
    case 'critical': return 'bg-red-100 text-red-800'
    case 'high': return 'bg-orange-100 text-orange-800'
    case 'medium': return 'bg-yellow-100 text-yellow-800'
    case 'low': return 'bg-green-100 text-green-800'
    default: return 'bg-gray-100 text-gray-800'
  }
}

const getUrgencyText = (urgency: string) => {
  switch (urgency) {
    case 'critical': return '🔴 Критическая'
    case 'high': return '🟠 Высокая'
    case 'medium': return '🟡 Средняя'
    case 'low': return '🟢 Низкая'
    default: return urgency
  }
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

const formatTimeAgo = (dateString: string) => {
  const date = new Date(dateString)
  const now = new Date()
  const diffMs = now.getTime() - date.getTime()
  const diffMins = Math.floor(diffMs / 60000)
  const diffHours = Math.floor(diffMs / 3600000)
  const diffDays = Math.floor(diffMs / 86400000)

  if (diffMins < 1) return 'только что'
  if (diffMins < 60) return `${diffMins} мин. назад`
  if (diffHours < 24) return `${diffHours} ч. назад`
  if (diffDays === 1) return 'вчера'
  if (diffDays < 7) return `${diffDays} дн. назад`
  
  return formatDate(dateString)
}
</script>