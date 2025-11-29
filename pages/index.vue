<template>
  <div class="min-h-screen bg-white">
    <!-- Hero Section с плавным переходом -->
    <section id="home" class="relative min-h-screen flex items-center justify-center overflow-hidden">
      <!-- Фон с плавным переходом -->
      <div class="absolute inset-0 z-0">
        <div class="absolute inset-0 bg-gradient-to-b from-green-900/90 via-green-900/70 to-gray-50 z-10 transition-all duration-1000"
             :style="{ opacity: 1 - scrollProgress }"></div>
        <img 
          src="/assets/images/forest.jpg" 
          alt="Лес Петропавловска" 
          class="w-full h-full object-cover transform transition-all duration-1000 ease-out"
          :style="{ 
            transform: `scale(${1.1 + parallax * 0.02})`,
            opacity: 1 - scrollProgress 
          }"
        >
      </div>

      <!-- Контент -->
      <div class="relative z-10 w-full max-w-6xl mx-auto px-4 sm:px-6 h-full flex items-center justify-center">
        <div class="text-center text-white w-full max-w-4xl">
          <!-- Alert Badge -->
          <div class="inline-flex items-center gap-3 bg-green-600/90 backdrop-blur-sm text-white px-6 py-3 rounded-full mb-8 animate-fade-in"
               :style="{ opacity: 1 - scrollProgress * 2 }">
            <div class="w-3 h-3 bg-green-300 rounded-full animate-pulse"></div>
            <span class="font-medium">Мониторинг лесов Петропавловска</span>
          </div>

          <!-- Заголовок -->
          <h1 class="mb-6 leading-tight transition-all duration-500"
              :style="{ 
                transform: `translateY(${scrollProgress * -50}px)`,
                opacity: 1 - scrollProgress * 1.5 
              }">
            <span class="block text-5xl md:text-6xl lg:text-7xl font-bold mb-4">Защитим леса</span>
            <span class="block text-4xl md:text-5xl lg:text-6xl font-bold text-green-300">Петропавловска</span>
          </h1>

          <!-- Описание -->
          <p class="text-xl md:text-2xl text-gray-200 mb-12 leading-relaxed max-w-4xl mx-auto transition-all duration-500"
             :style="{ 
               transform: `translateY(${scrollProgress * -30}px)`,
               opacity: 1 - scrollProgress * 2 
             }">
            Леса нашего города нуждаются в защите от пожаров, незаконных вырубок и загрязнения. 
            Сообщайте о проблемах — вместе сохраним зелёные лёгкие города!
          </p>

          <!-- Форма для отчётов - занимает всё доступное пространство -->
          <div class="max-w-2xl mx-auto transition-all duration-500"
               :style="{ 
                 transform: `translateY(${scrollProgress * -20}px) scale(${1 + scrollProgress * 0.1})`,
                 opacity: 1 - scrollProgress * 0.5 
               }">
            <div 
              class="bg-white/10 backdrop-blur-lg rounded-2xl border border-white/20 overflow-hidden transition-all duration-500 ease-out"
              :class="[
                isReportCollapsed 
                  ? 'max-h-16 hover:bg-white/15 cursor-pointer' 
                  : 'max-h-[80vh]'
              ]"
            >
              <!-- Заголовок формы -->
              <div 
                class="p-4 flex items-center justify-between cursor-pointer transition-all duration-300"
                @click="toggleReportForm"
              >
                <div class="flex items-center gap-3">
                  <div class="w-10 h-10 bg-green-500/20 rounded-xl flex items-center justify-center transition-transform duration-300"
                       :class="isReportCollapsed ? '' : 'rotate-12'">
                    <span class="text-xl">🌲</span>
                  </div>
                  <div>
                    <h3 class="text-white text-xl font-bold">Сообщить о проблеме</h3>
                    <p class="text-green-200 text-xs mt-1">Помогите сохранить наши леса</p>
                  </div>
                </div>
                <div class="text-white text-lg transition-transform duration-500"
                     :class="isReportCollapsed ? 'rotate-0' : 'rotate-180'">
                  <ChevronDown :size="24" />
                </div>
              </div>

              <!-- Содержимое формы -->
              <Transition name="slide-down">
                <div v-if="!isReportCollapsed" class="px-4 pb-4 border-t border-white/10 pt-4 h-full max-h-[70vh] overflow-y-auto">
                  <form @submit.prevent="submitForestReport" class="space-y-4">
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                      <div class="space-y-2">
                        <label class="text-white text-sm font-medium block">Лесной массив *</label>
                        <select 
                          v-model="forestReport.forest_name"
                          class="w-full px-3 py-3 rounded-lg bg-white border border-gray-300 text-gray-800 focus:border-green-500 focus:ring-2 focus:ring-green-500/30 transition-all duration-200 text-sm"
                          required
                        >
                          <option value="" class="text-gray-500">Выберите лес</option>
                          <option value="Борковский лес">Борковский лес</option>
                          <option value="Сосновый бор">Сосновый бор</option>
                          <option value="Заречный лесопарк">Заречный лесопарк</option>
                          <option value="Городской парк">Городской парк</option>
                          <option value="Пригородный лес">Пригородный лес</option>
                        </select>
                      </div>
                      
                      <div class="space-y-2">
                        <label class="text-white text-sm font-medium block">Район *</label>
                        <select 
                          v-model="forestReport.location"
                          class="w-full px-3 py-3 rounded-lg bg-white border border-gray-300 text-gray-800 focus:border-green-500 focus:ring-2 focus:ring-green-500/30 transition-all duration-200 text-sm"
                          required
                        >
                          <option value="" class="text-gray-500">Выберите район</option>
                          <option value="Северный район">Северный район</option>
                          <option value="Центральный район">Центральный район</option>
                          <option value="Южный район">Южный район</option>
                          <option value="Заречный район">Заречный район</option>
                        </select>
                      </div>
                    </div>

                    <div class="space-y-2">
                      <label class="text-white text-sm font-medium block">Тип проблемы *</label>
                      <select 
                        v-model="forestReport.report_type"
                        class="w-full px-3 py-3 rounded-lg bg-white border border-gray-300 text-gray-800 focus:border-green-500 focus:ring-2 focus:ring-green-500/30 transition-all duration-200 text-sm"
                        required
                      >
                        <option value="" class="text-gray-500">Выберите тип проблемы</option>
                        <option value="Незаконная вырубка">🚫 Незаконная вырубка</option>
                        <option value="Лесной пожар">🔥 Лесной пожар</option>
                        <option value="Загрязнение мусором">🗑️ Загрязнение мусором</option>
                        <option value="Болезнь деревьев">🌳 Болезнь деревьев</option>
                        <option value="Вредители">🐛 Вредители</option>
                        <option value="Другое">❓ Другое</option>
                      </select>
                    </div>

                    <div class="space-y-2">
                      <label class="text-white text-sm font-medium block">Срочность *</label>
                      <div class="grid grid-cols-4 gap-2">
                        <button
                          v-for="level in urgencyLevels"
                          :key="level.value"
                          type="button"
                          @click="forestReport.urgency_level = level.value"
                          :class="[
                            'py-3 px-2 rounded-lg border transition-all duration-200 font-medium text-sm',
                            forestReport.urgency_level === level.value 
                              ? level.selectedClass 
                              : level.defaultClass
                          ]"
                        >
                          <span class="flex flex-col items-center gap-1">
                            <span class="text-base">{{ level.emoji }}</span>
                            <span class="text-xs">{{ level.label }}</span>
                          </span>
                        </button>
                      </div>
                    </div>
                    
                    <div class="space-y-2">
                      <label class="text-white text-sm font-medium block">Описание проблемы *</label>
                      <textarea 
                        v-model="forestReport.description"
                        placeholder="Опишите проблему подробно..."
                        rows="4"
                        class="w-full px-3 py-3 rounded-lg bg-white border border-gray-300 text-gray-800 focus:border-green-500 focus:ring-2 focus:ring-green-500/30 transition-all duration-200 resize-none text-sm"
                        required
                      ></textarea>
                    </div>

                    <div class="space-y-2">
                      <label class="text-white text-sm font-medium block">Ваше имя (необязательно)</label>
                      <input 
                        v-model="forestReport.reporter_name"
                        type="text" 
                        placeholder="Введите ваше имя"
                        class="w-full px-3 py-3 rounded-lg bg-white border border-gray-300 text-gray-800 focus:border-green-500 focus:ring-2 focus:ring-green-500/30 transition-all duration-200 text-sm"
                      >
                    </div>
                    
                    <button 
                      type="submit"
                      :disabled="isSubmitting"
                      :class="[
                        'w-full py-4 px-4 rounded-lg font-semibold transition-all duration-300 flex items-center justify-center gap-2 mt-4',
                        isSubmitting 
                          ? 'bg-gray-500 cursor-not-allowed text-white' 
                          : 'bg-green-600 hover:bg-green-700 transform hover:scale-105 text-white shadow-md'
                      ]"
                    >
                      <span v-if="isSubmitting" class="flex items-center gap-2">
                        <div class="w-4 h-4 border-2 border-white/30 border-t-white rounded-full animate-spin"></div>
                        Отправка...
                      </span>
                      <span v-else class="flex items-center gap-2">
                        <span class="text-lg">📤</span>
                        Отправить отчёт
                      </span>
                    </button>
                  </form>

                  <!-- Уведомления -->
                  <Transition name="fade">
                    <div v-if="showSuccess" class="mt-4 p-3 bg-green-500/90 backdrop-blur-sm text-white rounded-lg text-center border border-green-300">
                      <div class="flex items-center justify-center gap-2">
                        <div class="w-5 h-5 bg-white rounded-full flex items-center justify-center">
                          <span class="text-green-500 text-xs font-bold">✓</span>
                        </div>
                        <span class="font-medium text-sm">Спасибо! Ваш отчёт отправлен.</span>
                      </div>
                    </div>
                  </Transition>

                  <Transition name="fade">
                    <div v-if="showError" class="mt-4 p-3 bg-red-500/90 backdrop-blur-sm text-white rounded-lg text-center border border-red-300">
                      <div class="flex items-center justify-center gap-2">
                        <span class="text-lg">❌</span>
                        <span class="font-medium text-sm">Ошибка при отправке.</span>
                      </div>
                    </div>
                  </Transition>
                </div>
              </Transition>
            </div>
          </div>
        </div>
      </div>

      <!-- Scroll Indicator -->
      <div class="absolute bottom-8 left-1/2 transform -translate-x-1/2 z-20 animate-bounce transition-all duration-500"
           :style="{ opacity: 1 - scrollProgress * 3 }">
        <button 
          @click="scrollToProblems"
          class="group bg-white/10 backdrop-blur-sm border border-white/30 rounded-full p-3 transition-all duration-300 hover:bg-white/20 hover:scale-110"
        >
          <div class="w-5 h-8 flex justify-center">
            <div class="w-1 h-2 bg-white/80 rounded-full mt-2 animate-pulse"></div>
          </div>
        </button>
      </div>
    </section>

    <!-- Problems Section с плавным появлением -->
    <section id="problems" class="py-20 bg-gradient-to-b from-gray-50 to-white min-h-screen transition-all duration-1000"
             :style="{ 
               transform: `translateY(${scrollProgress * -100}px)`,
               opacity: scrollProgress 
             }">
      <div class="container mx-auto px-4 sm:px-6">
        <div class="text-center mb-16">
          <h2 class="text-4xl md:text-5xl font-bold text-gray-800 mb-6">Основные угрозы лесам</h2>
          <p class="text-xl text-gray-600 max-w-3xl mx-auto">
            Наши леса сталкиваются с серьёзными экологическими проблемами, требующими внимания и активных действий
          </p>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8 mb-16">
          <div 
            v-for="(problem, index) in forestProblems" 
            :key="index" 
            class="group bg-white p-8 rounded-3xl shadow-lg hover:shadow-2xl transition-all duration-500 border border-gray-100 hover:border-green-200 hover:-translate-y-2"
          >
            <div class="flex flex-col items-center text-center">
              <div class="w-20 h-20 bg-gradient-to-br from-green-100 to-green-50 rounded-2xl flex items-center justify-center mb-6 group-hover:scale-110 transition-transform duration-300">
                <component :is="problem.icon" class="text-green-600" :size="32" />
              </div>
              <h3 class="text-2xl font-bold text-gray-800 mb-4">{{ problem.title }}</h3>
              <p class="text-gray-600 mb-6 leading-relaxed">{{ problem.description }}</p>
              <span :class="['inline-flex items-center gap-2 px-4 py-2 rounded-full text-sm font-semibold transition-colors', getSeverityColor(problem.severity)]">
                <span class="w-2 h-2 rounded-full" :class="getSeverityDotColor(problem.severity)"></span>
                {{ problem.severity }}
              </span>
            </div>
          </div>
        </div>

        <!-- Примеры лесов Петропавловска -->
        <div class="mb-20">
          <div class="text-center mb-12">
            <h2 class="text-4xl md:text-5xl font-bold text-gray-800 mb-6">Леса Петропавловска</h2>
            <p class="text-xl text-gray-600 max-w-3xl mx-auto">
              Уникальные лесные массивы нашего города, которые нуждаются в защите и сохранении
            </p>
          </div>

          <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
            <div 
              v-for="forest in petropavlovskForests" 
              :key="forest.name"
              class="group bg-white rounded-3xl overflow-hidden shadow-lg hover:shadow-2xl transition-all duration-500 hover:-translate-y-2"
            >
              <div class="relative h-48 overflow-hidden">
                <img 
                  :src="forest.image" 
                  :alt="forest.name"
                  class="w-full h-full object-cover group-hover:scale-110 transition-transform duration-500"
                >
                <div class="absolute inset-0 bg-black/20 group-hover:bg-black/10 transition-all duration-300"></div>
              </div>
              <div class="p-6">
                <h3 class="text-xl font-bold text-gray-800 mb-3">{{ forest.name }}</h3>
                <p class="text-gray-600 text-sm leading-relaxed mb-4">{{ forest.description }}</p>
                <div class="flex items-center justify-between">
                  <span class="text-green-600 font-semibold">{{ forest.area }}</span>
                  <span class="text-gray-500 text-sm">{{ forest.type }}</span>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Призыв к действию -->
        <div class="text-center">
          <div class="bg-gradient-to-br from-green-50 to-white rounded-3xl p-12 shadow-2xl border border-green-100 max-w-4xl mx-auto">
            <div class="w-24 h-24 bg-green-100 rounded-3xl flex items-center justify-center mx-auto mb-8">
              <span class="text-4xl">💡</span>
            </div>
            <h3 class="text-4xl font-bold text-gray-800 mb-6">Есть идеи по улучшению?</h3>
            <p class="text-gray-600 text-lg mb-8 leading-relaxed max-w-2xl mx-auto">
              Предложите свои решения экологических проблем лесов Петропавловска. 
              Ваши идеи могут помочь сохранить наши зелёные зоны для будущих поколений!
            </p>
            <a 
              href="/suggestions" 
              class="inline-flex items-center gap-4 bg-green-600 hover:bg-green-700 text-white font-semibold py-4 px-12 rounded-xl text-lg transition-all duration-300 transform hover:scale-105 shadow-lg hover:shadow-xl"
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
    <div class="fixed bottom-6 right-6 z-50">
      <button 
        @click="showEmergencyAlert = true"
        class="group bg-red-600 hover:bg-red-700 text-white p-4 rounded-2xl shadow-2xl transition-all duration-300 transform hover:scale-110 animate-pulse"
      >
        <div class="relative">
          <AlertTriangle :size="28" />
          <div class="absolute -top-1 -right-1 w-3 h-3 bg-red-300 rounded-full animate-ping"></div>
        </div>
      </button>
    </div>

    <!-- Emergency Alert Modal -->
    <Teleport to="body">
      <Transition name="modal">
        <div 
          v-if="showEmergencyAlert"
          class="fixed inset-0 bg-black/70 backdrop-blur-sm z-[10000] flex items-center justify-center p-4"
          @click.self="showEmergencyAlert = false"
        >
          <div class="bg-white rounded-2xl max-w-md w-full p-6 border-4 border-red-500 shadow-2xl">
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
                  class="w-full px-4 py-3 rounded-xl border border-gray-300 focus:border-red-400 focus:ring-2 focus:ring-red-400/20 transition-all duration-200 text-gray-800"
                  required
                >
              </div>
              
              <div class="space-y-2">
                <label class="text-sm font-medium text-gray-700">Местоположение *</label>
                <input 
                  v-model="emergencyAlert.location"
                  type="text" 
                  placeholder="Точное местоположение"
                  class="w-full px-4 py-3 rounded-xl border border-gray-300 focus:border-red-400 focus:ring-2 focus:ring-red-400/20 transition-all duration-200 text-gray-800"
                  required
                >
              </div>
              
              <div class="space-y-2">
                <label class="text-sm font-medium text-gray-700">Тип ЧС *</label>
                <select 
                  v-model="emergencyAlert.emergency_type"
                  class="w-full px-4 py-3 rounded-xl border border-gray-300 focus:border-red-400 focus:ring-2 focus:ring-red-400/20 transition-all duration-200 text-gray-800"
                  required
                >
                  <option value="" class="text-gray-500">Выберите тип ЧС</option>
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
                  class="w-full px-4 py-3 rounded-xl border border-gray-300 focus:border-red-400 focus:ring-2 focus:ring-red-400/20 transition-all duration-200 resize-none text-gray-800"
                  required
                ></textarea>
              </div>
              
              <div class="flex gap-3">
                <button 
                  type="submit" 
                  class="flex-1 bg-red-600 hover:bg-red-700 text-white font-semibold py-3 px-6 rounded-xl transition-all duration-300 transform hover:scale-105"
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
import { AlertTriangle, TreePine, Flame, Trash2, Bug, Axe, Truck, ArrowRight, ChevronDown } from 'lucide-vue-next'

const API_BASE = '/api'

// Прогресс скролла для плавных переходов
const scrollProgress = ref(0)
const parallax = ref(0)

const handleScroll = () => {
  const scrollY = window.scrollY
  const windowHeight = window.innerHeight
  const documentHeight = document.documentElement.scrollHeight
  
  // Прогресс скролла от 0 до 1
  scrollProgress.value = Math.min(scrollY / (windowHeight * 0.8), 1)
  parallax.value = scrollY / 1000
}

onMounted(() => {
  window.addEventListener('scroll', handleScroll)
})

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
})

// Состояние формы отчётов
const isReportCollapsed = ref(true)

const toggleReportForm = () => {
  isReportCollapsed.value = !isReportCollapsed.value
}

// Плавный скролл к секции проблем
const scrollToProblems = () => {
  const problemsSection = document.getElementById('problems')
  if (problemsSection) {
    problemsSection.scrollIntoView({ 
      behavior: 'smooth',
      block: 'start'
    })
  }
}

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
  { value: 'low', label: 'Низкая', emoji: '🟢', defaultClass: 'bg-white border-green-200 text-gray-700 hover:bg-green-50', selectedClass: 'bg-green-500 border-green-500 text-white' },
  { value: 'medium', label: 'Средняя', emoji: '🟡', defaultClass: 'bg-white border-yellow-200 text-gray-700 hover:bg-yellow-50', selectedClass: 'bg-yellow-500 border-yellow-500 text-white' },
  { value: 'high', label: 'Высокая', emoji: '🟠', defaultClass: 'bg-white border-orange-200 text-gray-700 hover:bg-orange-50', selectedClass: 'bg-orange-500 border-orange-500 text-white' },
  { value: 'critical', label: 'Критическая', emoji: '🔴', defaultClass: 'bg-white border-red-200 text-gray-700 hover:bg-red-50', selectedClass: 'bg-red-500 border-red-500 text-white' }
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
      isReportCollapsed.value = true
    }, 3000)
  } catch (error) {
    console.error('❌ Ошибка при отправке отчета:', error)
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

// Данные о лесах Петропавловска
const petropavlovskForests = [
  {
    name: "Борковский лес",
    description: "Крупный смешанный лесной массив с богатой флорой и фауной, популярное место отдыха горожан.",
    area: "850 га",
    type: "Смешанный лес",
    image: "https://images.unsplash.com/photo-1448375240586-882707db888b?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1000&q=80"
  },
  {
    name: "Сосновый бор",
    description: "Чистый сосновый лес с целебным воздухом, известный своими хвойными насаждениями.",
    area: "620 га",
    type: "Хвойный лес",
    image: "https://images.unsplash.com/photo-1503435980610-a51f3ddfee50?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1000&q=80"
  },
  {
    name: "Заречный лесопарк",
    description: "Лесопарковая зона вдоль реки с хорошо развитой инфраструктурой для отдыха.",
    area: "450 га",
    type: "Лесопарк",
    image: "https://images.unsplash.com/photo-1473448912268-2022ce9509d8?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1000&q=80"
  }
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
/* Анимации */
@keyframes fade-in {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.animate-fade-in {
  animation: fade-in 0.8s ease-out forwards;
}

/* Transition для формы */
.slide-down-enter-active {
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
}

.slide-down-leave-active {
  transition: all 0.3s cubic-bezier(0.4, 0, 1, 1);
}

.slide-down-enter-from {
  opacity: 0;
  transform: translateY(-10px);
  max-height: 0;
}

.slide-down-leave-to {
  opacity: 0;
  transform: translateY(-10px);
  max-height: 0;
}

.slide-down-enter-to,
.slide-down-leave-from {
  opacity: 1;
  transform: translateY(0);
  max-height: 80vh;
}

/* Плавное появление уведомлений */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

/* Модальное окно */
.modal-enter-active,
.modal-leave-active {
  transition: all 0.3s ease;
}

.modal-enter-from,
.modal-leave-to {
  opacity: 0;
  transform: scale(0.9);
}
</style>