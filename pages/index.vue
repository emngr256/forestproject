<template>
  <div class="min-h-screen bg-white">
    <!-- Hero Section -->
    <section id="home" class="relative pt-16 md:pt-20 min-h-screen flex items-center overflow-hidden">
      <!-- Фон с параллакс эффектом -->
      <div class="absolute inset-0 z-0">
        <div class="absolute inset-0 bg-gradient-to-br from-green-900/80 to-blue-900/60 z-10"></div>
        <img 
          src="/assets/images/forest.jpg" 
          alt="Лес Петропавловска" 
          class="w-full h-full object-cover transform scale-105"
          :style="{ transform: `scale(${1 + parallax * 0.05})` }"
        >
      </div>

      <!-- Анимированные элементы фона -->
      <div class="absolute inset-0 z-5 overflow-hidden">
        <div 
          v-for="i in 8" 
          :key="i"
          class="absolute w-4 h-4 bg-white/10 rounded-full animate-float"
          :style="{
            left: `${Math.random() * 100}%`,
            top: `${Math.random() * 100}%`,
            animationDelay: `${Math.random() * 5}s`,
            animationDuration: `${15 + Math.random() * 10}s`
          }"
        ></div>
      </div>

      <div class="container mx-auto px-4 sm:px-6 relative z-10">
        <div class="max-w-3xl">
          <!-- Alert Badge -->
          <div class="inline-flex items-center gap-3 bg-green-600/90 backdrop-blur-sm text-white px-6 py-3 rounded-full mb-8 animate-fade-in-up">
            <div class="w-3 h-3 bg-green-300 rounded-full animate-pulse"></div>
            <span class="font-medium">Мониторинг лесов Петропавловска</span>
          </div>

          <!-- Заголовок -->
          <h1 class="text-white mb-6 leading-tight">
            <span class="block text-4xl md:text-5xl lg:text-6xl font-bold mb-4 animate-fade-in-up" style="animation-delay: 0.1s">
              Защитим леса
            </span>
            <span class="block text-3xl md:text-4xl lg:text-5xl font-bold text-green-300 animate-fade-in-up" style="animation-delay: 0.2s">
              Петропавловска
            </span>
          </h1>

          <!-- Описание -->
          <p class="text-xl text-gray-200 mb-8 leading-relaxed max-w-2xl animate-fade-in-up" style="animation-delay: 0.3s">
            Леса нашего города нуждаются в защите от пожаров, незаконных вырубок и загрязнения. 
            Сообщайте о проблемах — вместе сохраним зелёные лёгкие города!
          </p>

          <!-- Форма для отчётов о лесах -->
          <div class="bg-white/15 backdrop-blur-lg rounded-2xl p-6 md:p-8 border border-white/30 mb-8 animate-fade-in-up" style="animation-delay: 0.4s">
            <div class="flex items-center gap-3 mb-6">
              <div class="w-10 h-10 bg-green-500/20 rounded-xl flex items-center justify-center">
                <span class="text-xl">🌲</span>
              </div>
              <h3 class="text-white text-2xl font-bold">Сообщить о проблеме в лесу</h3>
            </div>
            
            <form @submit.prevent="submitForestReport" class="space-y-6">
              <div class="grid grid-cols-1 md:grid-cols-2 gap-4 md:gap-6">
                <div class="space-y-2">
                  <label class="text-white text-sm font-medium flex items-center gap-2">
                    <span>Лесной массив</span>
                    <span class="text-red-400">*</span>
                  </label>
                  <select 
                    v-model="forestReport.forest_name"
                    class="w-full px-4 py-3 rounded-xl bg-white/95 border border-white/40 focus:border-green-400 focus:ring-4 focus:ring-green-400/20 transition-all duration-300"
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
                
                <div class="space-y-2">
                  <label class="text-white text-sm font-medium flex items-center gap-2">
                    <span>Район</span>
                    <span class="text-red-400">*</span>
                  </label>
                  <select 
                    v-model="forestReport.location"
                    class="w-full px-4 py-3 rounded-xl bg-white/95 border border-white/40 focus:border-green-400 focus:ring-4 focus:ring-green-400/20 transition-all duration-300"
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

              <div class="space-y-2">
                <label class="text-white text-sm font-medium flex items-center gap-2">
                  <span>Тип проблемы</span>
                  <span class="text-red-400">*</span>
                </label>
                <select 
                  v-model="forestReport.report_type"
                  class="w-full px-4 py-3 rounded-xl bg-white/95 border border-white/40 focus:border-green-400 focus:ring-4 focus:ring-green-400/20 transition-all duration-300"
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

              <div class="space-y-2">
                <label class="text-white text-sm font-medium flex items-center gap-2">
                  <span>Срочность</span>
                  <span class="text-red-400">*</span>
                </label>
                <div class="grid grid-cols-2 md:grid-cols-4 gap-2">
                  <button
                    v-for="level in urgencyLevels"
                    :key="level.value"
                    type="button"
                    @click="forestReport.urgency_level = level.value"
                    :class="[
                      'py-3 px-4 rounded-xl border-2 transition-all duration-300 font-medium',
                      forestReport.urgency_level === level.value 
                        ? level.selectedClass 
                        : level.defaultClass
                    ]"
                  >
                    <span class="flex items-center justify-center gap-2">
                      <span>{{ level.emoji }}</span>
                      <span class="hidden sm:inline">{{ level.label }}</span>
                    </span>
                  </button>
                </div>
              </div>
              
              <div class="space-y-2">
                <label class="text-white text-sm font-medium flex items-center gap-2">
                  <span>Описание проблемы</span>
                  <span class="text-red-400">*</span>
                </label>
                <textarea 
                  v-model="forestReport.description"
                  placeholder="Подробно опишите проблему, укажите точное местоположение если возможно..."
                  rows="4"
                  class="w-full px-4 py-3 rounded-xl bg-white/95 border border-white/40 focus:border-green-400 focus:ring-4 focus:ring-green-400/20 transition-all duration-300 resize-none"
                  required
                ></textarea>
              </div>

              <div class="space-y-2">
                <label class="text-white text-sm font-medium">Ваше имя (необязательно)</label>
                <input 
                  v-model="forestReport.reporter_name"
                  type="text" 
                  placeholder="Как к вам обращаться?"
                  class="w-full px-4 py-3 rounded-xl bg-white/95 border border-white/40 focus:border-green-400 focus:ring-4 focus:ring-green-400/20 transition-all duration-300"
                >
              </div>
              
              <button 
                type="submit"
                :disabled="isSubmitting"
                :class="[
                  'w-full py-4 px-6 rounded-xl font-semibold transition-all duration-300 transform flex items-center justify-center gap-3',
                  isSubmitting 
                    ? 'bg-gray-400 cursor-not-allowed' 
                    : 'bg-gradient-to-r from-green-600 to-green-700 hover:from-green-700 hover:to-green-800 hover:scale-105 shadow-lg hover:shadow-xl'
                ]"
              >
                <span v-if="isSubmitting" class="flex items-center gap-3">
                  <div class="w-5 h-5 border-2 border-white/30 border-t-white rounded-full animate-spin"></div>
                  Отправка отчёта...
                </span>
                <span v-else class="flex items-center gap-3 text-white">
                  <span class="text-xl">📤</span>
                  Отправить отчёт
                </span>
              </button>
            </form>

            <!-- Уведомления -->
            <Transition name="slide-down">
              <div v-if="showSuccess" class="mt-4 p-4 bg-green-500/90 backdrop-blur-sm text-white rounded-xl text-center border border-green-300">
                <div class="flex items-center justify-center gap-3">
                  <div class="w-6 h-6 bg-white rounded-full flex items-center justify-center">
                    <span class="text-green-500 text-sm">✓</span>
                  </div>
                  <span class="font-medium">Спасибо! Ваш отчёт отправлен. Мы уже реагируем!</span>
                </div>
              </div>
            </Transition>

            <Transition name="slide-down">
              <div v-if="showError" class="mt-4 p-4 bg-red-500/90 backdrop-blur-sm text-white rounded-xl text-center border border-red-300">
                <div class="flex items-center justify-center gap-3">
                  <span class="text-xl">❌</span>
                  <span class="font-medium">Ошибка при отправке. Попробуйте еще раз.</span>
                </div>
              </div>
            </Transition>
          </div>

          <!-- Кнопка перехода к отчётам -->
          <div class="text-center animate-fade-in-up" style="animation-delay: 0.5s">
            <a 
              href="/reports" 
              class="inline-flex items-center gap-3 bg-blue-600 hover:bg-blue-700 text-white font-semibold py-4 px-8 rounded-xl transition-all duration-300 transform hover:scale-105 shadow-lg hover:shadow-xl"
            >
              <span class="text-xl">📊</span>
              Посмотреть все отчёты
              <ArrowRight :size="20" />
            </a>
          </div>
        </div>
      </div>

      <!-- Scroll Indicator -->
      <div class="absolute bottom-8 left-1/2 transform -translate-x-1/2 z-10 animate-bounce">
        <div class="w-6 h-10 border-2 border-white/50 rounded-full flex justify-center">
          <div class="w-1 h-3 bg-white/70 rounded-full mt-2 animate-pulse"></div>
        </div>
      </div>
    </section>

    <!-- Problems Section -->
    <section id="problems" class="py-20 bg-gradient-to-b from-gray-50 to-white">
      <div class="container mx-auto px-4 sm:px-6">
        <div class="text-center mb-16">
          <h2 class="mb-6 text-gray-800">Основные угрозы лесам Петропавловска</h2>
          <p class="text-xl text-gray-600 max-w-3xl mx-auto leading-relaxed">
            Наши леса сталкиваются с серьёзными экологическими проблемами, требующими внимания и активных действий
          </p>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8 mb-16">
          <div 
            v-for="(problem, index) in forestProblems" 
            :key="index" 
            class="group bg-white p-6 rounded-2xl shadow-lg hover:shadow-2xl transition-all duration-500 border border-gray-100 hover:border-green-200 hover:-translate-y-2"
            :style="`animation-delay: ${index * 0.1}s`"
          >
            <div class="flex items-start gap-4">
              <div class="w-14 h-14 bg-gradient-to-br from-green-100 to-green-50 rounded-xl flex items-center justify-center flex-shrink-0 group-hover:scale-110 transition-transform duration-300">
                <component :is="problem.icon" class="text-green-600" :size="28" />
              </div>
              <div class="flex-1">
                <div class="flex items-start justify-between mb-3">
                  <h3 class="text-lg font-semibold text-gray-800">{{ problem.title }}</h3>
                </div>
                <p class="text-gray-600 mb-4 leading-relaxed">{{ problem.description }}</p>
                <span :class="['inline-flex items-center gap-2 px-3 py-1.5 rounded-full text-xs font-semibold transition-colors', getSeverityColor(problem.severity)]">
                  <span class="w-2 h-2 rounded-full" :class="getSeverityDotColor(problem.severity)"></span>
                  {{ problem.severity }}
                </span>
              </div>
            </div>
          </div>
        </div>

        <!-- Призыв к действию -->
        <div class="text-center">
          <div class="bg-gradient-to-br from-white to-green-50 rounded-3xl p-8 md:p-12 shadow-2xl border border-green-100 max-w-4xl mx-auto">
            <div class="w-20 h-20 bg-green-100 rounded-2xl flex items-center justify-center mx-auto mb-6">
              <span class="text-3xl">💡</span>
            </div>
            <h3 class="text-3xl font-bold text-gray-800 mb-4">Есть идеи по улучшению?</h3>
            <p class="text-gray-600 mb-8 text-lg leading-relaxed max-w-2xl mx-auto">
              Предложите свои решения экологических проблем лесов Петропавловска. 
              Ваши идеи могут помочь сохранить наши зелёные зоны для будущих поколений!
            </p>
            <a 
              href="/suggestions" 
              class="inline-flex items-center gap-4 bg-gradient-to-r from-green-600 to-green-700 hover:from-green-700 hover:to-green-800 text-white font-semibold py-4 px-10 rounded-xl text-lg transition-all duration-300 transform hover:scale-105 shadow-lg hover:shadow-xl"
            >
              <span class="text-xl">💡</span>
              Перейти к предложениям
              <ArrowRight :size="24" />
            </a>
          </div>
        </div>
      </div>
    </section>

    <!-- Emergency Alert Button -->
    <Transition name="bounce">
      <div v-if="!showEmergencyAlert" class="fixed bottom-6 right-6 z-50">
        <button 
          @click="showEmergencyAlert = true"
          class="group bg-red-600 hover:bg-red-700 text-white p-4 rounded-2xl shadow-2xl transition-all duration-300 transform hover:scale-110 animate-pulse"
        >
          <div class="relative">
            <AlertTriangle :size="28" />
            <div class="absolute -top-1 -right-1 w-3 h-3 bg-red-300 rounded-full animate-ping"></div>
          </div>
          <div class="absolute -bottom-2 -right-2 w-6 h-6 bg-red-500 rounded-full flex items-center justify-center text-xs font-bold">
            !
          </div>
        </button>
      </div>
    </Transition>

    <!-- Emergency Alert Modal -->
    <Teleport to="body">
      <Transition name="modal">
        <div 
          v-if="showEmergencyAlert"
          class="fixed inset-0 bg-black/70 backdrop-blur-sm z-[10000] flex items-center justify-center p-4"
          @click.self="showEmergencyAlert = false"
        >
          <div class="bg-white rounded-2xl max-w-md w-full p-6 border-4 border-red-500 shadow-2xl transform transition-all duration-300">
            <div class="text-center mb-6">
              <div class="w-16 h-16 bg-red-100 rounded-2xl flex items-center justify-center mx-auto mb-4">
                <AlertTriangle class="text-red-500" :size="32" />
              </div>
              <h3 class="text-2xl font-bold text-red-600 mb-2">ЭКСТРЕННОЕ УВЕДОМЛЕНИЕ</h3>
              <p class="text-gray-600">Только для серьёзных угроз!</p>
            </div>

            <form @submit.prevent="sendEmergencyAlert" class="space-y-4">
              <div class="space-y-2">
                <label class="text-sm font-medium text-gray-700">Лесной массив *</label>
                <input 
                  v-model="emergencyAlert.forest_name"
                  type="text" 
                  placeholder="Укажите лесной массив"
                  class="w-full px-4 py-3 rounded-xl border border-gray-300 focus:border-red-400 focus:ring-4 focus:ring-red-400/20 transition-all duration-300"
                  required
                >
              </div>
              
              <div class="space-y-2">
                <label class="text-sm font-medium text-gray-700">Местоположение *</label>
                <input 
                  v-model="emergencyAlert.location"
                  type="text" 
                  placeholder="Точное местоположение"
                  class="w-full px-4 py-3 rounded-xl border border-gray-300 focus:border-red-400 focus:ring-4 focus:ring-red-400/20 transition-all duration-300"
                  required
                >
              </div>
              
              <div class="space-y-2">
                <label class="text-sm font-medium text-gray-700">Тип ЧС *</label>
                <select 
                  v-model="emergencyAlert.emergency_type"
                  class="w-full px-4 py-3 rounded-xl border border-gray-300 focus:border-red-400 focus:ring-4 focus:ring-red-400/20 transition-all duration-300"
                  required
                >
                  <option value="">Выберите тип ЧС</option>
                  <option value="Крупный пожар">🔥 Крупный пожар</option>
                  <option value="Массовая вырубка">🚫 Массовая вырубка</option>
                  <option value="Химическое загрязнение">☣️ Химическое загрязнение</option>
                </select>
              </div>
              
              <div class="space-y-2">
                <label class="text-sm font-medium text-gray-700">Детали ситуации *</label>
                <textarea 
                  v-model="emergencyAlert.details"
                  placeholder="Подробно опишите чрезвычайную ситуацию..."
                  rows="3"
                  class="w-full px-4 py-3 rounded-xl border border-gray-300 focus:border-red-400 focus:ring-4 focus:ring-red-400/20 transition-all duration-300 resize-none"
                  required
                ></textarea>
              </div>
              
              <div class="flex gap-3">
                <button 
                  type="submit" 
                  class="flex-1 bg-gradient-to-r from-red-600 to-red-700 hover:from-red-700 hover:to-red-800 text-white font-semibold py-3 px-6 rounded-xl transition-all duration-300 transform hover:scale-105"
                >
                  🚨 Отправить
                </button>
                <button 
                  @click="showEmergencyAlert = false" 
                  type="button" 
                  class="flex-1 bg-gray-500 hover:bg-gray-600 text-white font-semibold py-3 px-6 rounded-xl transition-all duration-300"
                >
                  Отмена
                </button>
              </div>
            </form>
          </div>
        </div>
      </Transition>
    </Teleport>
  </div>
</template>

<script setup>
// Импорты
import { ref, onMounted, onUnmounted } from 'vue'
import { AlertCircle, AlertTriangle, TreePine, Flame, Trash2, Bug, Axe, Truck, ArrowRight } from 'lucide-vue-next'

const API_BASE = '/api'

// Параллакс эффект
const parallax = ref(0)

const handleScroll = () => {
  parallax.value = window.scrollY / 1000
}

onMounted(() => {
  window.addEventListener('scroll', handleScroll)
})

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
})

// Форма для отчётов о лесах
const forestReport = ref({
  forest_name: '',
  location: '',
  report_type: '',
  description: '',
  reporter_name: '',
  urgency_level: 'medium'
})

// Уровни срочности
const urgencyLevels = [
  { value: 'low', label: 'Низкая', emoji: '🟢', defaultClass: 'bg-white/80 border-green-200 text-gray-700', selectedClass: 'bg-green-500 border-green-500 text-white' },
  { value: 'medium', label: 'Средняя', emoji: '🟡', defaultClass: 'bg-white/80 border-yellow-200 text-gray-700', selectedClass: 'bg-yellow-500 border-yellow-500 text-white' },
  { value: 'high', label: 'Высокая', emoji: '🟠', defaultClass: 'bg-white/80 border-orange-200 text-gray-700', selectedClass: 'bg-orange-500 border-orange-500 text-white' },
  { value: 'critical', label: 'Критическая', emoji: '🔴', defaultClass: 'bg-white/80 border-red-200 text-gray-700', selectedClass: 'bg-red-500 border-red-500 text-white' }
]

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
    description: "Массовая незаконная вырубка деревьев в пригородных лесах наносит непоправимый ущерб экосистеме.",
    severity: "Критично",
  },
  {
    icon: Flame,
    title: "Лесные пожары",
    description: "Участившиеся случаи лесных пожаров в засушливый период угрожают биоразнообразию.",
    severity: "Высокая",
  },
  {
    icon: Trash2,
    title: "Загрязнение мусором",
    description: "Стихийные свалки в лесопарковых зонах города создают угрозу для животных и растений.",
    severity: "Высокая",
  },
  {
    icon: Bug,
    title: "Вредители леса",
    description: "Распространение короеда и других вредителей ослабляет здоровье лесных массивов.",
    severity: "Средняя",
  },
  {
    icon: Truck,
    title: "Техногенное воздействие",
    description: "Загрязнение от транспорта и промышленности влияет на качество воздуха и почвы.",
    severity: "Средняя",
  },
  {
    icon: TreePine,
    title: "Болезни деревьев",
    description: "Ухудшение состояния хвойных насаждений требует внимания специалистов.",
    severity: "Средняя",
  },
]

// Вспомогательная функция для цветов severity
const getSeverityColor = (severity) => {
  switch (severity) {
    case "Критично":
      return "bg-red-50 text-red-700 border border-red-200"
    case "Высокая":
      return "bg-orange-50 text-orange-700 border border-orange-200"
    case "Средняя":
      return "bg-yellow-50 text-yellow-700 border border-yellow-200"
    default:
      return "bg-gray-50 text-gray-700 border border-gray-200"
  }
}

const getSeverityDotColor = (severity) => {
  switch (severity) {
    case "Критично":
      return "bg-red-500"
    case "Высокая":
      return "bg-orange-500"
    case "Средняя":
      return "bg-yellow-500"
    default:
      return "bg-gray-500"
  }
}
</script>

<style scoped>
h1 {
  @apply text-5xl md:text-6xl font-bold;
}

h2 {
  @apply text-4xl md:text-5xl font-bold text-gray-900;
}

/* Анимации */
@keyframes float {
  0%, 100% { transform: translateY(0px) rotate(0deg); }
  50% { transform: translateY(-20px) rotate(180deg); }
}

@keyframes fade-in-up {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.animate-float {
  animation: float 15s ease-in-out infinite;
}

.animate-fade-in-up {
  animation: fade-in-up 0.8s ease-out forwards;
  opacity: 0;
}

/* Transition styles */
.slide-down-enter-active,
.slide-down-leave-active {
  transition: all 0.3s ease;
}

.slide-down-enter-from,
.slide-down-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}

.modal-enter-active,
.modal-leave-active {
  transition: all 0.3s ease;
}

.modal-enter-from,
.modal-leave-to {
  opacity: 0;
  transform: scale(0.9);
}

.bounce-enter-active {
  animation: bounce-in 0.5s;
}

.bounce-leave-active {
  animation: bounce-in 0.5s reverse;
}

@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.1);
  }
  100% {
    transform: scale(1);
  }
}
</style>