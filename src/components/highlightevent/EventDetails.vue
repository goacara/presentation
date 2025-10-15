<script setup>
import { reactive, computed, onMounted, watch } from 'vue'
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

// Image loading state
const imageLoading = reactive({
  heroImage: true,
  heroImageError: false,
})

// Image loading handlers
function onHeroImageLoad() {
  imageLoading.heroImage = false
  imageLoading.heroImageError = false
}

function onHeroImageError() {
  imageLoading.heroImage = false
  imageLoading.heroImageError = true
}

// Functions
async function fetchEventDetails() {
  try {
    eventDetails.isLoading = true
    eventDetails.error = null
    // Reset image loading state
    imageLoading.heroImage = true
    imageLoading.heroImageError = false

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
        description: 'A month-long program exploring material transformation and relational art through exhibitions, talks, and performances.',
        fullDescription: 'Interlinked is a month-long exploration of material transformation and relational art, created through exhibitions, talks, and performances in collaboration with UNIMAS and international partners. This program highlights the intersection of creativity, innovation, and human connection in artistic expression.',
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
        description: 'A studio and gallery dedicated to preserving Sarawak’s songket weaving heritage with live demonstrations and handmade textiles.',
        fullDescription: 'Immerse yourself in Sarawak’s traditional weaving culture through the Songket Weaving Workshop and Crafts Gallery. Learn from master weavers, observe live demonstrations, and explore an extensive collection of handwoven textiles that represent centuries of cultural artistry.',
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
        description: 'A collection showcasing indigenous Sarawak crafts that connect tradition, culture, and sustainability.',
        fullDescription: 'Project Craft Connections highlights the craftsmanship of Sarawak’s indigenous artisans, blending tradition with modern sustainability. The exhibition showcases intricate handmade works that reflect deep cultural heritage and innovative preservation.',
        date: '1 Oct - 31 Oct 2025',
        time: 'All Day',
        location: 'Tanoti House',
        image: '/3.png',
        category: 'Culture, Arts & Heritage',
        organizer: 'Tanoti Sdn Bhd',
        price: 'FREE',
        images: ['/3.png']
      },
      {
        id: 4,
        title: 'WAK Food Adventure Trail 2.0',
        description: 'A culinary exploration of Kuching’s food scene featuring signature local dishes and hidden gems.',
        fullDescription: 'Join the WAK Food Adventure Trail 2.0 and experience Kuching’s vibrant culinary scene. Explore local eateries, uncover hidden gems, and savor dishes that define Sarawak’s diverse food culture.',
        date: '1 Oct - 31 Oct 2025',
        time: 'All Day',
        location: 'Various eateries across Kuching',
        image: '/4.png',
        category: 'Food & Markets',
        organizer: 'WAK Festival',
        price: 'Variable (by location)',
        images: ['/4.png']
      },
      {
        id: 5,
        title: 'WAK Stinky Toe Trail',
        description: 'An interactive trail celebrating Kuching’s art, culture, and street heritage.',
        fullDescription: 'The WAK Stinky Toe Trail invites you to explore Kuching’s vibrant cultural heritage through an interactive city trail that combines art, storytelling, and community spaces.',
        date: '1 Oct - 31 Oct 2025',
        time: 'All Day',
        location: 'Kuching City',
        image: '/5.png',
        category: 'Culture, Arts & Heritage',
        organizer: 'WAK Festival',
        price: 'FREE',
        images: ['/5.png']
      },
      {
        id: 6,
        title: 'All About Arts',
        description: 'A citywide showcase featuring visual artists, creative installations, and art workshops throughout Kuching.',
        fullDescription: 'All About Arts transforms Kuching into a citywide gallery, celebrating creativity through visual arts, installations, and workshops that bring together artists and art lovers alike.',
        date: '1 Oct - 31 Oct 2025',
        time: 'All Day',
        location: 'Various art venues',
        image: '/6.png',
        category: 'Culture, Arts & Heritage',
        organizer: 'WAK Festival',
        price: 'FREE',
        images: ['/6.png']
      },
      {
        id: 7,
        title: 'Celebrating Malaysia Through the Lens',
        description: 'A photography exhibition highlighting Malaysia’s landscapes, people, and stories through artistic perspectives.',
        fullDescription: 'Celebrating Malaysia Through the Lens showcases stunning photography that captures the nation’s landscapes, people, and stories, offering artistic reflections on culture and identity.',
        date: '1 Oct - 31 Oct 2025',
        time: 'All Day',
        location: 'Tegas Digital Village',
        image: '/7.png',
        category: 'Culture, Arts & Heritage',
        organizer: 'WAK Festival',
        price: 'FREE',
        images: ['/7.png']
      },
      {
        id: 8,
        title: 'Wild About Nature – Closer to Nature',
        description: 'A nature appreciation series including guided walks, talks, and eco-education activities.',
        fullDescription: 'Wild About Nature – Closer to Nature offers guided walks, engaging talks, and environmental education activities designed to deepen appreciation for Sarawak’s rich biodiversity and urban green spaces.',
        date: 'Ongoing throughout October 2025',
        time: 'All Day',
        location: 'Sama Jaya Forest Park',
        image: '/8.png',
        category: 'Wellness & Nature',
        organizer: 'WAK Festival x Nature Society',
        price: 'FREE',
        images: ['/8.png']
      },
      {
        id: 9,
        title: 'MATTA Malaysia Pickleball Grand Slam 2025 (MPG2025)',
        description: 'A national-level pickleball championship bringing together athletes from across Malaysia.',
        fullDescription: 'The MATTA Malaysia Pickleball Grand Slam 2025 (MPG2025) gathers top athletes nationwide for an exciting week of competitive matches, community spirit, and sporting excellence.',
        date: '13 Oct - 20 Oct 2025',
        time: 'All Day',
        location: 'Pikabol Kuching',
        image: '/9.png',
        category: 'Sports & Outdoor',
        organizer: 'Malaysia Pickleball Association',
        price: 'Spectator entry FREE',
        images: ['/9.png']
      },
      {
        id: 10,
        title: 'Pink it Up! Market Place',
        description: 'A bazaar celebrating Breast Cancer Awareness Month, featuring local artisans, fashion, and wellness brands.',
        fullDescription: 'Pink it Up! Market Place is a vibrant bazaar held over three weekends to promote Breast Cancer Awareness Month. Visitors can shop from local artisans, fashion designers, and wellness brands supporting a great cause.',
        date: '10 Oct - 12 Oct, 17 Oct - 19 Oct, 24 Oct - 26 Oct 2025',
        time: 'All Day',
        location: '2nd Floor, tHe Spring Shopping Mall',
        image: '/10.png',
        category: 'Food & Markets',
        organizer: 'The Spring Management Services Sdn Bhd & WAK Festival',
        price: 'FREE',
        images: ['/10.png']
      },
      {
        id: 11,
        title: 'Explore, Experience and Energize with LaVie!',
        description: 'A 4-week wellness journey exploring nutrition, hormones, breathing, and holistic health.',
        fullDescription: 'Join LaVie Wellness Sanctuary for a month-long program of mini workshops focused on nutrition, hormones, breathwork, and holistic living for improved health and balance.',
        date: '5 Oct, 12 Oct, 19 Oct, 26 Oct 2025',
        time: 'All Day',
        location: 'LaVie Wellness Sanctuary',
        image: '/11.png',
        category: 'Wellness & Nature',
        organizer: 'LaVie Wellness Sanctuary by Dr. Lowell Chai',
        price: 'RM39 per pax (inclusive of lunch & mini workshops)',
        images: ['/11.png']
      },
      {
        id: 12,
        title: 'WAK X Texhouse Artspace',
        description: 'A collaboration exhibit showcasing textile-based art and cultural designs inspired by Sarawak’s fabric traditions.',
        fullDescription: 'WAK X Texhouse Artspace features textile-based artworks inspired by Sarawak’s traditional fabrics, blending cultural craftsmanship with modern creative interpretation.',
        date: '1 Oct - 20 Oct 2025',
        time: 'All Day',
        location: 'Texhouse Artspace',
        image: '/12.png',
        category: 'Culture, Arts & Heritage',
        organizer: 'WAK Festival',
        price: 'FREE',
        images: ['/12.png']
      },
      {
        id: 13,
        title: 'The Unforgettable 1945: A Forest Timewalk for Peace',
        description: 'A guided storytelling walk through nature commemorating the legacy of peace and resilience post-World War II.',
        fullDescription: 'The Unforgettable 1945: A Forest Timewalk for Peace combines history and nature through guided storytelling walks honoring post-war resilience and peacebuilding.',
        date: '18 Oct - 20 Oct 2025',
        time: 'All Day',
        location: 'Sama Jaya Forest Park',
        image: '/13.png',
        category: 'Culture, Arts & Heritage',
        organizer: 'WAK Festival',
        price: 'FREE',
        images: ['/13.png']
      },
      {
        id: 14,
        title: 'Engkudu, Akar Penawar Landak and Natural Indigo Dye Workshop',
        description: 'A hands-on fabric dyeing workshop using natural plant-based dyes guided by local artisans.',
        fullDescription: 'This workshop introduces participants to natural fabric dyeing techniques using Engkudu, Akar Penawar Landak, and indigo plants, guided by renowned local artisans Ejah Jepon and Injan Lekong.',
        date: '18 Oct - 19 Oct 2025',
        time: 'All Day',
        location: '1st Floor, tHe Spring Shopping Mall',
        image: '/14.png',
        category: 'Culture, Arts & Heritage',
        organizer: 'WAK Festival',
        price: 'RM20 (inclusive of materials)',
        images: ['/14.png']
      },
      {
        id: 15,
        title: 'Tapestry: Harmonising Peranakan Cultures',
        description: 'An intercultural music workshop exploring the rhythms and melodies of Peranakan traditions.',
        fullDescription: 'Tapestry: Harmonising Peranakan Cultures is an intercultural music workshop that explores the rhythms, melodies, and performance traditions of Peranakan culture through collaborative art.',
        date: '18 Oct 2025',
        time: 'All Day',
        location: 'Telang Usan Hotel',
        image: '/15.png',
        category: 'Culture, Arts & Heritage',
        organizer: 'WAK Festival x Tapestry',
        price: 'RM35',
        images: ['/15.png']
      },
      {
        id: 16,
        title: 'An Evening with Baba G.T. Lye',
        description: 'A special theatre evening with the iconic Baba G.T. Lye sharing stories and performances from decades in Peranakan arts.',
        fullDescription: 'An Evening with Baba G.T. Lye presents a memorable night of storytelling, performance, and reflection by the iconic figure of Peranakan arts, celebrating his lifelong contribution to culture.',
        date: '19 Oct 2025',
        time: 'Evening',
        location: 'Old Courthouse',
        image: '/16.png',
        category: 'Culture, Arts & Heritage',
        organizer: 'WAK Festival x Tapestry',
        price: 'RM45 (inclusive of Makan Kecil)',
        images: ['/16.png']
      },
      {
        id: 17,
        title: 'All About Arts Exhibition',
        description: 'A curated exhibition showcasing multidisciplinary artworks from Sarawakian artists.',
        fullDescription: 'All About Arts Exhibition brings together multidisciplinary works by emerging and established Sarawakian artists in a month-long celebration of creativity and cultural expression.',
        date: '1 Oct - 31 Oct 2025',
        time: 'All Day',
        location: 'Old Courthouse Galleries',
        image: '/17.png',
        category: 'Culture, Arts & Heritage',
        organizer: 'WAK Festival',
        price: 'FREE',
        images: ['/17.png']
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

// Watch for route changes to refetch event details when navigating between events
watch(
  () => route.params.id,
  (newId) => {
    if (newId) {
      fetchEventDetails()
    }
  },
)
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
        <!-- Image loading state -->
        <div v-if="imageLoading.heroImage" class="hero-image-loading">
          <div class="spinner-border text-light" role="status">
            <span class="visually-hidden">Loading image...</span>
          </div>
        </div>
        
        <!-- Hero image with error handling -->
        <img 
          v-show="!imageLoading.heroImage && !imageLoading.heroImageError"
          :src="eventDetails.event.image" 
          :alt="eventDetails.event.title" 
          class="hero-image"
          @load="onHeroImageLoad"
          @error="onHeroImageError"
        />
        
        <!-- Fallback image on error -->
        <div v-if="imageLoading.heroImageError" class="hero-image-fallback">
          <i class="fas fa-image fa-3x text-muted"></i>
          <p class="text-muted mt-2">Image not available</p>
        </div>
        
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

.hero-image-loading {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #f8f9fa;
}

.hero-image-fallback {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background-color: #f8f9fa;
  color: #6c757d;
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
