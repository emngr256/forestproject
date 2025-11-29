<template>
  <div class="min-h-screen bg-white">
    <!-- Hero Section -->
    <section class="relative py-12 bg-gradient-to-br from-green-900 to-blue-900">
      <div class="container mx-auto px-4 sm:px-6">
        <div class="text-center text-white mb-8">
          <div class="inline-flex items-center gap-3 bg-green-600/90 backdrop-blur-sm text-white px-6 py-3 rounded-full mb-6">
            <div class="w-3 h-3 bg-green-300 rounded-full animate-pulse"></div>
            <span class="font-medium">Интерактивная карта лесов</span>
          </div>
          <h1 class="text-4xl md:text-5xl font-bold mb-4">Карта лесных массивов</h1>
          <p class="text-xl text-gray-200 max-w-2xl mx-auto">
            Изучайте лесные массивы Петропавловска, отслеживайте их состояние и получайте аналитику
          </p>
        </div>
      </div>
    </section>

    <!-- Main Content -->
    <section class="py-8 bg-gray-50">
      <div class="container mx-auto px-4 sm:px-6">
        <div class="grid grid-cols-1 lg:grid-cols-4 gap-8">
          <!-- Sidebar -->
          <div class="lg:col-span-1 space-y-6">
            <!-- Статистика -->
            <div class="bg-white rounded-2xl shadow-lg p-6 border border-gray-200">
              <h3 class="text-xl font-bold text-gray-800 mb-4 flex items-center gap-2">
                <svg class="w-5 h-5 text-green-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z"/>
                </svg>
                Общая статистика
              </h3>
              <div class="space-y-4">
                <div class="flex justify-between items-center p-3 bg-green-50 rounded-lg">
                  <span class="text-sm font-medium text-gray-700">Всего лесов</span>
                  <span class="font-bold text-green-600">{{ forests.length }}</span>
                </div>
                <div class="flex justify-between items-center p-3 bg-blue-50 rounded-lg">
                  <span class="text-sm font-medium text-gray-700">Общая площадь</span>
                  <span class="font-bold text-blue-600">{{ totalArea }} га</span>
                </div>
                <div class="flex justify-between items-center p-3 bg-purple-50 rounded-lg">
                  <span class="text-sm font-medium text-gray-700">Поглощено CO₂</span>
                  <span class="font-bold text-purple-600">{{ (totalCarbon / 1000).toFixed(0) }} тыс.т</span>
                </div>
                <div class="flex justify-between items-center p-3 bg-orange-50 rounded-lg">
                  <span class="text-sm font-medium text-gray-700">Среднее здоровье</span>
                  <span class="font-bold text-orange-600">{{ avgHealth }}%</span>
                </div>
              </div>
            </div>

            <!-- Фильтры -->
            <div class="bg-white rounded-2xl shadow-lg p-6 border border-gray-200">
              <h3 class="text-xl font-bold text-gray-800 mb-4 flex items-center gap-2">
                <svg class="w-5 h-5 text-blue-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 4a1 1 0 011-1h16a1 1 0 011 1v2.586a1 1 0 01-.293.707l-6.414 6.414a1 1 0 00-.293.707V17l-4 4v-6.586a1 1 0 00-.293-.707L3.293 7.293A1 1 0 013 6.586V4z"/>
                </svg>
                Фильтры
              </h3>
              <div class="space-y-4">
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Тип леса</label>
                  <select 
                    v-model="filters.type"
                    class="w-full px-3 py-2 rounded-lg border border-gray-300 focus:border-blue-500 focus:ring-2 focus:ring-blue-500/30 transition-all duration-200"
                  >
                    <option value="all">Все типы</option>
                    <option value="pine">Сосновый</option>
                    <option value="birch">Березовый</option>
                    <option value="mixed">Смешанный</option>
                  </select>
                </div>
                
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">
                    Здоровье: от {{ filters.health }}%
                  </label>
                  <input 
                    v-model="filters.health"
                    type="range" 
                    min="0" 
                    max="100" 
                    class="w-full h-2 bg-gray-200 rounded-lg appearance-none cursor-pointer slider"
                  >
                </div>

                <div class="grid grid-cols-2 gap-3">
                  <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">Площадь от</label>
                    <input 
                      v-model.number="filters.areaMin"
                      type="number" 
                      placeholder="0"
                      class="w-full px-3 py-2 rounded-lg border border-gray-300 focus:border-blue-500 focus:ring-2 focus:ring-blue-500/30 transition-all duration-200"
                    >
                  </div>
                  <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">Площадь до</label>
                    <input 
                      v-model.number="filters.areaMax"
                      type="number" 
                      placeholder="1000"
                      class="w-full px-3 py-2 rounded-lg border border-gray-300 focus:border-blue-500 focus:ring-2 focus:ring-blue-500/30 transition-all duration-200"
                    >
                  </div>
                </div>

                <button 
                  @click="resetFilters"
                  class="w-full bg-gray-100 hover:bg-gray-200 text-gray-700 font-medium py-2 px-4 rounded-lg transition-all duration-200 flex items-center justify-center gap-2"
                >
                  <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"/>
                  </svg>
                  Сбросить фильтры
                </button>
              </div>
            </div>

            <!-- Быстрые действия -->
            <div class="bg-white rounded-2xl shadow-lg p-6 border border-gray-200">
              <h3 class="text-xl font-bold text-gray-800 mb-4">Быстрые действия</h3>
              <div class="space-y-3">
                <button 
                  @click="resetView"
                  class="w-full bg-blue-600 hover:bg-blue-700 text-white font-medium py-3 px-4 rounded-lg transition-all duration-200 flex items-center justify-center gap-2"
                >
                  <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"/>
                  </svg>
                  Сбросить вид карты
                </button>
                <button 
                  @click="showAllForests"
                  class="w-full bg-green-600 hover:bg-green-700 text-white font-medium py-3 px-4 rounded-lg transition-all duration-200 flex items-center justify-center gap-2"
                >
                  <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m5.618-4.016A11.955 11.955 0 0112 2.944a11.955 11.955 0 01-8.618 3.04A12.02 12.02 0 003 9c0 5.591 3.824 10.29 9 11.622 5.176-1.332 9-6.03 9-11.622 0-1.042-.133-2.052-.382-3.016z"/>
                  </svg>
                  Показать все леса
                </button>
              </div>
            </div>
          </div>

          <!-- Карта и информация -->
          <div class="lg:col-span-3">
            <div class="bg-white rounded-2xl shadow-lg overflow-hidden border border-gray-200 mb-6">
              <!-- Заголовок карты -->
              <div class="px-6 py-4 border-b border-gray-200 flex justify-between items-center">
                <h2 class="text-2xl font-bold text-gray-800">Интерактивная карта</h2>
                <div class="flex items-center gap-3">
                  <!-- Переключатель стилей карты -->
                  <div class="flex bg-gray-100 rounded-lg p-1">
                    <button 
                      v-for="style in mapStyles" 
                      :key="style.id"
                      @click="mapStyle = style.id"
                      :class="[
                        'px-3 py-1 rounded-md text-sm font-medium transition-all duration-200',
                        mapStyle === style.id 
                          ? 'bg-white text-blue-600 shadow-sm' 
                          : 'text-gray-600 hover:text-gray-800'
                      ]"
                      :title="style.name"
                    >
                      {{ style.shortName }}
                    </button>
                  </div>
                </div>
              </div>

              <!-- Контейнер карты -->
              <div class="relative h-96 lg:h-[500px]">
                <LMap 
                  ref="map"
                  :zoom="zoom" 
                  :center="center" 
                  :use-global-leaflet="false"
                  class="h-full w-full rounded-b-2xl"
                  :options="{
                    zoomControl: false,
                    attributionControl: false,
                  }"
                  @click="clearSelection"
                  @moveend="onMapMove"
                  @zoomend="onMapZoom"
                >
                  <!-- Основной слой OpenStreetMap -->
                  <LTileLayer
                    v-if="mapStyle === 'standard'"
                    url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
                    layer-type="base"
                    name="OpenStreetMap"
                  />
                  
                  <!-- Альтернативные слои карты -->
                  <LTileLayer
                    v-if="mapStyle === 'satellite'"
                    url="https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}"
                    layer-type="base"
                    name="Спутник"
                  />
                  
                  <LTileLayer
                    v-if="mapStyle === 'topo'"
                    url="https://{s}.tile.opentopomap.org/{z}/{x}/{y}.png"
                    layer-type="base"
                    name="Топографическая"
                  />

                  <!-- Контуры лесов -->
                  <LPolygon
                    v-for="forest in filteredForests"
                    :key="`polygon-${forest.id}`"
                    :lat-lngs="forest.polygon.coordinates[0]"
                    :options="getForestStyle(forest)"
                    @click="selectForest(forest)"
                    @mouseover="hoverForest = forest"
                    @mouseout="hoverForest = null"
                  />

                  <!-- Подсветка при наведении -->
                  <LPolygon
                    v-if="hoverForest && hoverForest.id !== selectedForest?.id"
                    :lat-lngs="hoverForest.polygon.coordinates[0]"
                    :options="getHoverStyle(hoverForest)"
                  />

                  <!-- Выделение выбранного леса -->
                  <LPolygon
                    v-if="selectedForest"
                    :lat-lngs="selectedForest.polygon.coordinates[0]"
                    :options="getSelectedStyle(selectedForest)"
                  />

                  <!-- Маркеры для важных точек -->
                  <LMarker
                    v-for="point in importantPoints"
                    :key="`marker-${point.id}`"
                    :lat-lng="point.position"
                    :icon="getMarkerIcon(point.type)"
                  >
                    <LTooltip :options="{ permanent: false, direction: 'top' }">
                      <div class="font-semibold">{{ point.name }}</div>
                      <div class="text-xs text-gray-600">{{ point.description }}</div>
                    </LTooltip>
                  </LMarker>

                  <!-- Легенда карты -->
                  <LControl position="bottomright" class="custom-control">
                    <div class="bg-white rounded-lg shadow-xl p-4 min-w-48 border border-gray-200">
                      <h3 class="font-bold text-gray-800 mb-3 text-sm flex items-center">
                        <svg class="w-4 h-4 mr-2 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"/>
                        </svg>
                        Легенда карты
                      </h3>
                      <div class="space-y-2">
                        <div class="flex items-center justify-between group">
                          <div class="flex items-center">
                            <div class="w-4 h-4 bg-green-600 rounded mr-2 opacity-30 border-2 border-green-600 group-hover:opacity-50 transition-opacity"></div>
                            <span class="text-sm text-gray-700">Сосновый лес</span>
                          </div>
                          <span class="text-xs text-gray-500 bg-green-50 px-2 py-1 rounded">{{ pineCount }}</span>
                        </div>
                        <div class="flex items-center justify-between group">
                          <div class="flex items-center">
                            <div class="w-4 h-4 bg-yellow-600 rounded mr-2 opacity-30 border-2 border-yellow-600 group-hover:opacity-50 transition-opacity"></div>
                            <span class="text-sm text-gray-700">Березовый лес</span>
                          </div>
                          <span class="text-xs text-gray-500 bg-yellow-50 px-2 py-1 rounded">{{ birchCount }}</span>
                        </div>
                        <div class="flex items-center justify-between group">
                          <div class="flex items-center">
                            <div class="w-4 h-4 bg-blue-600 rounded mr-2 opacity-30 border-2 border-blue-600 group-hover:opacity-50 transition-opacity"></div>
                            <span class="text-sm text-gray-700">Смешанный лес</span>
                          </div>
                          <span class="text-xs text-gray-500 bg-blue-50 px-2 py-1 rounded">{{ mixedCount }}</span>
                        </div>
                      </div>
                    </div>
                  </LControl>

                  <!-- Контролы масштабирования -->
                  <LControlZoom position="topleft" />

                  <!-- Масштабная линейка -->
                  <LControlScale 
                    position="bottomleft" 
                    :imperial="false" 
                    :metric="true"
                  />
                </LMap>

                <!-- Индикатор загрузки -->
                <Transition name="fade">
                  <div 
                    v-if="isLoading"
                    class="absolute inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 rounded-b-2xl"
                  >
                    <div class="bg-white rounded-lg p-6 shadow-xl">
                      <div class="flex items-center space-x-3">
                        <div class="animate-spin rounded-full h-8 w-8 border-b-2 border-blue-600"></div>
                        <span class="text-gray-700 font-semibold">Загрузка карты...</span>
                      </div>
                    </div>
                  </div>
                </Transition>
              </div>
            </div>

            <!-- Информация о выбранном лесе -->
            <Transition name="panel">
              <div 
                v-if="selectedForest"
                class="bg-white rounded-2xl shadow-lg p-6 border border-gray-200"
              >
                <div class="flex justify-between items-start mb-6">
                  <div>
                    <h3 class="text-2xl font-bold text-gray-800">{{ selectedForest.name }}</h3>
                    <div class="flex items-center mt-2">
                      <div 
                        class="w-3 h-3 rounded-full mr-2 border-2"
                        :class="getTypeColorClass(selectedForest.type)"
                      ></div>
                      <span class="text-sm text-gray-600 capitalize">
                        {{ getTypeName(selectedForest.type) }}
                      </span>
                    </div>
                  </div>
                  <button 
                    @click="clearSelection"
                    class="text-gray-400 hover:text-gray-600 transition-colors p-1 hover:bg-gray-100 rounded-lg"
                  >
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
                    </svg>
                  </button>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-6">
                  <div class="bg-blue-50 p-4 rounded-xl border border-blue-200">
                    <div class="text-xs text-blue-600 font-semibold mb-1">ПЛОЩАДЬ</div>
                    <div class="font-bold text-gray-800 text-xl">{{ selectedForest.area }} га</div>
                  </div>
                  <div class="bg-green-50 p-4 rounded-xl border border-green-200">
                    <div class="text-xs text-green-600 font-semibold mb-1">ВОЗРАСТ</div>
                    <div class="font-bold text-gray-800 text-xl">{{ selectedForest.age }} лет</div>
                  </div>
                  <div class="bg-purple-50 p-4 rounded-xl border border-purple-200">
                    <div class="text-xs text-purple-600 font-semibold mb-1">БИОРАЗНООБРАЗИЕ</div>
                    <div class="font-bold text-gray-800 text-xl">{{ selectedForest.biodiversity }}%</div>
                  </div>
                  <div class="bg-orange-50 p-4 rounded-xl border border-orange-200">
                    <div class="text-xs text-orange-600 font-semibold mb-1">ЦЕННОСТЬ</div>
                    <div class="font-bold text-gray-800 text-xl">{{ calculateValueIndex(selectedForest) }}/100</div>
                  </div>
                </div>

                <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                  <!-- Здоровье -->
                  <div class="bg-white p-4 rounded-xl border border-gray-200">
                    <div class="flex justify-between items-center mb-3">
                      <span class="text-sm font-semibold text-gray-700">Состояние здоровья</span>
                      <span class="text-sm font-bold" :class="getHealthTextColor(selectedForest.health)">
                        {{ selectedForest.health }}%
                      </span>
                    </div>
                    <div class="w-full h-3 bg-gray-200 rounded-full overflow-hidden">
                      <div 
                        class="h-full rounded-full transition-all duration-1000"
                        :class="getHealthColor(selectedForest.health)"
                        :style="{ width: selectedForest.health + '%' }"
                      ></div>
                    </div>
                  </div>

                  <!-- Поглощение углерода -->
                  <div class="bg-gradient-to-r from-green-500 to-emerald-600 p-4 rounded-xl text-white">
                    <div class="text-xs font-semibold text-green-100 mb-1">ПОГЛОЩЕНО УГЛЕРОДА</div>
                    <div class="font-bold text-2xl">{{ selectedForest.carbon.toLocaleString() }} т</div>
                    <div class="text-green-100 text-xs mt-1">Эквивалент CO₂</div>
                  </div>
                </div>

                <!-- Дополнительная информация -->
                <div class="mt-6 bg-gray-50 p-4 rounded-xl border border-gray-200">
                  <h4 class="font-semibold text-gray-800 mb-3">Детальная информация</h4>
                  <div class="grid grid-cols-1 md:grid-cols-2 gap-4 text-sm text-gray-600">
                    <div class="flex justify-between py-2 border-b border-gray-200">
                      <span>Плотность древостоя:</span>
                      <span class="font-semibold text-gray-800">{{ calculateDensity(selectedForest) }} дер/га</span>
                    </div>
                    <div class="flex justify-between py-2 border-b border-gray-200">
                      <span>Запас углерода:</span>
                      <span class="font-semibold text-gray-800">{{ calculateCarbonDensity(selectedForest).toFixed(1) }} т/га</span>
                    </div>
                    <div class="flex justify-between py-2 border-b border-gray-200">
                      <span>Координаты центра:</span>
                      <span class="font-semibold text-gray-800">{{ getForestCenter(selectedForest)[0].toFixed(4) }}, {{ getForestCenter(selectedForest)[1].toFixed(4) }}</span>
                    </div>
                    <div class="flex justify-between py-2 border-b border-gray-200">
                      <span>Дата последнего обхода:</span>
                      <span class="font-semibold text-gray-800">{{ new Date().toLocaleDateString() }}</span>
                    </div>
                  </div>
                </div>
              </div>
            </Transition>

            <!-- Сообщение при отсутствии выбора -->
            <div 
              v-if="!selectedForest"
              class="bg-white rounded-2xl shadow-lg p-8 border border-gray-200 text-center"
            >
              <div class="w-16 h-16 bg-blue-100 rounded-2xl flex items-center justify-center mx-auto mb-4">
                <svg class="w-8 h-8 text-blue-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 20l-5.447-2.724A1 1 0 013 16.382V5.618a1 1 0 011.447-.894L9 7m0 13l6-3m-6 3V7m6 10l4.553 2.276A1 1 0 0021 18.382V7.618a1 1 0 00-.553-.894L15 4m0 13V4m0 0L9 7"/>
                </svg>
              </div>
              <h3 class="text-xl font-bold text-gray-800 mb-2">Выберите лес на карте</h3>
              <p class="text-gray-600">Кликните на любой лесной массив для просмотра детальной информации</p>
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script setup lang="ts">
import 'leaflet/dist/leaflet.css'
import L from 'leaflet'
import { ref, reactive, computed, onMounted } from 'vue'

// Фикс для маркеров
delete (L.Icon.Default.prototype as any)._getIconUrl
L.Icon.Default.mergeOptions({
  iconRetinaUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.7.1/images/marker-icon-2x.png',
  iconUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.7.1/images/marker-icon.png',
  shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.7.1/images/marker-shadow.png',
})

// Импорты Vue
const map = ref()
const zoom = ref(11)
const center = ref<[number, number]>([54.89, 69.10])
const selectedForest = ref<any>(null)
const hoverForest = ref<any>(null)
const isLoading = ref(false)
const currentZoom = ref(11)
const currentCenter = ref<[number, number]>([54.89, 69.10])
const mapStyle = ref('standard')

// Стили карты
const mapStyles = ref([
  { id: 'standard', name: 'Стандарт', shortName: 'Станд' },
  { id: 'satellite', name: 'Спутник', shortName: 'Спут' },
  { id: 'topo', name: 'Топо', shortName: 'Топо' }
])

// Фильтры
const filters = reactive({
  type: 'all',
  health: 0,
  areaMin: null as number | null,
  areaMax: null as number | null
})

// Данные лесов с правильными координатами
const forests = ref([
  {
    id: 1,
    name: 'Сосновый бор (юг)',
    type: 'pine',
    area: 320,
    health: 85,
    carbon: 8500,
    biodiversity: 78,
    age: 45,
    polygon: {
      type: 'Polygon',
      coordinates: [[
        [54.890862, 69.123389],
        [54.895627, 69.128581],
        [54.902431, 69.128948],
        [54.907856, 69.127825],
        [54.910711, 69.126443],
        [54.911009, 69.124176],
        [54.899538, 69.124154],
        [54.899501, 69.124888],
        [54.893765, 69.122276]
      ]]
    }
  },
  {
    id: 2,
    name: 'Березовая роща (север)',
    type: 'birch',
    area: 280,
    health: 92,
    carbon: 7200,
    biodiversity: 65,
    age: 30,
    polygon: {
      type: 'Polygon',
      coordinates: [[
      [54.926383, 69.124484],
      [54.932095, 69.125835],
      [54.932571, 69.129933],
      [54.930266, 69.132593],
      [54.931118, 69.139350],
      [54.930993, 69.147111],
      [54.932897, 69.149334],
      [54.935251, 69.148724],
      [54.937129, 69.148375],
      [54.939409, 69.151906],
      [54.941637, 69.149116],
      [54.942664, 69.153737],
      [54.941337, 69.160495],
      [54.942514, 69.162719],
      [54.941111, 69.168255],
      [54.942664, 69.170915],
      [54.944041, 69.175580],
      [54.943716, 69.176713],
      [54.941437, 69.175318],
      [54.939208, 69.169607],
      [54.938582, 69.173313],
      [54.937530, 69.172877],
      [54.937180, 69.176931],
      [54.935727, 69.181640],
      [54.938357, 69.182686],
      [54.940561, 69.184997],
      [54.940561, 69.189792],
      [54.936403, 69.199384],
      [54.933948, 69.198294],
      [54.930216, 69.201956],
      [54.922250, 69.211765],
      [54.921799, 69.230250],
      [54.920170, 69.231297],
      [54.915209, 69.226196],
      [54.914858, 69.215166],
      [54.915058, 69.204615],
      [54.922901, 69.195024],
      [54.919869, 69.174664],
      [54.916862, 69.172615],
      [54.912778, 69.163154],
      [54.914757, 69.162238],
      [54.914432, 69.159797],
      [54.911951, 69.158882],
      [54.911399, 69.154827],
      [54.917614, 69.158010],
      [54.918015, 69.153563],
      [54.916737, 69.153170],
      [54.917765, 69.146151],
      [54.921999, 69.143186],
      [54.924780, 69.143012],
      [54.926258, 69.138565],
      [54.924479, 69.134641],
      [54.923302, 69.129017],
      [54.926383, 69.124484]
      ]]
    }
  },
  {
    id: 3,
    name: 'Смешанный лес (восток)',
    type: 'mixed',
    area: 450,
    health: 78,
    carbon: 11200,
    biodiversity: 82,
    age: 55,
    polygon: {
      type: 'Polygon',
      coordinates: [[
      [54.822392, 69.161940],
      [54.831604, 69.212825],
      [54.822350, 69.228457],
      [54.818304, 69.210230],
      [54.814952, 69.175435],
      [54.817348, 69.166389],
      [54.822392, 69.161940]
      ]]
    }
  },
  {
    id: 4,
    name: 'Смешанный лес (центр)',
    type: 'mixed',
    area: 450,
    health: 78,
    carbon: 11200,
    biodiversity: 82,
    age: 55,
    polygon: {
      type: 'Polygon',
      coordinates: [[
      [54.840743, 69.118414],
      [54.831251, 69.117056],
      [54.829122, 69.113963],
      [54.828405, 69.114227],
      [54.827927, 69.112907],
      [54.826624, 69.114077],
      [54.825407, 69.114190],
      [54.825385, 69.116717],
      [54.823343, 69.117358],
      [54.823365, 69.119696],
      [54.823864, 69.122638],
      [54.829252, 69.121997],
      [54.835183, 69.121619],
      [54.838940, 69.120262]
      ]]
    }
  },
  {
  id: 5,
  name: 'Центральный лесопарк',
  type: 'mixed',
  area: 85,
  health: 75,
  carbon: 2100,
  biodiversity: 60,
  age: 25,
  polygon: {
    type: 'Polygon',
    coordinates: [[
      [54.840070, 69.120575],
      [54.833350, 69.123049],
      [54.831545, 69.126512],
      [54.831189, 69.132325],
      [54.834632, 69.134304],
      [54.835012, 69.136448],
      [54.836176, 69.135458],
      [54.836722, 69.132655],
      [54.837363, 69.127089],
      [54.837862, 69.123543],
      [54.839713, 69.123708],
      [54.840070, 69.120575] 
    ]]
  }
},
{
  id: 6,
  name: 'Городской сквер',
  type: 'mixed',
  area: 25,
  health: 80,
  carbon: 600,
  biodiversity: 45,
  age: 15,
  polygon: {
    type: 'Polygon',
    coordinates: [[
      [54.827924, 69.123924],
      [54.828232, 69.128416],
      [54.826833, 69.127712],
      [54.824973, 69.124021],
      [54.827924, 69.123924]
    ]]
  }
},
{
  id: 7,
  name: 'Юго-Западный лесной массив',
  type: 'pine',
  area: 320,
  health: 82,
  carbon: 7800,
  biodiversity: 68,
  age: 40,
  polygon: {
    type: 'Polygon',
    coordinates: [[
      [54.823597, 69.111457],
      [54.821609, 69.122709],
      [54.818051, 69.124844],
      [54.812230, 69.125510],
      [54.810470, 69.128951],
      [54.806184, 69.130061],
      [54.803304, 69.125732],
      [54.800393, 69.116740],
      [54.811977, 69.121607],
      [54.815598, 69.112624],
      [54.819840, 69.114718],
      [54.823597, 69.111457]
    ]]
  }
},
{
  id: 8,
  name: 'Восточный лесной массив',
  type: 'birch',
  area: 280,
  health: 88,
  carbon: 6500,
  biodiversity: 72,
  age: 38,
  polygon: {
    type: 'Polygon',
    coordinates: [[
      [54.859038, 69.081868],
      [54.865334, 69.095846],
      [54.868676, 69.094226],
      [54.869725, 69.089499],
      [54.875164, 69.091457],
      [54.876877, 69.089972],
      [54.875982, 69.082611],
      [54.869182, 69.080518],
      [54.870736, 69.077074],
      [54.869299, 69.072346],
      [54.865063, 69.066539],
      [54.864208, 69.068702],
      [54.862149, 69.066407],
      [54.857723, 69.063848],
      [54.858538, 69.068572],
      [54.863393, 69.074510],
      [54.863469, 69.082273],
      [54.859038, 69.081868]
    ]]
  }
},
{
  id: 9,
  name: 'Северо-Восточный лесной массив',
  type: 'mixed',
  area: 28,
  health: 82,
  carbon: 700,
  biodiversity: 62,
  age: 18,
  polygon: {
    type: 'Polygon',
    coordinates: [[
      [54.872537, 69.148736],
      [54.873470, 69.150307],
      [54.874081, 69.150432],
      [54.875098, 69.150182],
      [54.875080, 69.149433],
      [54.874841, 69.149475],
      [54.874799, 69.148674],
      [54.875224, 69.148435],
      [54.875188, 69.147665],
      [54.874930, 69.147457],
      [54.874577, 69.147343],
      [54.874613, 69.147000],
      [54.874320, 69.146989],
      [54.874260, 69.147145],
      [54.873823, 69.147177],
      [54.873602, 69.147572],
      [54.873560, 69.148352],
      [54.874075, 69.149298],
      [54.874051, 69.149558],
      [54.873698, 69.149308],
      [54.873500, 69.149392],
      [54.872537, 69.148736]
    ]]
  }
},
{
  id: 10,
  name: 'Юго-Восточный лесной массив',
  type: 'pine',
  area: 180,
  health: 85,
  carbon: 4500,
  biodiversity: 65,
  age: 30,
  polygon: {
    type: 'Polygon',
    coordinates: [[
      [54.818827, 69.135967],
      [54.817292, 69.138724],
      [54.816313, 69.136978],
      [54.814486, 69.138035],
      [54.811521, 69.141251],
      [54.813665, 69.159444],
      [54.816551, 69.157193],
      [54.815836, 69.151037],
      [54.816101, 69.148648],
      [54.813983, 69.146902],
      [54.814327, 69.145432],
      [54.816471, 69.146259],
      [54.818430, 69.143272],
      [54.818642, 69.139781],
      [54.819145, 69.137392],
      [54.818827, 69.135967]
    ]]
  }
},
{
  id: 11,
  name: 'Северный лесной участок',
  type: 'birch',
  area: 12,
  health: 88,
  carbon: 300,
  biodiversity: 48,
  age: 15,
  polygon: {
    type: 'Polygon',
    coordinates: [[
      [54.958146, 69.135071],
      [54.957105, 69.136447],
      [54.956682, 69.135071],
      [54.957298, 69.133326],
      [54.958146, 69.135071]
    ]]
  }
},
{
  id: 12,
  name: 'Северо-Западный лесной массив',
  type: 'mixed',
  area: 45,
  health: 82,
  carbon: 1100,
  biodiversity: 58,
  age: 22,
  polygon: {
    type: 'Polygon',
    coordinates: [[
      [54.954870, 69.139601],
      [54.951094, 69.141044],
      [54.950708, 69.137286],
      [54.951344, 69.135809],
      [54.952192, 69.135608],
      [54.952019, 69.138259],
      [54.952577, 69.138292],
      [54.952712, 69.136447],
      [54.953811, 69.137420],
      [54.954080, 69.135910],
      [54.954562, 69.136011],
      [54.954928, 69.137990],
      [54.954870, 69.139601]
    ]]
  }
},
{
  id: 13,
  name: 'Западный лесной массив',
  type: 'pine',
  area: 65,
  health: 84,
  carbon: 1600,
  biodiversity: 62,
  age: 28,
  polygon: {
    type: 'Polygon',
    coordinates: [[
      [54.950265, 69.139634],
      [54.950111, 69.141748],
      [54.949648, 69.143258],
      [54.948666, 69.144265],
      [54.949726, 69.145876],
      [54.948550, 69.150037],
      [54.946237, 69.150506],
      [54.945736, 69.149936],
      [54.946642, 69.147453],
      [54.945968, 69.146614],
      [54.945813, 69.143527],
      [54.947297, 69.142923],
      [54.947837, 69.141144],
      [54.949128, 69.139836],
      [54.950265, 69.139634]
    ]]
  }
},
{
  id: 14,
  name: 'Северо-Восточный большой лес',
  type: 'pine',
  area: 320,
  health: 87,
  carbon: 7800,
  biodiversity: 75,
  age: 45,
  polygon: {
    type: 'Polygon',
    coordinates: [[
      [54.965803, 69.204154],
      [54.961406, 69.202089],
      [54.958455, 69.208337],
      [54.959265, 69.220178],
      [54.962563, 69.227283],
      [54.963605, 69.226628],
      [54.966787, 69.220178],
      [54.967481, 69.211511],
      [54.965948, 69.210856],
      [54.965803, 69.204154]
    ]]
  }
},
{
  id: 15,
  name: 'Восточный большой лесной массив',
  type: 'mixed',
  area: 1850,
  health: 90,
  carbon: 42500,
  biodiversity: 85,
  age: 60,
  polygon: {
    type: 'Polygon',
    coordinates: [[
      [54.904075, 69.248284],
      [54.903034, 69.259810],
      [54.892813, 69.260504],
      [54.893772, 69.271889],
      [54.891695, 69.282299],
      [54.886186, 69.288546],
      [54.886505, 69.312429],
      [54.886186, 69.331034],
      [54.875962, 69.332702],
      [54.871967, 69.355900],
      [54.871227, 69.375543],
      [54.857168, 69.379395],
      [54.857761, 69.343648],
      [54.866197, 69.319218],
      [54.874335, 69.310474],
      [54.870784, 69.290930],
      [54.863680, 69.277299],
      [54.852726, 69.266496],
      [54.854947, 69.259296],
      [54.858499, 69.246179],
      [54.868416, 69.247211],
      [54.879366, 69.239737],
      [54.886172, 69.243088],
      [54.890463, 69.249765],
      [54.904075, 69.248284]
    ]]
  }
}
])

// Важные точки на карте
const importantPoints = ref([
  {
    id: 1,
    name: 'Лесничество',
    type: 'management',
    position: [54.895, 69.125],
    description: 'Центр управления лесным хозяйством'
  },
  {
    id: 2,
    name: 'Пикниковая зона',
    type: 'recreation',
    position: [54.828, 69.125],
    description: 'Место для отдыха и пикников'
  },
  {
    id: 3,
    name: 'Научная станция',
    type: 'research',
    position: [54.865, 69.095],
    description: 'Исследования лесных экосистем'
  },
  {
    id: 4,
    name: 'Пожарная вышка',
    type: 'firewatch',
    position: [54.912, 69.200],
    description: 'Наблюдение за пожарной безопасностью'
  }
])

// Вычисляемые свойства
const totalArea = computed(() => {
  return forests.value.reduce((sum, forest) => sum + forest.area, 0)
})

const totalCarbon = computed(() => {
  return forests.value.reduce((sum, forest) => sum + forest.carbon, 0)
})

const avgHealth = computed(() => {
  const avg = forests.value.reduce((sum, forest) => sum + forest.health, 0) / forests.value.length
  return Math.round(avg)
})

const pineCount = computed(() => {
  return forests.value.filter(f => f.type === 'pine').length
})

const birchCount = computed(() => {
  return forests.value.filter(f => f.type === 'birch').length
})

const mixedCount = computed(() => {
  return forests.value.filter(f => f.type === 'mixed').length
})

const filteredForests = computed(() => {
  return forests.value.filter(forest => {
    const typeMatch = filters.type === 'all' || forest.type === filters.type
    const healthMatch = forest.health >= filters.health
    const areaMinMatch = filters.areaMin === null || forest.area >= filters.areaMin
    const areaMaxMatch = filters.areaMax === null || forest.area <= filters.areaMax
    
    return typeMatch && healthMatch && areaMinMatch && areaMaxMatch
  })
})

// Методы
const getForestStyle = (forest: any) => {
  const typeColors: { [key: string]: string } = {
    'pine': '#059669',
    'birch': '#CA8A04',
    'mixed': '#2563EB'
  }
  
  const baseColor = typeColors[forest.type] || '#6B7280'
  const isSelected = selectedForest.value?.id === forest.id
  const isHovered = hoverForest.value?.id === forest.id
  
  return {
    color: baseColor,
    weight: isSelected ? 4 : (isHovered ? 3 : 2),
    opacity: isSelected ? 1 : (isHovered ? 0.9 : 0.7),
    fillColor: baseColor,
    fillOpacity: isSelected ? 0.4 : (isHovered ? 0.25 : 0.2),
    className: isSelected ? 'selected-forest' : ''
  }
}

const getHoverStyle = (forest: any) => {
  const typeColors: { [key: string]: string } = {
    'pine': '#059669',
    'birch': '#CA8A04',
    'mixed': '#2563EB'
  }
  
  const baseColor = typeColors[forest.type] || '#6B7280'
  
  return {
    color: '#9333EA',
    weight: 3,
    opacity: 0.8,
    fillColor: baseColor,
    fillOpacity: 0.15,
    className: 'hover-forest'
  }
}

const getSelectedStyle = (forest: any) => {
  return {
    color: '#DC2626',
    weight: 5,
    opacity: 0.9,
    fillColor: 'transparent',
    fillOpacity: 0,
    className: 'selected-forest-outline'
  }
}

const getTypeColorClass = (type: string) => {
  const colors: { [key: string]: string } = {
    'pine': 'bg-green-600 border-green-600',
    'birch': 'bg-yellow-600 border-yellow-600',
    'mixed': 'bg-blue-600 border-blue-600'
  }
  return colors[type] || 'bg-gray-600 border-gray-600'
}

const getTypeName = (type: string) => {
  const names: { [key: string]: string } = {
    'pine': 'сосновый',
    'birch': 'березовый',
    'mixed': 'смешанный'
  }
  return names[type] || type
}

const getHealthColor = (health: number) => {
  if (health >= 80) return 'bg-green-500'
  if (health >= 60) return 'bg-yellow-500'
  if (health >= 40) return 'bg-orange-500'
  return 'bg-red-500'
}

const getHealthTextColor = (health: number) => {
  if (health >= 80) return 'text-green-600'
  if (health >= 60) return 'text-yellow-600'
  if (health >= 40) return 'text-orange-600'
  return 'text-red-600'
}

const getMarkerIcon = (type: string) => {
  const colors: { [key: string]: string } = {
    'management': 'blue',
    'recreation': 'green',
    'research': 'purple',
    'firewatch': 'red'
  }
  
  const color = colors[type] || 'gray'
  
  return L.divIcon({
    className: `custom-marker ${color}-marker`,
    html: `
      <div class="relative">
        <div class="w-5 h-5 rounded-full bg-${color}-500 border-2 border-white shadow-lg"></div>
      </div>
    `,
    iconSize: [20, 20],
    iconAnchor: [10, 10]
  })
}

const getForestCenter = (forest: any) => {
  const coords = forest.polygon.coordinates[0]
  const lat = coords.reduce((sum: number, coord: [number, number]) => sum + coord[0], 0) / coords.length
  const lng = coords.reduce((sum: number, coord: [number, number]) => sum + coord[1], 0) / coords.length
  return [lat, lng] as [number, number]
}

const selectForest = (forest: any) => {
  selectedForest.value = forest
  // Центрируем карту на выбранном лесе
  if (map.value) {
    const leafletMap = map.value.leafletObject
    const bounds = L.latLngBounds(forest.polygon.coordinates[0])
    leafletMap.fitBounds(bounds, { padding: [50, 50], maxZoom: 15 })
  }
}

const clearSelection = () => {
  selectedForest.value = null
}

// Функция сброса карты к исходному виду
const resetView = () => {
  if (map.value) {
    const leafletMap = map.value.leafletObject
    leafletMap.setView(center.value, zoom.value)
    clearSelection()
  }
}

// Функция сброса фильтров
const resetFilters = () => {
  filters.type = 'all'
  filters.health = 0
  filters.areaMin = null
  filters.areaMax = null
}

// Функция показа всех лесов
const showAllForests = () => {
  if (map.value && forests.value.length > 0) {
    const leafletMap = map.value.leafletObject
    const bounds = new L.LatLngBounds([])
    
    forests.value.forEach(forest => {
      bounds.extend(L.latLngBounds(forest.polygon.coordinates[0]))
    })
    
    leafletMap.fitBounds(bounds, { padding: [50, 50] })
    clearSelection()
  }
}

const onMapMove = (event: any) => {
  const leafletMap = map.value.leafletObject
  currentCenter.value = leafletMap.getCenter()
}

const onMapZoom = (event: any) => {
  const leafletMap = map.value.leafletObject
  currentZoom.value = leafletMap.getZoom()
}

const calculateDensity = (forest: any) => {
  const baseDensity = forest.type === 'pine' ? 1200 : forest.type === 'birch' ? 800 : 1000
  const ageFactor = Math.min(forest.age / 50, 1.5)
  const healthFactor = forest.health / 100
  return Math.round(baseDensity * ageFactor * healthFactor)
}

const calculateCarbonDensity = (forest: any) => {
  return forest.carbon / forest.area
}

const calculateValueIndex = (forest: any) => {
  const healthScore = forest.health * 0.4
  const biodiversityScore = forest.biodiversity * 0.3
  const carbonScore = (forest.carbon / 1000) * 0.2
  const ageScore = Math.min(forest.age / 2, 10)
  return Math.min(Math.round(healthScore + biodiversityScore + carbonScore + ageScore), 100)
}

// Инициализация
onMounted(() => {
  isLoading.value = true
  setTimeout(() => {
    isLoading.value = false
  }, 1000)
})
</script>

<style scoped>
/* Стили для карты */
:deep(.leaflet-container) {
  height: 100%;
  width: 100%;
  margin: 0;
  padding: 0;
  font-family: inherit;
  border-radius: 0 0 1rem 1rem;
}

/* Элементы управления картой */
:deep(.custom-control) {
  background: transparent !important;
  border: none !important;
  z-index: 10;
}

/* Стили для маркеров */
:deep(.custom-marker) {
  background: transparent !important;
  border: none !important;
}

/* Анимации */
.panel-enter-active,
.panel-leave-active {
  transition: all 0.3s ease;
}

.panel-enter-from {
  opacity: 0;
  transform: translateY(-10px);
}

.panel-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

/* Стили для выбранных лесов */
:deep(.selected-forest) {
  animation: pulse 2s infinite;
}

:deep(.hover-forest) {
  animation: bounce 0.5s ease-in-out;
}

/* Кастомный стиль для слайдера */
.slider::-webkit-slider-thumb {
  appearance: none;
  height: 20px;
  width: 20px;
  border-radius: 50%;
  background: #3b82f6;
  cursor: pointer;
  border: 2px solid #ffffff;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.slider::-moz-range-thumb {
  height: 20px;
  width: 20px;
  border-radius: 50%;
  background: #3b82f6;
  cursor: pointer;
  border: 2px solid #ffffff;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

@keyframes pulse {
  0% { opacity: 0.4; }
  50% { opacity: 0.7; }
  100% { opacity: 0.4; }
}

@keyframes bounce {
  0% { transform: scale(1); }
  50% { transform: scale(1.02); }
  100% { transform: scale(1); }
}
</style>