<script setup>
import { reactive, computed, onMounted } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()

// TODO: change object properties to match API response
const eventDetails = reactive({
  event: {
    id: 0,
    title: '',
    description: '',
    date: '',
    time: '',
    location: '',
    image: '',
    category: '',
    fullDescription: '',
    images: [],
    organizer: '',
    price: '',
  },
  isLoading: false,
  error: null,
  hasData: false,
})

// Functions
async function fetchEventDetails() {
  try {
    eventDetails.isLoading = true
    eventDetails.error = null

    // API call to get event details by ID
    const eventId = route.params.id
    const response = await fetch(`/api/events/${eventId}`)

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`)
    }

    const data = await response.json()

    // Update the reactive data
    eventDetails.event = data.event || data
    eventDetails.hasData = !!eventDetails.event.id
  } catch (error) {
    eventDetails.error = 'Failed to fetch event details'
    console.error('Error fetching event details:', error)

    // Real event data from events.json
    // Mock data for development
    const eventId = parseInt(route.params.id) || 1
    
    // Real events data
    const realEvents = [
      {
        id: 1,
        title: 'Interlinked',
        description: 'An innovative exhibition exploring the connections between art, technology, and human experience.',
        fullDescription: 'Interlinked is a groundbreaking exhibition that delves into the intricate relationships between art, technology, and human experience. This immersive showcase features interactive installations, digital art pieces, and thought-provoking displays that challenge visitors to reconsider their understanding of connectivity in the modern world. Through a carefully curated selection of works by local and international artists, the exhibition explores themes of communication, interdependence, and the evolving nature of human relationships in our increasingly digital society.',
        date: '1 Oct - 31 Oct 2025',
        time: 'All Day',
        location: 'Think & Tink',
        image: '/1.png',
        category: 'Culture, Arts & Heritage',
        organizer: 'Think & Tink',
        price: 'FREE',
        images: ['/1.png']
      },
      {
        id: 2,
        title: 'Songket Weaving Workshop and Crafts Gallery',
        description: 'Learn the traditional art of songket weaving and explore indigenous crafts.',
        fullDescription: 'Immerse yourself in the rich cultural heritage of Sarawak through this comprehensive songket weaving workshop and crafts gallery. Participants will learn the intricate techniques of traditional songket weaving from master craftspeople, understanding the cultural significance and historical importance of this beautiful art form. The accompanying gallery showcases a stunning collection of indigenous crafts, highlighting the diversity and skill of Sarawak\'s artisan communities.',
        date: '1 Oct - 31 Oct 2025',
        time: 'All Day',
        location: 'Tanoti House',
        image: '/2.png',
        category: 'Culture, Arts & Heritage',
        organizer: 'Tanoti Sdn Bhd',
        price: 'FREE',
        images: ['/2.png']
      },
      {
        id: 3,
        title: 'Project Craft Connections: Sarawak Indigenous Craft Anthology',
        description: 'A comprehensive showcase of Sarawak\'s rich indigenous craft traditions.',
        fullDescription: 'Project Craft Connections presents an extensive anthology of Sarawak\'s indigenous craft traditions, celebrating the remarkable diversity and artistry of the region\'s cultural heritage. This comprehensive showcase features traditional crafts from various ethnic communities, including intricate beadwork, wood carvings, textiles, and ceremonial objects. Visitors will gain deep insights into the cultural significance, techniques, and stories behind each craft, fostering appreciation and understanding of Sarawak\'s rich indigenous heritage.',
        date: '1 Oct - 31 Oct 2025',
        time: 'All Day',
        location: 'Tanoti House',
        image: '/3.png',
        category: 'Culture, Arts & Heritage',
        organizer: 'Tanoti Sdn Bhd',
        price: 'FREE',
        images: ['/3.png']
      }
    ]
    
    // Find the event by ID or use the first event as default
    const selectedEvent = realEvents.find(event => event.id === eventId) || realEvents[0]
    eventDetails.event = selectedEvent
    eventDetails.hasData = true
    eventDetails.error = null
  } finally {
    eventDetails.isLoading = false
  }
}

// Computed property for sidebar details
const sidebarDetails = computed(() => {
  const details = {
    Date: eventDetails.event.date,
    Time: eventDetails.event.time,
    Location: eventDetails.event.location,
    Organizer: eventDetails.event.organizer,
    Price: eventDetails.event.price,
    Category: eventDetails.event.category,
  }

  // Filter out empty values
  return Object.entries(details).filter(([key, value]) => value && value.trim() !== '')
})

// Fetch event details when component mounts
onMounted(() => {
  fetchEventDetails()
})
</script>

<template>
  <div class="event-details">
    <!-- Loading state -->
    <div v-if="eventDetails.isLoading" class="loading">
      <div class="spinner-border text-primary" role="status">
        <span class="visually-hidden">Loading event details...</span>
      </div>
    </div>

    <!-- Error state -->
    <div v-if="eventDetails.error" class="error alert alert-danger">
      {{ eventDetails.error }}
    </div>

    <!-- Event details content -->
    <div v-if="eventDetails.hasData && !eventDetails.isLoading" class="event-content">
      <!-- Hero Image -->
      <div class="event-hero">
        <img :src="eventDetails.event.image" :alt="eventDetails.event.title" class="hero-image" />
        <div class="hero-overlay">
          <div class="hero-content">
            <h1 class="event-title">{{ eventDetails.event.title }}</h1>
            <div class="event-meta">
              <span class="event-date">📅 {{ eventDetails.event.date }}</span>
              <span class="event-time">🕒 {{ eventDetails.event.time }}</span>
              <span class="event-location">📍 {{ eventDetails.event.location }}</span>
            </div>
          </div>
        </div>
      </div>

      <!-- Event Information -->
      <div class="container mt-5">
        <div class="row">
          <div class="col-lg-8">
            <!-- Description -->
            <div class="event-description">
              <h2>About This Event</h2>
              <p>{{ eventDetails.event.fullDescription || eventDetails.event.description }}</p>
            </div>

            <!-- Additional Images -->
            <div
              v-if="eventDetails.event.images && eventDetails.event.images.length > 0"
              class="event-gallery mt-4"
            >
              <h3>Event Gallery</h3>
              <div class="row">
                <div
                  v-for="(image, index) in eventDetails.event.images"
                  :key="index"
                  class="col-md-6 mb-3"
                >
                  <img :src="image" :alt="`Event image ${index + 1}`" class="gallery-image" />
                </div>
              </div>
            </div>
          </div>

          <div class="col-lg-4">
            <!-- Event Details Sidebar -->
            <div class="event-sidebar">
              <div class="sidebar-card">
                <h3>Event Details</h3>
                <div v-for="[label, value] in sidebarDetails" :key="label" class="detail-item">
                  <strong>{{ label }}:</strong> {{ value }}
                </div>
              </div>

              <!-- Action Buttons -->
              <div class="action-buttons mt-3">
                <button class="btn btn-primary btn-lg w-100 mb-2">Register Now</button>
                <button class="btn btn-outline-secondary w-100">Share Event</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.event-details {
  min-height: 100vh;
}

.loading {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 400px;
}

.error {
  margin: 20px;
  text-align: center;
}

.event-hero {
  position: relative;
  height: 500px;
  overflow: hidden;
}

.hero-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.hero-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(to bottom, rgba(0, 0, 0, 0.3), rgba(0, 0, 0, 0.7));
  display: flex;
  align-items: flex-end;
  padding: 40px;
}

.hero-content {
  color: white;
}

.event-title {
  font-size: 3rem;
  font-weight: bold;
  margin-bottom: 20px;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
}

.event-meta {
  display: flex;
  gap: 30px;
  font-size: 1.2rem;
}

.event-meta span {
  background: rgba(255, 255, 255, 0.2);
  padding: 8px 16px;
  border-radius: 20px;
  backdrop-filter: blur(10px);
}

.event-description {
  background: white;
  padding: 30px;
  border-radius: 10px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.event-description h2 {
  color: #006852;
  margin-bottom: 20px;
}

.event-description p {
  line-height: 1.6;
  color: #333;
  font-size: 1.1rem;
}

.event-gallery h3 {
  color: #006852;
  margin-bottom: 20px;
}

.gallery-image {
  width: 100%;
  height: 200px;
  object-fit: cover;
  border-radius: 10px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.event-sidebar {
  position: sticky;
  top: 20px;
}

.sidebar-card {
  background: white;
  padding: 30px;
  border-radius: 10px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.sidebar-card h3 {
  color: #006852;
  margin-bottom: 20px;
  border-bottom: 2px solid #f4e7ce;
  padding-bottom: 10px;
}

.detail-item {
  margin-bottom: 15px;
  padding-bottom: 10px;
  border-bottom: 1px solid #eee;
}

.detail-item:last-child {
  border-bottom: none;
  margin-bottom: 0;
}

.detail-item strong {
  color: #006852;
  display: inline-block;
  width: 100px;
}

.action-buttons .btn-primary {
  background-color: #006852;
  border-color: #006852;
}

.action-buttons .btn-primary:hover {
  background-color: #005544;
  border-color: #005544;
}

.action-buttons .btn-outline-secondary {
  color: #006852;
  border-color: #006852;
}

.action-buttons .btn-outline-secondary:hover {
  background-color: #006852;
  border-color: #006852;
  color: white;
}

@media (max-width: 768px) {
  .event-title {
    font-size: 2rem;
  }

  .event-meta {
    flex-direction: column;
    gap: 10px;
  }

  .hero-overlay {
    padding: 20px;
  }

  .event-description,
  .sidebar-card {
    padding: 20px;
  }
}
</style>
