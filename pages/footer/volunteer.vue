<template>
  <div class="min-h-screen bg-white">
    <!-- Hero Section -->
    <section class="relative min-h-screen flex items-center justify-center overflow-hidden">
      <!-- Фон с градиентом -->
      <div class="absolute inset-0 z-0">
        <div class="absolute inset-0 bg-gradient-to-b from-green-900/90 via-green-800/70 to-gray-50 z-10"></div>
        <img 
          src="https://images.unsplash.com/photo-1542601906990-b4d3fb778b09?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1000&q=80"
          alt="Волонтёры сажают деревья" 
          class="w-full h-full object-cover"
        >
      </div>

      <!-- Контент -->
      <div class="relative z-10 w-full max-w-6xl mx-auto px-4 sm:px-6 h-full flex items-center justify-center">
        <div class="text-center text-white w-full max-w-4xl">
          <!-- Badge -->
          <div class="inline-flex items-center gap-3 bg-green-600/90 backdrop-blur-sm text-white px-6 py-3 rounded-full mb-8 animate-fade-in">
            <div class="w-3 h-3 bg-green-300 rounded-full animate-pulse"></div>
            <span class="font-medium">Присоединяйтесь к команде волонтёров</span>
          </div>

          <!-- Заголовок -->
          <h1 class="mb-6 leading-tight">
            <span class="block text-5xl md:text-6xl lg:text-7xl font-bold mb-4">Стань волонтёром</span>
            <span class="block text-4xl md:text-5xl lg:text-6xl font-bold text-green-300">лесов Петропавловска</span>
          </h1>

          <!-- Описание -->
          <p class="text-xl md:text-2xl text-gray-200 mb-12 leading-relaxed max-w-4xl mx-auto">
            Вместе мы можем изменить будущее наших лесов. Присоединяйтесь к сообществу единомышленников 
            и участвуйте в реальных экологических проектах!
          </p>

          <!-- Кнопки действий -->
          <div class="flex flex-col sm:flex-row gap-4 justify-center items-center">
            <button 
              @click="scrollToEvents"
              class="group bg-green-600 hover:bg-green-700 text-white font-semibold py-4 px-8 rounded-xl text-lg transition-all duration-300 transform hover:scale-105 shadow-lg hover:shadow-xl flex items-center gap-3"
            >
              <span class="text-xl">📅</span>
              Смотреть мероприятия
              <span class="text-xl">→</span>
            </button>
            <button 
              @click="scrollToRegistration"
              class="group bg-white/20 hover:bg-white/30 backdrop-blur-sm text-white font-semibold py-4 px-8 rounded-xl text-lg transition-all duration-300 transform hover:scale-105 border border-white/30 flex items-center gap-3"
            >
              <span class="text-xl">👥</span>
              Стать волонтёром
            </button>
          </div>
        </div>
      </div>
    </section>

    <!-- Простой контент для теста -->
    <section class="py-20 bg-white">
      <div class="container mx-auto px-4 text-center">
        <h2 class="text-4xl font-bold mb-8">Волонтёрская программа</h2>
        <p class="text-xl text-gray-600">Присоединяйтесь к нашей команде!</p>
      </div>
    </section>
  </div>
</template>

<script setup>
// Простой скрипт без импортов
const scrollToEvents = () => {
  console.log('Scroll to events')
}

const scrollToRegistration = () => {
  console.log('Scroll to registration')
}
</script>

<style scoped>
/* Базовые стили */
.animate-fade-in {
  animation: fadeIn 0.8s ease-out;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(20px); }
  to { opacity: 1; transform: translateY(0); }
}
</style>