<template>
  <div>
    <!-- Hero Section -->
    <section id="home" class="relative pt-20 min-h-screen flex items-center">
      <div class="absolute inset-0 z-0">
        <img src="/assets/images/forest.jpg" alt="Лес Петропавловска" class="w-full h-full object-cover">
        <div class="absolute inset-0 bg-black/40"></div>
      </div>

      <div class="container mx-auto px-4 relative z-10">
        <div class="max-w-3xl">
          <div class="inline-flex items-start gap-2 bg-green-600/90 text-white px-4 py-2 rounded-full mb-6">
            <AlertCircle :size="20" />
            <span>Мониторинг лесов Петропавловска</span>
          </div>

          <h1 class="text-white mb-6">
            Защитим леса Петропавловска
          </h1>

          <p class="text-xl text-gray-200 mb-8 leading-relaxed">
            Леса нашего города нуждаются в защите от пожаров, незаконных вырубок и загрязнения. 
            Сообщайте о проблемах - вместе сохраним зелёные лёгкие города!
          </p>

          <!-- Форма для отчётов о лесах -->
          <div class="bg-white/20 backdrop-blur-sm rounded-2xl p-6 border border-white/30 mb-6">
            <h3 class="text-white text-xl font-bold mb-4">🌲 Сообщить о проблеме в лесу</h3>
            
            <form @submit.prevent="submitForestReport" class="space-y-4">
              <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                <div>
                  <label class="text-white text-sm font-medium mb-2 block">Лесной массив *</label>
                  <select 
                    v-model="forestReport.forest_name"
                    class="w-full px-4 py-3 rounded-lg bg-white/90 border border-white/30 focus:border-green-400 focus:ring-2 focus:ring-green-400/20"
                    required
                  >
                    <option value="">Выберите лес</option>
                    <option value="Борковский лес">Борковский лес</option>
                    <option value="Сосновый бор">Сосновый бор</option>
                    <option value="Заречный лесопарк">Заречный лесопарк</option>
                    <option value="Городской парк">Городской парк</option>
                    <option value="Пригородный лес">Пригородный лес</option>
                  </select>
                </div>
                
                <div>
                  <label class="text-white text-sm font-medium mb-2 block">Район *</label>
                  <select 
                    v-model="forestReport.location"
                    class="w-full px-4 py-3 rounded-lg bg-white/90 border border-white/30 focus:border-green-400 focus:ring-2 focus:ring-green-400/20"
                    required
                  >
                    <option value="">Выберите район</option>
                    <option value="Северный район">Северный район</option>
                    <option value="Центральный район">Центральный район</option>
                    <option value="Южный район">Южный район</option>
                    <option value="Заречный район">Заречный район</option>
                  </select>
                </div>
              </div>

              <div>
                <label class="text-white text-sm font-medium mb-2 block">Тип проблемы *</label>
                <select 
                  v-model="forestReport.report_type"
                  class="w-full px-4 py-3 rounded-lg bg-white/90 border border-white/30 focus:border-green-400 focus:ring-2 focus:ring-green-400/20"
                  required
                >
                  <option value="">Выберите тип проблемы</option>
                  <option value="Незаконная вырубка">🚫 Незаконная вырубка</option>
                  <option value="Лесной пожар">🔥 Лесной пожар</option>
                  <option value="Загрязнение мусором">🗑️ Загрязнение мусором</option>
                  <option value="Болезнь деревьев">🌳 Болезнь деревьев</option>
                  <option value="Вредители">🐛 Вредители</option>
                  <option value="Другое">❓ Другое</option>
                </select>
              </div>

              <div>
                <label class="text-white text-sm font-medium mb-2 block">Срочность *</label>
                <select 
                  v-model="forestReport.urgency_level"
                  class="w-full px-4 py-3 rounded-lg bg-white/90 border border-white/30 focus:border-green-400 focus:ring-2 focus:ring-green-400/20"
                  required
                >
                  <option value="low">🟢 Низкая</option>
                  <option value="medium" selected>🟡 Средняя</option>
                  <option value="high">🟠 Высокая</option>
                  <option value="critical">🔴 Критическая</option>
                </select>
              </div>
              
              <div>
                <label class="text-white text-sm font-medium mb-2 block">Описание проблемы *</label>
                <textarea 
                  v-model="forestReport.description"
                  placeholder="Подробно опишите проблему, укажите точное местоположение если возможно..."
                  rows="4"
                  class="w-full px-4 py-3 rounded-lg bg-white/90 border border-white/30 focus:border-green-400 focus:ring-2 focus:ring-green-400/20 resize-none"
                  required
                ></textarea>
              </div>

              <div>
                <label class="text-white text-sm font-medium mb-2 block">Ваше имя (необязательно)</label>
                <input 
                  v-model="forestReport.reporter_name"
                  type="text" 
                  placeholder="Как к вам обращаться?"
                  class="w-full px-4 py-3 rounded-lg bg-white/90 border border-white/30 focus:border-green-400 focus:ring-2 focus:ring-green-400/20"
                >
              </div>
              
              <button 
                type="submit"
                :disabled="isSubmitting"
                class="w-full bg-green-600 hover:bg-green-700 text-white font-semibold py-3 px-6 rounded-lg transition-all duration-300 transform hover:scale-105 disabled:opacity-50 disabled:cursor-not-allowed"
              >
                <span v-if="isSubmitting">⏳ Отправка отчёта...</span>
                <span v-else>📤 Отправить отчёт</span>
              </button>
            </form>

            <!-- Успешное сообщение -->
            <div v-if="showSuccess" class="mt-4 p-3 bg-green-500/90 text-white rounded-lg text-center">
              ✅ Спасибо! Ваш отчёт отправлен. Мы уже реагируем!
            </div>

            <!-- Сообщение об ошибке -->
            <div v-if="showError" class="mt-4 p-3 bg-red-500/90 text-white rounded-lg text-center">
              ❌ Ошибка при отправке. Попробуйте еще раз.
            </div>
          </div>

          <!-- Кнопка перехода к отчётам -->
          <div class="text-center">
            <a 
              href="/reports" 
              class="inline-flex items-center gap-2 bg-blue-600 hover:bg-blue-700 text-white font-semibold py-3 px-8 rounded-lg transition-all duration-300 transform hover:scale-105"
            >
              📊 Посмотреть все отчёты
              <ArrowRight :size="20" />
            </a>
          </div>
        </div>
      </div>
    </section>

    <!-- Problems Section -->
    <section id="problems" class="py-20 bg-gray-50">
      <div class="container mx-auto px-4">
        <div class="text-center mb-12">
          <h2 class="mb-4">Основные угрозы лесам Петропавловска</h2>
          <p class="text-xl text-gray-600 max-w-3xl mx-auto">
            Наши леса сталкиваются с серьёзными экологическими проблемами, требующими внимания
          </p>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
          <div 
            v-for="(problem, index) in forestProblems" 
            :key="index" 
            class="bg-white p-6 rounded-xl shadow-md hover:shadow-lg transition-shadow border border-gray-200"
          >
            <div class="flex items-start gap-4">
              <div class="w-12 h-12 bg-green-100 rounded-lg flex items-center justify-center flex-shrink-0">
                <component :is="problem.icon" class="text-green-600" :size="24" />
              </div>
              <div class="flex-1">
                <div class="flex items-start justify-between mb-2">
                  <h3 class="text-lg font-semibold">{{ problem.title }}</h3>
                </div>
                <p class="text-gray-600 mb-3">{{ problem.description }}</p>
                <span :class="['inline-block px-3 py-1 rounded-full text-xs font-medium', getSeverityColor(problem.severity)]">
                  {{ problem.severity }}
                </span>
              </div>
            </div>
          </div>
        </div>

        <!-- Призыв к действию -->
        <div class="text-center mt-12">
          <div class="bg-white rounded-2xl p-8 shadow-lg border border-green-200 max-w-2xl mx-auto">
            <h3 class="text-2xl font-bold text-gray-800 mb-4">Есть идеи по улучшению?</h3>
            <p class="text-gray-600 mb-6 text-lg">
              Предложите свои решения экологических проблем лесов Петропавловска. 
              Ваши идеи могут помочь сохранить наши зелёные зоны!
            </p>
            <a 
              href="/suggestions" 
              class="inline-flex items-center gap-3 bg-green-600 hover:bg-green-700 text-white font-semibold py-4 px-8 rounded-lg text-lg transition-all duration-300 transform hover:scale-105"
            >
              💡 Перейти к предложениям
              <ArrowRight :size="24" />
            </a>
          </div>
        </div>
      </div>
    </section>

    <!-- Emergency Alert Button -->
    <div class="fixed bottom-6 right-6 z-50">
      <button 
        @click="showEmergencyAlert = true"
        class="bg-red-600 hover:bg-red-700 text-white p-4 rounded-full shadow-2xl animate-pulse"
      >
        <AlertTriangle :size="24" />
      </button>
    </div>

    <!-- Emergency Alert Modal -->
    <Teleport to="body">
      <div 
        v-if="showEmergencyAlert"
        class="fixed inset-0 bg-black/70 backdrop-blur-sm z-[10000] flex items-center justify-center p-4"
      >
        <div class="bg-white rounded-xl max-w-md w-full p-6 border-4 border-red-500">
          <div class="text-center mb-4">
            <AlertTriangle class="text-red-500 mx-auto mb-2" :size="48" />
            <h3 class="text-xl font-bold text-red-600">ЭКСТРЕННОЕ УВЕДОМЛЕНИЕ</h3>
            <p class="text-gray-600 mt-2">Только для серьёзных угроз!</p>
          </div>

          <form @submit.prevent="sendEmergencyAlert" class="space-y-4">
            <input 
              v-model="emergencyAlert.forest_name"
              type="text" 
              placeholder="Лесной массив"
              class="w-full px-3 py-2 border border-gray-300 rounded-lg"
              required
            >
            <input 
              v-model="emergencyAlert.location"
              type="text" 
              placeholder="Местоположение"
              class="w-full px-3 py-2 border border-gray-300 rounded-lg"
              required
            >
            <select 
              v-model="emergencyAlert.emergency_type"
              class="w-full px-3 py-2 border border-gray-300 rounded-lg"
              required
            >
              <option value="">Тип ЧС</option>
              <option value="Крупный пожар">🔥 Крупный пожар</option>
              <option value="Массовая вырубка">🚫 Массовая вырубка</option>
              <option value="Химическое загрязнение">☣️ Химическое загрязнение</option>
            </select>
            <textarea 
              v-model="emergencyAlert.details"
              placeholder="Детали чрезвычайной ситуации..."
              rows="3"
              class="w-full px-3 py-2 border border-gray-300 rounded-lg resize-none"
              required
            ></textarea>
            <div class="flex gap-2">
              <button type="submit" class="flex-1 bg-red-600 text-white py-2 rounded-lg font-semibold">
                🚨 Отправить
              </button>
              <button @click="showEmergencyAlert = false" type="button" class="flex-1 bg-gray-500 text-white py-2 rounded-lg">
                Отмена
              </button>
            </div>
          </form>
        </div>
      </div>
    </Teleport>
  </div>
</template>

<script setup>
// Импорты
import { ref } from 'vue'
import { AlertCircle, AlertTriangle, TreePine, Flame, Trash2, Bug, Axe, Truck, ArrowRight } from 'lucide-vue-next'

const API_BASE = '/api'

// Форма для отчётов о лесах
const forestReport = ref({
  forest_name: '',
  location: '',
  report_type: '',
  description: '',
  reporter_name: '',
  urgency_level: 'medium'
})

// Экстренное уведомление
const emergencyAlert = ref({
  forest_name: '',
  location: '',
  emergency_type: '',
  details: '',
  reporter_name: ''
})

// Состояния UI
const showEmergencyAlert = ref(false)
const showSuccess = ref(false)
const showError = ref(false)
const isSubmitting = ref(false)

// Функция отправки отчёта о лесе
const submitForestReport = async () => {
  if (isSubmitting.value) return
  
  isSubmitting.value = true
  showError.value = false

  try {
    console.log('📤 Отправка отчета:', forestReport.value)
    
    const response = await $fetch(`${API_BASE}/forest-reports`, {
      method: 'POST',
      body: forestReport.value
    })

    console.log('✅ Ответ сервера:', response)

    // Успешно отправлено
    showSuccess.value = true
    
    // Сбрасываем форму
    forestReport.value = {
      forest_name: '',
      location: '',
      report_type: '',
      description: '',
      reporter_name: '',
      urgency_level: 'medium'
    }
    
    setTimeout(() => {
      showSuccess.value = false
    }, 5000)
  } catch (error) {
    console.error('❌ Ошибка при отправке отчета:', error)
    console.error('Детали ошибки:', error.data)
    showError.value = true
    setTimeout(() => {
      showError.value = false
    }, 3000)
  } finally {
    isSubmitting.value = false
  }
}

// Функция отправки экстренного уведомления
const sendEmergencyAlert = async () => {
  try {
    console.log('🚨 Отправка экстренного уведомления:', emergencyAlert.value)
    
    await $fetch(`${API_BASE}/forest-alert`, {
      method: 'POST',
      body: emergencyAlert.value
    })

    showEmergencyAlert.value = false
    emergencyAlert.value = {
      forest_name: '',
      location: '',
      emergency_type: '',
      details: '',
      reporter_name: ''
    }
    
    alert('🚨 Экстренное уведомление отправлено! Службы оповещены!')
  } catch (error) {
    console.error('❌ Ошибка при отправке экстренного уведомления:', error)
    alert('❌ Ошибка при отправке экстренного уведомления')
  }
}

// Данные для секции проблем
const forestProblems = [
  {
    icon: Axe,
    title: "Незаконные вырубки",
    description: "Массовая незаконная вырубка деревьев в пригородных лесах.",
    severity: "Критично",
  },
  {
    icon: Flame,
    title: "Лесные пожары",
    description: "Участившиеся случаи лесных пожаров в засушливый период.",
    severity: "Высокая",
  },
  {
    icon: Trash2,
    title: "Загрязнение мусором",
    description: "Стихийные свалки в лесопарковых зонах города.",
    severity: "Высокая",
  },
  {
    icon: Bug,
    title: "Вредители леса",
    description: "Распространение короеда и других вредителей.",
    severity: "Средняя",
  },
  {
    icon: Truck,
    title: "Техногенное воздействие",
    description: "Загрязнение от транспорта и промышленности.",
    severity: "Средняя",
  },
  {
    icon: TreePine,
    title: "Болезни деревьев",
    description: "Ухудшение состояния хвойных насаждений.",
    severity: "Средняя",
  },
]

// Вспомогательная функция для цветов severity
const getSeverityColor = (severity) => {
  switch (severity) {
    case "Критично":
      return "bg-red-100 text-red-800"
    case "Высокая":
      return "bg-orange-100 text-orange-800"
    case "Средняя":
      return "bg-yellow-100 text-yellow-800"
    default:
      return "bg-gray-100 text-gray-800"
  }
}
</script>

<style scoped>
h1 {
  @apply text-5xl font-bold;
}

h2 {
  @apply text-4xl font-bold;
}

h3 {
  @apply text-xl font-semibold;
}
</style>