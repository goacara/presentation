<script setup>
import { ref, computed, onMounted } from 'vue'

// Filter and sort state
const searchQuery = ref('')
const selectedCategory = ref('Event Category')
const selectedYear = ref('2025')
const sortBy = ref('date')

// Sample categories for filters
const categories = ['Event Category', 'Culture, Arts & Heritage', 'Food & Markets', 'Festivals & Entertainment', 'Business & Education', 'Wellness & Nature', 'Sports & Outdoor']
const years = ['2024', '2025', '2026']
const sortOptions = [
  { value: 'date', label: 'Date' },
  { value: 'title', label: 'Title' }
]

// Real event data from events.json
const events = ref([
  {
    id: 1,
    title: 'Interlinked',
    date: '1 Oct - 31 Oct 2025',
    tagline: 'An innovative exhibition exploring the connections between art, technology, and human experience.',
    image: '/events/1.png',
    category: 'Culture, Arts & Heritage'
  },
  {
    id: 2,
    title: 'Songket Weaving Workshop and Crafts Gallery',
    date: '1 Oct - 31 Oct 2025',
    tagline: 'Learn the traditional art of songket weaving and explore indigenous crafts.',
    image: '/events/2.png',
    category: 'Culture, Arts & Heritage'
  },
  {
    id: 3,
    title: 'Project Craft Connections: Sarawak Indigenous Craft Anthology',
    date: '1 Oct - 31 Oct 2025',
    tagline: 'A comprehensive showcase of Sarawak\'s rich indigenous craft traditions.',
    image: '/events/3.png',
    category: 'Culture, Arts & Heritage'
  },
  {
    id: 4,
    title: 'WAK Food Adventure Trail 2.0',
    date: '1 Oct - 31 Oct 2025',
    tagline: 'Discover Kuching\'s culinary delights through this exciting food trail adventure.',
    image: '/events/4.png',
    category: 'Food & Markets'
  },
  {
    id: 5,
    title: 'WAK Stinky Toe Trail',
    date: '1 Oct - 31 Oct 2025',
    tagline: 'A unique cultural trail exploring Kuching\'s heritage and local stories.',
    image: '/events/5.png',
    category: 'Culture, Arts & Heritage'
  },
  {
    id: 6,
    title: 'All About Arts',
    date: '1 Oct - 31 Oct 2025',
    tagline: 'Immerse yourself in various art forms and creative expressions.',
    image: '/events/6.png',
    category: 'Culture, Arts & Heritage'
  },
  {
    id: 7,
    title: 'Celebrating Malaysia Through the Lens',
    date: '1 Oct - 31 Oct 2025',
    tagline: 'A photography exhibition celebrating Malaysia\'s diversity and beauty.',
    image: '/events/7.png',
    category: 'Culture, Arts & Heritage'
  },
  {
    id: 8,
    title: 'Wild About Nature – Closer to Nature',
    date: 'Ongoing throughout October 2025',
    tagline: 'Connect with nature through guided walks and environmental activities.',
    image: '/events/8.png',
    category: 'Wellness & Nature'
  },
  {
    id: 9,
    title: 'MATTA Malaysia Pickleball Grand Slam 2025 (MPG2025)',
    date: '13 Oct - 20 Oct 2025',
    tagline: 'Malaysia\'s premier pickleball tournament featuring top players from around the region.',
    image: '/events/9.png',
    category: 'Sports & Outdoor'
  },
  {
    id: 10,
    title: 'Pink it Up! Market Place',
    date: '10 Oct - 12 Oct, 17 Oct - 19 Oct, 24 Oct - 26 Oct 2025',
    tagline: 'A vibrant marketplace featuring local vendors, wellness products, and community activities.',
    image: '/events/10.png',
    category: 'Food & Markets'
  },
  {
    id: 11,
    title: 'Explore, Experience and Energize with LaVie!',
    date: '5 Oct, 12 Oct, 19 Oct, 26 Oct 2025',
    tagline: 'Wellness workshops and activities designed to rejuvenate mind, body, and spirit.',
    image: '/events/11.png',
    category: 'Wellness & Nature'
  },
  {
    id: 12,
    title: 'WAK X Texhouse Artspace',
    date: '1 Oct - 20 Oct 2025',
    tagline: 'Contemporary art exhibitions and creative workshops in a unique artspace setting.',
    image: '/events/12.png',
    category: 'Culture, Arts & Heritage'
  }
])

// Computed filtered and sorted events
const filteredEvents = computed(() => {
  let filtered = events.value

  // Filter by search query
  if (searchQuery.value) {
    filtered = filtered.filter(event => 
      event.title.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
      event.tagline.toLowerCase().includes(searchQuery.value.toLowerCase())
    )
  }

  // Filter by category
  if (selectedCategory.value && selectedCategory.value !== 'Event Category') {
    filtered = filtered.filter(event => event.category === selectedCategory.value)
  }

  // Sort events
  filtered.sort((a, b) => {
    switch (sortBy.value) {
      case 'title':
        return a.title.localeCompare(b.title)
      case 'date':
      default:
        return a.id - b.id // Mock date sorting by ID
    }
  })

  return filtered
})

// Clear filters function
const clearFilters = () => {
  searchQuery.value = ''
  selectedCategory.value = 'Event Category'
  selectedYear.value = '2025'
  sortBy.value = 'date'
}
</script>

<template>
  <div class="event-list-page">
    <!-- Page Header -->
    <div class="page-header">
      <div class="container">
        <h1 class="page-title">Events in June 2025</h1>
        <div class="search-filters-section">
          <div class="search-container">
            <input 
              v-model="searchQuery"
              type="text" 
              placeholder="Search events in Sarawak"
              class="search-input"
            />
          </div>
          
          <div class="filters-row">
            <select v-model="selectedCategory" class="filter-select">
              <option v-for="category in categories" :key="category" :value="category">
                {{ category }}
              </option>
            </select>
            
            <select v-model="selectedYear" class="filter-select year-filter">
              <option v-for="year in years" :key="year" :value="year">
                {{ year }}
              </option>
            </select>
            
            <select v-model="sortBy" class="filter-select sort-filter">
              <option v-for="option in sortOptions" :key="option.value" :value="option.value">
                Sort by {{ option.label }}
              </option>
            </select>
            
            <button @click="clearFilters" class="clear-filters-btn">
              Clear Filters
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Events Grid -->
    <div class="events-section">
      <div class="container">
        <div class="events-grid">
          <RouterLink 
            v-for="event in filteredEvents" 
            :key="event.id"
            :to="`/event/${event.id}`"
            class="event-card-link"
          >
            <div class="event-card">
              <div class="event-image-container">
                <img :src="event.image" :alt="event.title" class="event-image" />
              </div>
              <div class="event-content">
                <h3 class="event-title">Title: {{ event.title }}</h3>
                <p class="event-date">{{ event.date }}</p>
                <p class="event-tagline">Tagline: {{ event.tagline }}</p>
              </div>
            </div>
          </RouterLink>
        </div>
        
        <div v-if="filteredEvents.length === 0" class="no-events">
          <p>No events found matching your criteria.</p>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.event-list-page {
  min-height: 100vh;
  background-color: #f8f9fa;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
}

/* Page Header */
.page-header {
  background: white;
  padding: 2rem 0;
  border-bottom: 1px solid #e9ecef;
}

.page-title {
  font-size: 2rem;
  font-weight: 700;
  color: #2c3e50;
  margin: 0 0 1.5rem 0;
}

.search-filters-section {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.search-container {
  width: 100%;
}

.search-input {
  width: 100%;
  padding: 12px 16px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 1rem;
  background-color: #f8f9fa;
}

.search-input:focus {
  outline: none;
  border-color: #007bff;
  background-color: white;
}

.filters-row {
  display: flex;
  gap: 1rem;
  align-items: center;
  flex-wrap: wrap;
}

.filter-select {
  padding: 8px 12px;
  border: 1px solid #ddd;
  border-radius: 6px;
  background: #4a5568;
  color: white;
  font-size: 0.9rem;
  cursor: pointer;
}

.year-filter,
.sort-filter {
  background: #4a5568;
}

.sort-filter {
  min-width: 140px;
}

.clear-filters-btn {
  padding: 8px 16px;
  background: transparent;
  border: 1px solid #ddd;
  border-radius: 6px;
  color: #666;
  cursor: pointer;
  font-size: 0.9rem;
}

.clear-filters-btn:hover {
  background: #f8f9fa;
}

/* Events Section */
.events-section {
  padding: 2rem 0;
}

.events-grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 1rem;
}

.event-card-link {
  text-decoration: none;
  color: inherit;
  display: block;
  transition: transform 0.2s ease;
}

.event-card-link:hover {
  transform: translateY(-2px);
  text-decoration: none;
  color: inherit;
}

.event-card-link:focus {
  outline: 2px solid #007bff;
  outline-offset: 2px;
}

.event-card {
  display: flex;
  background: white;
  border: 1px solid #e9ecef;
  border-radius: 8px;
  overflow: hidden;
  transition: box-shadow 0.3s ease;
  cursor: pointer;
}

.event-card-link:hover .event-card {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.event-image-container {
  flex-shrink: 0;
  width: 200px;
  height: 150px;
}

.event-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  background: linear-gradient(135deg, #2d5a3d 0%, #4a7c59 100%);
}

.event-content {
  padding: 1.5rem;
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.event-title {
  font-size: 1.1rem;
  font-weight: 600;
  color: #2c3e50;
  margin: 0 0 0.5rem 0;
  line-height: 1.4;
}

.event-date {
  font-size: 0.9rem;
  color: #666;
  margin: 0 0 0.75rem 0;
}

.event-tagline {
  font-size: 0.95rem;
  color: #555;
  margin: 0;
  line-height: 1.5;
}

.no-events {
  text-align: center;
  padding: 3rem 0;
  color: #666;
}

/* Responsive Design */
@media (max-width: 768px) {
  .container {
    padding: 0 15px;
  }
  
  .page-title {
    font-size: 1.5rem;
  }
  
  .filters-row {
    flex-direction: column;
    align-items: stretch;
  }
  
  .filter-select,
  .clear-filters-btn {
    width: 100%;
  }
  
  .event-card {
    flex-direction: column;
  }
  
  .event-image-container {
    width: 100%;
    height: 200px;
  }
  
  .event-content {
    padding: 1rem;
  }
}
</style>