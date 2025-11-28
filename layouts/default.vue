<template>
  <div class="min-h-screen">
    <!-- Фиксированный header -->
    <header class="fixed top-0 left-0 right-0 z-[9999]">
      <!-- Кнопка меню и логотип -->
      <div class="flex items-center justify-start p-4 gap-4">
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
          class="dropdown-menu"
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

          <NuxtLink 
            v-for="item in menuItems" 
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

          <div class="menu-divider"></div>

          <!-- Быстрые действия в меню -->
          <div class="px-4 py-2">
            <div class="text-xs text-gray-400 mb-2">Быстрые действия</div>
            <div class="grid grid-cols-2 gap-2">
              <NuxtLink 
                to="/reports" 
                class="quick-action-menu-btn"
                @click="closeMenu"
              >
                📊 Отчёты
              </NuxtLink>
              <NuxtLink 
                to="/suggestions" 
                class="quick-action-menu-btn"
                @click="closeMenu"
              >
                💡 Предложения
              </NuxtLink>
            </div>
          </div>

          <!-- Контактная информация в меню -->
          <div class="px-4 py-3 bg-black/30 mt-2">
            <div class="text-xs text-gray-400 mb-1">Контакты</div>
            <div class="text-sm text-white">+7 707 913 4080</div>
            <div class="text-xs text-gray-300 truncate">alan27945@gmail.com km9294949@gmail.com </div>
          </div>
        </div>
      </transition>

      <!-- Overlay для закрытия меню -->
      <div 
        v-show="menuOpen"
        class="menu-overlay"
        @click="closeMenu"
      ></div>
    </header>

    <!-- Основной контент -->
    <main class="min-h-screen">
      <slot />
    </main>

    <!-- Footer -->
    <footer id="contact" class="bg-gray-900 text-gray-300 py-12">
      <div class="container mx-auto px-4">
        <div class="grid grid-cols-1 md:grid-cols-3 gap-8 mb-8">
          <!-- About Section -->
          <div>
            <div class="flex items-center gap-2 mb-4">
              <div class="w-10 h-10 bg-green-600 rounded-full flex items-center justify-center">
                <span class="text-white">🌳</span>
              </div>
              <div>
                <div class="font-semibold text-white">Forest Shield</div>
                <div class="text-xs">Петропавловск</div>
              </div>
            </div>
            <p class="text-sm">
              Общественная инициатива по защите и восстановлению лесов города Петропавловска.
            </p>
          </div>

          <!-- Contacts Section -->
          <div>
            <h4 class="text-white mb-4">Контакты</h4>
            <div class="space-y-3">
              <div class="flex items-center gap-2">
                <Mail :size="16" />
                <span class="text-sm">alan27945@gmail.com</span>
              </div>
              <div class="flex items-center gap-2">
                <Phone :size="16" />
                <span class="text-sm">+7 707 913 4080</span>
              </div>
              <div class="flex items-center gap-2">
                <MapPin :size="16" />
                <span class="text-sm">г. Петропавловск, Казахстан</span>
              </div>
            </div>
          </div>

          <!-- Social Media Section -->
          <div>
            <h4 class="text-white mb-4">Следите за нами</h4>
            <div class="flex gap-4">
              <a 
                href="https://aim-urbathon.netlify.app/" 
                class="w-10 h-10 bg-gray-800 rounded-full flex items-center justify-center hover:bg-green-600 transition-colors"
                aria-label="Netlify"
              >
                <Monitor :size="20" />
              </a>
              <a 
                href="https://github.com/emngr256/forestproject" 
                class="w-10 h-10 bg-gray-800 rounded-full flex items-center justify-center hover:bg-green-600 transition-colors"
                aria-label="Github"
              >
                <Github :size="20" />
              </a>
              <a 
                href="https://youtube.com/" 
                class="w-10 h-10 bg-gray-800 rounded-full flex items-center justify-center hover:bg-green-600 transition-colors"
                aria-label="Youtube"
              >
                <Youtube :size="20" />
              </a>
            </div>
            <div class="mt-6">
              <p class="text-sm text-gray-400">
                Присоединяйтесь к нашему сообществу и получайте новости о природоохранных мероприятиях.
              </p>
            </div>
          </div>
        </div>

        <!-- Copyright Section -->
        <div class="border-t border-gray-800 pt-8">
          <div class="flex flex-col md:flex-row justify-between items-center gap-4">
            <p class="text-sm text-gray-400">
              © {{ currentYear }} Все права защищены.
            </p>
            <div class="flex gap-6 text-sm">
              <button @click="$router.push('/policy')" class="hover:text-white transition-colors px-4 py-2 hover:bg-green-500 rounded">Политика конфиденциальности</button>
              <button @click="$router.push('/conditions')" class="hover:text-white transition-colors px-4 py-2 hover:bg-green-500 rounded">Условия использования</button>
            </div>
          </div>
        </div>
      </div>
    </footer>
  </div>
</template>

<script setup lang="ts">
import { Mail, Phone, MapPin, Youtube, Github, Monitor } from 'lucide-vue-next'

// Состояние меню
const menuOpen = ref(false)
const route = useRoute()

// Текущий год
const currentYear = ref(new Date().getFullYear())

// Элементы меню
const menuItems = [
  { path: '/', label: 'Главная', icon: '🏠' },
  { path: '/dropdownbar/map', label: 'Карта', icon: '🗺️' },
  { path: '/suggestions', label: 'Предложения', icon: '💡' },
]

// Функции управления меню
const toggleMenu = () => {
  menuOpen.value = !menuOpen.value
}

const closeMenu = () => {
  menuOpen.value = false
}

// Закрытие меню при изменении маршрута
watch(() => route.path, () => {
  closeMenu()
})

// Закрытие меню по Escape и клику вне меню
onMounted(() => {
  const handleEscape = (e: KeyboardEvent) => {
    if (e.key === 'Escape' && menuOpen.value) {
      closeMenu()
    }
  }
  
  window.addEventListener('keydown', handleEscape)
  
  onUnmounted(() => {
    window.removeEventListener('keydown', handleEscape)
  })
})
</script>

<style scoped>
/* Базовые стили */
html, body {
  margin: 0;
  padding: 0;
  overflow-x: hidden;
}

/* Кнопка меню - как в старом варианте */
.menu-button {
  padding: 12px;
  background-color: rgba(0, 0, 0, 0.3);
  backdrop-filter: blur(8px);
  border-radius: 9999px;
  box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
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

/* Выпадающее меню - поверх всего сайта */
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
  z-index: 10000;
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

/* Элемент меню - улучшенная версия */
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

/* Кнопки быстрого действия в меню */
.quick-action-menu-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 8px 12px;
  background-color: rgba(255, 255, 255, 0.1);
  color: white;
  border-radius: 8px;
  font-size: 12px;
  font-weight: 500;
  transition: all 0.2s ease;
  border: 1px solid rgba(255, 255, 255, 0.1);
  text-align: center;
}

.quick-action-menu-btn:hover {
  background-color: rgba(255, 255, 255, 0.2);
}

/* Overlay - поверх всего */
.menu-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.3);
  backdrop-filter: blur(4px);
  z-index: 9999;
}

/* Логотип рядом с бургером - как в старом варианте */
.logo-link-side {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 16px;
  background-color: rgba(0, 0, 0, 0.3);
  backdrop-filter: blur(8px);
  border-radius: 9999px;
  border: 1px solid rgba(255, 255, 255, 0.2);
  box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
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

/* Footer heading */
h4 {
  font-size: 18px;
  font-weight: 600;
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