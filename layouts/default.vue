<template>
  <div class="min-h-screen flex flex-col">
    <!-- Фиксированный header ПОЛНОСТЬЮ ПРОЗРАЧНЫЙ И НЕОСЯЗАЕМЫЙ -->
    <header class="fixed top-0 left-0 right-0 z-[10000] pointer-events-none">
      <!-- Только функциональные элементы получают pointer-events -->
      <div class="flex items-center justify-start p-4 gap-4 pointer-events-auto">
        <!-- Кнопка меню -->
        <button 
          @click="toggleMenu"
          class="menu-button group"
          :aria-label="menuOpen ? 'Закрыть меню' : 'Открыть меню'"
          :aria-expanded="menuOpen"
        >
          <svg class="w-6 h-6 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path 
              v-if="!menuOpen"
              stroke-linecap="round" 
              stroke-linejoin="round" 
              stroke-width="2" 
              d="M4 6h16M4 12h16M4 18h16"
            />
            <path 
              v-else
              stroke-linecap="round" 
              stroke-linejoin="round" 
              stroke-width="2" 
              d="M6 18L18 6M6 6l12 12"
            />
          </svg>
        </button>

        <!-- Логотип рядом с бургером -->
        <NuxtLink 
          to="/" 
          class="logo-link-side"
          @click="closeMenu"
        >
          <span class="logo-text">Forest Shield</span>
        </NuxtLink>
      </div>

      <!-- Выпадающее меню -->
      <transition
        enter-active-class="transition-all duration-300 ease-out"
        leave-active-class="transition-all duration-200 ease-in"
        enter-from-class="opacity-0 transform -translate-y-4"
        leave-to-class="opacity-0 transform -translate-y-4"
      >
        <div 
          v-show="menuOpen"
          class="dropdown-menu pointer-events-auto"
        >
          <div class="menu-header">
            <div class="w-8 h-8 bg-green-600 rounded-full flex items-center justify-center">
              <span class="text-white text-sm">🌳</span>
            </div>
            <div>
              <div class="font-semibold text-white">Forest Shield</div>
              <div class="text-xs text-gray-300">Петропавловск</div>
            </div>
          </div>

          <div class="menu-divider"></div>

          <!-- Основные пункты меню -->
          <div class="menu-section">
            <div class="menu-section-title">Основные разделы</div>
            <NuxtLink 
              v-for="item in mainMenuItems" 
              :key="item.path"
              :to="item.path" 
              class="menu-item group"
              @click="closeMenu"
              :class="{ 'active': $route.path === item.path }"
            >
              <div class="menu-item-content">
                <span class="menu-icon">{{ item.icon }}</span>
                <span class="menu-label">{{ item.label }}</span>
              </div>
              <div class="menu-arrow">→</div>
            </NuxtLink>
          </div>

          <div class="menu-divider"></div>

          <!-- Быстрые действия - объединены с основными -->
          <div class="menu-section">
            <NuxtLink 
              v-for="item in quickActionItems" 
              :key="item.path"
              :to="item.path" 
              class="menu-item group"
              @click="closeMenu"
              :class="{ 'active': $route.path === item.path }"
            >
              <div class="menu-item-content">
                <span class="menu-icon">{{ item.icon }}</span>
                <span class="menu-label">{{ item.label }}</span>
              </div>
              <div class="menu-arrow">→</div>
            </NuxtLink>
          </div>

          <div class="menu-divider"></div>

          <!-- Кнопка админ-панели в меню -->
          <div class="px-4 py-3">
            <button 
              v-if="!isAdmin && !isLoading"
              @click="showAdminLogin = true"
              class="w-full flex items-center justify-center gap-2 bg-red-600 hover:bg-red-700 text-white font-semibold py-3 px-4 rounded-lg transition-all duration-300"
            >
              🔐 Админ-панель
            </button>
            
            <button 
              v-else-if="isAdmin"
              @click="logout"
              class="w-full flex items-center justify-center gap-2 bg-gray-600 hover:bg-gray-700 text-white font-semibold py-3 px-4 rounded-lg transition-all duration-300"
            >
              🚪 Выйти
            </button>

            <button 
              v-else
              disabled
              class="w-full flex items-center justify-center gap-2 bg-gray-400 text-white font-semibold py-3 px-4 rounded-lg transition-all duration-300 cursor-not-allowed"
            >
              <div class="w-4 h-4 border-2 border-white/30 border-t-white rounded-full animate-spin"></div>
              Проверка...
            </button>
          </div>
        </div>
      </transition>

      <!-- Overlay для закрытия меню -->
      <div 
        v-show="menuOpen"
        class="menu-overlay pointer-events-auto"
        @click="closeMenu"
      ></div>
    </header>

    <!-- Основной контент БЕЗ отступа сверху -->
    <main class="flex-1">
      <slot />
    </main>

    <!-- Модальное окно авторизации -->
    <div v-if="showAdminLogin" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-[10001] pointer-events-auto">
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
              :disabled="isLoading"
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
              :disabled="isLoading"
            >
          </div>
          
          <div class="flex gap-3 pt-4">
            <button 
              type="submit"
              :disabled="isLoading"
              class="flex-1 bg-red-600 hover:bg-red-700 text-white font-semibold py-3 px-4 rounded-lg transition-all duration-300 disabled:opacity-50 disabled:cursor-not-allowed"
            >
              <span v-if="isLoading">⏳ Вход...</span>
              <span v-else>Войти</span>
            </button>
            <button 
              type="button"
              @click="showAdminLogin = false"
              :disabled="isLoading"
              class="flex-1 bg-gray-500 hover:bg-gray-600 text-white font-semibold py-3 px-4 rounded-lg transition-all duration-300 disabled:opacity-50"
            >
              Отмена
            </button>
          </div>
        </form>
      </div>
    </div>

    <!-- Футер -->
    <footer class="bg-gradient-to-br from-gray-900 via-green-900 to-emerald-900 text-white relative overflow-hidden">
      <div class="absolute inset-0 bg-black/20"></div>
      
      <div class="relative z-10">
        <!-- Верхняя секция -->
        <div class="container mx-auto px-4 py-12">
          <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-8">
            <!-- Бренд -->
            <div class="lg:col-span-2">
              <div class="flex items-center gap-4 mb-6">
                <div class="w-16 h-16 bg-green-600 rounded-2xl flex items-center justify-center text-2xl shadow-lg">
                  🌳
                </div>
                <div>
                  <h3 class="text-3xl font-bold text-white">Forest Shield</h3>
                  <p class="text-green-300 text-sm font-medium">ЭКО-ЗАЩИТА ПЕТРОПАВЛОВСКА</p>
                </div>
              </div>
              <p class="text-green-100 text-lg leading-relaxed mb-6 max-w-md">
                Общественная инициатива по защите и восстановлению зелёных зон города. 
                Мы создаём устойчивое экологическое будущее вместе.
              </p>
              
              <!-- Статистика -->
              <div class="grid grid-cols-3 gap-4 mb-6">
                <div class="text-center p-4 bg-green-800/30 rounded-xl border border-green-700/50">
                  <div class="text-2xl font-bold text-white mb-1">1,250+</div>
                  <div class="text-xs text-green-300">Деревьев посажено</div>
                </div>
                <div class="text-center p-4 bg-green-800/30 rounded-xl border border-green-700/50">
                  <div class="text-2xl font-bold text-white mb-1">24</div>
                  <div class="text-xs text-green-300">Проектов</div>
                </div>
                <div class="text-center p-4 bg-green-800/30 rounded-xl border border-green-700/50">
                  <div class="text-2xl font-bold text-white mb-1">150+</div>
                  <div class="text-xs text-green-300">Волонтёров</div>
                </div>
              </div>
            </div>

            <!-- Навигация -->
            <div>
              <h4 class="text-white font-bold text-lg mb-6 pb-2 border-b border-green-700/50">Навигация</h4>
              <nav class="space-y-3">
                <NuxtLink 
                  v-for="link in navLinks"
                  :key="link.path"
                  :to="link.path"
                  class="flex items-center gap-3 text-green-200 hover:text-white transition-colors group"
                >
                  <span class="text-lg">{{ link.icon }}</span>
                  <span>{{ link.label }}</span>
                </NuxtLink>
              </nav>
            </div>

            <!-- Контакты -->
            <div>
              <h4 class="text-white font-bold text-lg mb-6 pb-2 border-b border-green-700/50">Контакты</h4>
              <div class="space-y-4">
                <div class="flex items-center gap-3">
                  <Mail class="w-5 h-5 text-green-400" />
                  <div>
                    <div class="text-green-300 text-sm">Email</div>
                    <div class="text-white text-sm">alan27945@gmail.com</div>
                    <div class="text-white text-sm">km9294949@gmail.com</div>
                  </div>
                </div>
                <div class="flex items-center gap-3">
                  <Phone class="w-5 h-5 text-green-400" />
                  <div>
                    <div class="text-green-300 text-sm">Телефон</div>
                    <div class="text-white">+7 707 913 4080</div>
                  </div>
                </div>
                <div class="flex items-center gap-3">
                  <MapPin class="w-5 h-5 text-green-400" />
                  <div>
                    <div class="text-green-300 text-sm">Адрес</div>
                    <div class="text-white">г. Петропавловск, Казахстан</div>
                  </div>
                </div>
              </div>

              <!-- Соцсети -->
              <div class="mt-6">
                <h5 class="text-white font-semibold mb-4">Мы в соцсетях</h5>
                <div class="flex gap-3">
                  <a 
                    v-for="social in socialLinks"
                    :key="social.name"
                    :href="social.url"
                    class="w-12 h-12 bg-green-800/30 rounded-xl flex items-center justify-center hover:bg-green-700 transition-colors border border-green-700/50"
                    :aria-label="social.name"
                  >
                    <component :is="social.icon" class="w-5 h-5 text-green-400" />
                  </a>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Нижняя часть -->
        <div class="border-t border-green-800/50">
          <div class="container mx-auto px-4 py-8">
            <div class="flex flex-col md:flex-row justify-between items-center gap-4">
              <!-- Копирайт -->
              <div class="text-center md:text-left">
                <p class="text-green-300">
                  © {{ currentYear }} Forest Shield. С любовью к природе ❤️
                </p>
              </div>

              <!-- Дополнительные ссылки -->
              <div class="flex flex-wrap justify-center gap-4">
                <button 
                  v-for="link in legalLinks"
                  :key="link.path"
                  @click="$router.push(link.path)"
                  class="text-green-400 hover:text-white transition-colors text-sm"
                >
                  {{ link.label }}
                </button>
              </div>

              <!-- Кнопка наверх -->
              <button 
                @click="scrollToTop"
                class="w-12 h-12 bg-green-600 rounded-xl flex items-center justify-center hover:bg-green-500 transition-colors text-white"
                aria-label="Вернуться наверх"
              >
                <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 15l7-7 7 7"/>
                </svg>
              </button>
            </div>
          </div>
        </div>
      </div>
    </footer>
  </div>
</template>

<script setup lang="ts">
import { Mail, Phone, MapPin, Github, Monitor } from 'lucide-vue-next'

// Состояние
const isAdmin = ref(false)
const isLoading = ref(false)
const adminUser = ref(null)
const menuOpen = ref(false)
const route = useRoute()
const showAdminLogin = ref(false)

// Данные для формы входа
const adminCredentials = ref({
  login: '',
  password: ''
})

// Текущий год
const currentYear = ref(new Date().getFullYear())

// Основные элементы меню
const mainMenuItems = [
  { path: '/', label: 'Главная', icon: '🏠' },
  { path: '/dropdownbar/map', label: 'Карта', icon: '🗺️' },
]

// Элементы быстрого доступа
const quickActionItems = [
  { path: '/reports', label: 'Отчёты', icon: '📊' },
  { path: '/suggestions', label: 'Предложения', icon: '💡' },
]

// Навигационные ссылки
const navLinks = [
  { path: '/', label: 'Главная', icon: '🏠' },
  { path: '/dropdownbar/map', label: 'Карта', icon: '🗺️' },
  { path: '/reports', label: 'Отчёты', icon: '📊' },
  { path: '/suggestions', label: 'Предложения', icon: '💡' },
  { path: '/about', label: 'О нас', icon: '👥' },
  { path: '/footer/volunteer', label: 'Волонтёрство', icon: '🌱' },
]

// Социальные сети
const socialLinks = [
  { name: 'Netlify', url: 'https://aim-urbathon.netlify.app/', icon: Monitor },
  { name: 'GitHub Frontend', url: 'https://github.com/emngr256/forestproject', icon: Github },
  { name: 'GitHub Backend', url: 'https://github.com/mansur2286969sgma/urb-back', icon: Github },
]

// Юридические ссылки
const legalLinks = [
  { path: '/policy', label: 'Политика конфиденциальности' },
  { path: '/footer/conditions', label: 'Условия использования' },
  { path: '/donate', label: 'Поддержать проект' }
]

// Функции
const toggleMenu = () => {
  menuOpen.value = !menuOpen.value
}

const closeMenu = () => {
  menuOpen.value = false
}

const scrollToTop = () => {
  window.scrollTo({
    top: 0,
    behavior: 'smooth'
  })
}

// Функции авторизации
const adminLogin = async (credentials: { login: string; password: string }) => {
  try {
    isLoading.value = true
    
    if (credentials.login === 'admin' && credentials.password === 'admin123') {
      isAdmin.value = true
      adminUser.value = {
        id: 1,
        login: 'admin',
        role: 'admin',
        name: 'Администратор'
      }
      
      if (process.client) {
        localStorage.setItem('isAdmin', 'true')
        localStorage.setItem('adminUser', JSON.stringify(adminUser.value))
      }
      
      return { success: true }
    } else {
      return { success: false, error: 'Неверный логин или пароль' }
    }
  } catch (error: any) {
    console.error('Ошибка входа:', error)
    return { 
      success: false, 
      error: error.data?.error || 'Ошибка соединения с сервером' 
    }
  } finally {
    isLoading.value = false
  }
}

const adminLogout = () => {
  isAdmin.value = false
  adminUser.value = null
  
  if (process.client) {
    localStorage.removeItem('isAdmin')
    localStorage.removeItem('adminUser')
  }
}

const checkAdminAuth = () => {
  if (process.client) {
    const savedAdmin = localStorage.getItem('isAdmin')
    if (savedAdmin === 'true') {
      isAdmin.value = true
      const savedUser = localStorage.getItem('adminUser')
      if (savedUser) {
        adminUser.value = JSON.parse(savedUser)
      }
    }
  }
}

const login = async () => {
  try {
    const result = await adminLogin(adminCredentials.value)
    
    if (result.success) {
      showAdminLogin.value = false
      adminCredentials.value = { login: '', password: '' }
      alert('✅ Успешный вход в админ-панель!')
    } else {
      alert(`❌ ${result.error}`)
    }
  } catch (error) {
    console.error('Ошибка авторизации:', error)
    alert('❌ Ошибка при авторизации')
  }
}

const logout = () => {
  adminLogout()
  alert('👋 Вы вышли из системы')
}

// Проверяем авторизацию при загрузке
onMounted(() => {
  checkAdminAuth()
})

// Закрытие меню при изменении маршрута
watch(() => route.path, () => {
  closeMenu()
})
</script>

<style scoped>
/* Базовые стили */
html, body {
  margin: 0;
  padding: 0;
  overflow-x: hidden;
}

/* Хедер полностью неосязаемый */
header {
  background: transparent !important;
}

/* Кнопка меню - единственный осязаемый элемент */
.menu-button {
  padding: 12px;
  background-color: rgba(0, 0, 0, 0.3);
  backdrop-filter: blur(8px);
  border-radius: 9999px;
  border: 1px solid rgba(255, 255, 255, 0.2);
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
}

.menu-button:hover {
  background-color: rgba(0, 0, 0, 0.5);
}

.menu-button:active {
  transform: scale(0.95);
}

/* Выпадающее меню */
.dropdown-menu {
  position: absolute;
  top: 72px;
  left: 16px;
  background-color: rgba(0, 0, 0, 0.95);
  backdrop-filter: blur(20px);
  color: white;
  border-radius: 16px;
  box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
  padding: 12px 0;
  min-width: 280px;
  z-index: 10001;
  border: 1px solid rgba(255, 255, 255, 0.2);
  overflow: hidden;
}

/* Заголовок меню */
.menu-header {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 12px 16px;
}

.menu-divider {
  height: 1px;
  background-color: rgba(255, 255, 255, 0.1);
  margin: 8px 16px;
}

/* Секции меню */
.menu-section {
  padding: 0 8px;
}

.menu-section-title {
  font-size: 11px;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  color: rgba(255, 255, 255, 0.5);
  padding: 8px 16px 4px;
  margin-bottom: 4px;
}

/* Элемент меню */
.menu-item {
  display: block;
  margin: 4px 8px;
  padding: 12px;
  border-radius: 8px;
  transition: all 0.2s ease;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.menu-item:hover {
  background-color: rgba(255, 255, 255, 0.1);
}

.menu-item:active {
  background-color: rgba(255, 255, 255, 0.15);
}

.menu-item.active {
  background-color: rgba(34, 197, 94, 0.3);
  border: 1px solid rgba(34, 197, 94, 0.3);
}

.menu-item-content {
  display: flex;
  align-items: center;
  gap: 12px;
}

.menu-icon {
  font-size: 16px;
  width: 20px;
  text-align: center;
}

.menu-label {
  font-weight: 500;
}

.menu-arrow {
  opacity: 0;
  transform: translateX(-4px);
  transition: all 0.2s ease;
  color: rgb(74, 222, 128);
}

.menu-item:hover .menu-arrow {
  opacity: 1;
  transform: translateX(0);
}

/* Overlay */
.menu-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.3);
  backdrop-filter: blur(4px);
  z-index: 10000;
}

/* Логотип рядом с бургером */
.logo-link-side {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 16px;
  background-color: rgba(0, 0, 0, 0.3);
  backdrop-filter: blur(8px);
  border-radius: 9999px;
  border: 1px solid rgba(255, 255, 255, 0.2);
  transition: all 0.3s ease;
}

.logo-link-side:hover {
  background-color: rgba(0, 0, 0, 0.5);
}

.logo-text {
  color: white;
  font-weight: 600;
  font-size: 18px;
  letter-spacing: 0.025em;
}

/* Адаптивность */
@media (max-width: 640px) {
  .dropdown-menu {
    left: 8px;
    right: 8px;
  }
  
  .logo-link-side {
    display: flex;
  }
}
</style>