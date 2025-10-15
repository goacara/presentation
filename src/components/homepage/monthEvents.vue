<script setup>
import { reactive, computed, onMounted, ref } from 'vue'
import rainforestImage from '@/assets/rainforest.jpg'

// Sorting options
const sortBy = ref('date')
const sortOptions = [
  { value: 'date', label: 'Date' },
  { value: 'title', label: 'Title' }
]

// Get current month name
const currentMonth = computed(() => {
  const months = [
    'January', 'February', 'March', 'April', 'May', 'June',
    'July', 'August', 'September', 'October', 'November', 'December'
  ]
  return months[new Date().getMonth()]
})

//Constants
const monthlyEvents = reactive({
  // Real event data from events.json
  monthEvents: [
    {
      id: 1,
      title: 'Interlinked',
      date: '1 Oct - 31 Oct 2025',
      time: 'All Day',
      location: 'Think & Tink',
      image: '/events/1.png',
      category: ['Culture, Arts & Heritage', 'Business & Education'],
      organiser: 'Think & Tink',
      price: 'FREE'
    },
    {
      id: 2,
      title: 'Songket Weaving Workshop and Crafts Gallery',
      date: '1 Oct - 31 Oct 2025',
      time: 'All Day',
      location: 'Tanoti House',
      image: '/events/2.png',
      category: ['Culture, Arts & Heritage'],
      organiser: 'Tanoti Sdn Bhd',
      price: 'FREE'
    },
    {
      id: 3,
      title: 'Project Craft Connections: Sarawak Indigenous Craft Anthology',
      date: '1 Oct - 31 Oct 2025',
      time: 'All Day',
      location: 'Tanoti House',
      image: '/events/3.png',
      category: ['Culture, Arts & Heritage'],
      organiser: 'Tanoti Sdn Bhd',
      price: 'FREE'
    },
    {
      id: 4,
      title: 'WAK Food Adventure Trail 2.0',
      date: '1 Oct - 31 Oct 2025',
      time: 'All Day',
      location: 'Various eateries across Kuching',
      image: '/events/4.png',
      category: ['Food & Markets', 'Festivals & Entertainment'],
      organiser: 'WAK Festival',
      price: 'Variable (by location)'
    },
    {
      id: 5,
      title: 'WAK Stinky Toe Trail',
      date: '1 Oct - 31 Oct 2025',
      time: 'All Day',
      location: 'Kuching City',
      image: '/events/5.png',
      category: ['Culture, Arts & Heritage', 'Festivals & Entertainment'],
      organiser: 'WAK Festival',
      price: 'FREE'
    },
    {
      id: 6,
      title: 'All About Arts',
      date: '1 Oct - 31 Oct 2025',
      time: 'All Day',
      location: 'Various art venues',
      image: '/events/6.png',
      category: ['Culture, Arts & Heritage'],
      organiser: 'WAK Festival',
      price: 'FREE'
    },
    {
      id: 7,
      title: 'Celebrating Malaysia Through the Lens',
      date: '1 Oct - 31 Oct 2025',
      time: 'All Day',
      location: 'Tegas Digital Village',
      image: '/events/7.png',
      category: ['Culture, Arts & Heritage', 'Business & Education'],
      organiser: 'WAK Festival',
      price: 'FREE'
    },
    {
      id: 8,
      title: 'Wild About Nature – Closer to Nature',
      date: 'Ongoing throughout October 2025',
      time: 'All Day',
      location: 'Sama Jaya Forest Park',
      image: '/events/8.png',
      category: ['Wellness & Nature', 'Culture, Arts & Heritage'],
      organiser: 'WAK Festival x Nature Society',
      price: 'FREE'
    },
    {
      id: 9,
      title: 'MATTA Malaysia Pickleball Grand Slam 2025 (MPG2025)',
      date: '13 Oct - 20 Oct 2025',
      time: 'All Day',
      location: 'Pikabol Kuching',
      image: '/events/9.png',
      category: ['Sports & Outdoor'],
      organiser: 'Malaysia Pickleball Association',
      price: 'Spectator entry FREE'
    },
    {
      id: 10,
      title: 'Pink it Up! Market Place',
      date: '10 Oct - 12 Oct, 17 Oct - 19 Oct, 24 Oct - 26 Oct 2025',
      time: 'All Day',
      location: '2nd Floor, tHe Spring Shopping Mall',
      image: '/events/10.png',
      category: ['Food & Markets', 'Wellness & Nature'],
      organiser: 'The Spring Management Services Sdn Bhd & WAK Festival',
      price: 'FREE'
    },
    {
      id: 11,
      title: 'Explore, Experience and Energize with LaVie!',
      date: '5 Oct, 12 Oct, 19 Oct, 26 Oct 2025',
      time: 'All Day',
      location: 'LaVie Wellness Sanctuary',
      image: '/events/11.png',
      category: ['Wellness & Nature'],
      organiser: 'LaVie Wellness Sanctuary by Dr. Lowell Chai',
      price: 'RM39 per pax (inclusive of lunch & mini workshops)'
    },
    {
      id: 12,
      title: 'WAK X Texhouse Artspace',
      date: '1 Oct - 20 Oct 2025',
      time: 'All Day',
      location: 'Texhouse Artspace',
      image: '/events/12.png',
      category: ['Culture, Arts & Heritage'],
      organiser: 'WAK Festival',
      price: 'FREE'
    }
  ],
  isLoading: false,
  error: null,
  hasData: true,
})

// Computed property for sorted events
const sortedEvents = computed(() => {
  const events = [...monthlyEvents.monthEvents]
  
  switch (sortBy.value) {
    case 'date':
      return events.sort((a, b) => new Date(a.date) - new Date(b.date))
    case 'popularity':
      return events.sort((a, b) => b.popularity - a.popularity)
    case 'title':
      return events.sort((a, b) => a.title.localeCompare(b.title))
    default:
      return events
  }
})

//functions
async function fetchMonthlyEvents() {
  try {
    monthlyEvents.isLoading = true
    monthlyEvents.error = null

    // TODO: Replace with actual API endpoint during production deployment
    const response = await fetch('/api/monthly-events')

    const data = await response.json()

    // Update the retrieved data
    monthlyEvents.monthEvents = data.monthEvents || data
    monthlyEvents.hasData = monthlyEvents.monthEvents.length > 0
  } catch (error) {
    // TODO: Remove mock data fallback during production deployment
    console.log('Using mock data for monthly events (development mode)')
    monthlyEvents.error = null // Clear error since we have mock data
    monthlyEvents.hasData = true
  } finally {
    monthlyEvents.isLoading = false
  }
}

// TODO: Remove this mock function during production deployment
function handleSortChange() {
  console.log(`Sorting events by: ${sortBy.value}`)
}

// Fetch events when component mounts
onMounted(() => {
  fetchMonthlyEvents()
})
</script>

<template>
  <div class="month-events">
    <div class="container">
      <!-- Section Header -->
      <div class="section-header">
        <h2 class="section-title">Events in {{ currentMonth }} 2025</h2>
        <div class="sort-controls">
          <label for="sort-select" class="sort-label">Sort by:</label>
          <select 
            id="sort-select" 
            v-model="sortBy" 
            @change="handleSortChange"
            class="sort-select"
          >
            <option 
              v-for="option in sortOptions" 
              :key="option.value" 
              :value="option.value"
            >
              {{ option.label }}
            </option>
          </select>
        </div>
      </div>

      <!-- Loading State -->
      <div v-if="monthlyEvents.isLoading" class="loading-state">
        <div class="spinner"></div>
        <p>Loading events...</p>
      </div>

      <!-- Error State -->
      <div v-else-if="monthlyEvents.error" class="error-state">
        <p>{{ monthlyEvents.error }}</p>
      </div>

      <!-- Events Grid -->
      <div v-else-if="monthlyEvents.hasData" class="events-grid">
        <div 
          v-for="event in sortedEvents" 
          :key="event.id" 
          class="event-card"
        >
          <RouterLink :to="`/event/${event.id}`" class="event-link">
            <div class="event-image-container">
              <img 
                :src="event.image" 
                :alt="event.title" 
                class="event-image"
              />
            </div>
            <div class="event-content">
              <h3 class="event-title">{{ event.title }}</h3>
              <div class="event-details">
                <p class="event-date">📅 {{ event.date }}</p>
                <p class="event-time">🕒 {{ event.time }}</p>
                <p class="event-location">📍 {{ event.location }}</p>
              </div>
            </div>
          </RouterLink>
        </div>
      </div>

      <!-- No Events State -->
      <div v-else class="no-events-state">
        <p>No events found for this month.</p>
      </div>

      <!-- View All Events Button -->
      <div class="view-all-container">
        <RouterLink to="/events" class="view-all-button">
          View All Events
        </RouterLink>
      </div>
    </div>
  </div>
</template>

<style scoped>
.month-events {
  padding: 3rem 0;
  background-color: #f8f9fa;
}

.container {
  padding: 25px 40px;
}

/* Section Header */
.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 2rem;
  flex-wrap: wrap;
  gap: 1rem;
}

.section-title {
  font-size: 1.5rem;
  font-weight: 700;
  color: #2c3e50;
  margin: 0;
}

.sort-controls {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.sort-label {
  font-size: 0.9rem;
  color: #666;
  font-weight: 500;
}

.sort-select {
  padding: 0.5rem 1rem;
  border: 1px solid #ddd;
  border-radius: 6px;
  background-color: white;
  font-size: 0.9rem;
  color: #333;
  cursor: pointer;
  transition: border-color 0.2s ease;
}

.sort-select:hover {
  border-color: #007bff;
}

.sort-select:focus {
  outline: none;
  border-color: #007bff;
  box-shadow: 0 0 0 2px rgba(0, 123, 255, 0.25);
}

/* Loading State */
.loading-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 4rem 0;
  color: #666;
}

.spinner {
  width: 40px;
  height: 40px;
  border: 4px solid #f3f3f3;
  border-top: 4px solid #007bff;
  border-radius: 50%;
  animation: spin 1s linear infinite;
  margin-bottom: 1rem;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

/* Error State */
.error-state {
  text-align: center;
  padding: 4rem 0;
  color: #dc3545;
  font-size: 1.1rem;
}

/* No Events State */
.no-events-state {
  text-align: center;
  padding: 4rem 0;
  color: #666;
  font-size: 1.1rem;
}

/* Events Grid */
.events-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 1.5rem;
  margin-bottom: 2rem;
}

.event-card {
  background: white;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  transition: all 0.3s ease;
  height: fit-content;
}

.event-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.15);
}

.event-link {
  display: block;
  text-decoration: none;
  color: inherit;
  height: 100%;
}

.event-image-container {
  position: relative;
  width: 100%;
  height: 200px;
  overflow: hidden;
}

.event-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.3s ease;
}

.event-card:hover .event-image {
  transform: scale(1.05);
}

.event-content {
  padding: 1.25rem;
}

.event-title {
  font-size: 1.1rem;
  font-weight: 600;
  color: #2c3e50;
  margin: 0 0 1rem 0;
  line-height: 1.4;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.event-details {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.event-details p {
  margin: 0;
  font-size: 0.9rem;
  color: #666;
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

/* View All Events Button */
.view-all-container {
  display: flex;
  justify-content: center;
  margin-top: 3rem;
  padding-bottom: 1rem;
}

.view-all-button {
  display: inline-block;
  padding: 1rem 2.5rem;
  background: #4a5568;
  color: white;
  text-decoration: none;
  border-radius: 12px;
  font-weight: 500;
  font-size: 1rem;
  text-align: center;
  transition: all 0.3s ease;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  border: none;
  cursor: pointer;
}

.view-all-button:hover {
  background: #2d3748;
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  color: white;
  text-decoration: none;
}

.view-all-button:active {
  transform: translateY(0);
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
}

/* Responsive Design */
@media (max-width: 768px) {
  .section-header {
    flex-direction: column;
    align-items: flex-start;
  }
  
  .section-title {
    font-size: 1.5rem;
  }
  
  .events-grid {
    grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
    gap: 1rem;
  }
  
  .container {
    padding: 15px 35px;
  }
  
  .view-all-button {
    padding: 0.875rem 2rem;
    font-size: 0.95rem;
  }
}

@media (max-width: 480px) {
  .events-grid {
    grid-template-columns: 1fr;
  }
  
  .event-image-container {
    height: 180px;
  }
  
  .view-all-button {
    padding: 0.75rem 1.5rem;
    font-size: 0.9rem;
  }
}
</style>
