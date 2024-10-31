<script setup>
import { ref, onMounted, computed } from 'vue';

const posts = ref([]);
const currentIndex = ref(0);
const randomColors = ref([]);
const isScrolling = ref(false);
const touchStart = ref(0);
const lastScrollTime = ref(Date.now());

const currentTransform = computed(() => {
  return `translateY(-${currentIndex.value * 100}%)`;
});

const generateRandomColor = () => {
  const colors = ["#FFADAD", "#FFD6A5", "#FDFFB6", "#CAFFBF", "#9BF6FF", "#A0C4FF", "#BDB2FF", "#FFC6FF"];
  return colors[Math.floor(Math.random() * colors.length)];
};

const fetchPosts = async () => {
  try {
    const response = await fetch("https://saurav.tech/NewsAPI/top-headlines/category/health/in.json");
    const data = await response.json();
    posts.value = data.articles;
    randomColors.value = data.articles.map(() => generateRandomColor());
  } catch (error) {
    console.error("Error fetching posts:", error);
  }
};

// Prevent browser pull-to-refresh
const preventDefault = (e) => {
  e.preventDefault();
};

const handleScroll = (event) => {
  event.preventDefault();
  
  const now = Date.now();
  if (now - lastScrollTime.value < 800) return;
  lastScrollTime.value = now;

  if (isScrolling.value) return;
  
  const delta = event.deltaY;
  
  if (delta > 0 && currentIndex.value < posts.value.length - 1) {
    isScrolling.value = true;
    currentIndex.value++;
    setTimeout(() => { isScrolling.value = false; }, 800);
  } else if (delta < 0 && currentIndex.value > 0) {
    isScrolling.value = true;
    currentIndex.value--;
    setTimeout(() => { isScrolling.value = false; }, 800);
  }
};

const handleTouchStart = (event) => {
  touchStart.value = event.touches[0].clientY;
};

const handleTouchMove = (event) => {
  // Prevent default browser behavior
  event.preventDefault();
  
  if (isScrolling.value) return;
  
  const touchEnd = event.touches[0].clientY;
  const delta = touchStart.value - touchEnd;
  
  if (Math.abs(delta) < 50) return;
  
  if (delta > 0 && currentIndex.value < posts.value.length - 1) {
    isScrolling.value = true;
    currentIndex.value++;
    setTimeout(() => { isScrolling.value = false; }, 800);
  } else if (delta < 0 && currentIndex.value > 0) {
    isScrolling.value = true;
    currentIndex.value--;
    setTimeout(() => { isScrolling.value = false; }, 800);
  }
  
  touchStart.value = touchEnd;
};

const handleKeyDown = (event) => {
  if (isScrolling.value) return;
  
  if (event.key === 'ArrowDown' && currentIndex.value < posts.value.length - 1) {
    isScrolling.value = true;
    currentIndex.value++;
    setTimeout(() => { isScrolling.value = false; }, 800);
  } else if (event.key === 'ArrowUp' && currentIndex.value > 0) {
    isScrolling.value = true;
    currentIndex.value--;
    setTimeout(() => { isScrolling.value = false; }, 800);
  }
};

onMounted(async () => {
  await fetchPosts();
  window.addEventListener('keydown', handleKeyDown);
  
  // Add event listeners to prevent pull-to-refresh
  document.body.addEventListener('touchmove', preventDefault, { passive: false });
  
  // Clean up on component unmount
  onUnmounted(() => {
    window.removeEventListener('keydown', handleKeyDown);
    document.body.removeEventListener('touchmove', preventDefault);
  });
});
</script>

<template>
  <div 
    class="feed-container"
    @wheel="handleScroll"
    @touchstart="handleTouchStart"
    @touchmove="handleTouchMove"
  >
    <div 
      class="posts-wrapper"
      :style="{ transform: currentTransform }"
    >
      <div
        v-for="(post, index) in posts"
        :key="index"
        class="post-section"
        :class="{ 
          'active': index === currentIndex,
          'prev': index === currentIndex - 1,
          'next': index === currentIndex + 1
        }"
      >
        <!-- Background overlay gradient -->
        <div class="background-overlay"></div>
        
        <!-- Post image -->
        <div class="image-wrapper">
          <img
            :src="post.urlToImage"
            :alt="post.title"
            class="post-image"
            loading="lazy"
          />
        </div>
        
        <!-- Post content -->
        <div class="post-content">
          <div class="text-content">
            <h2 class="post-title">{{ post.title }}</h2>
            <p class="post-description">{{ post.description }}</p>
          </div>
          
          <!-- Action buttons -->
          <div class="action-buttons">
            <button class="action-button">
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="action-icon">
                <path d="M20.84 4.61a5.5 5.5 0 0 0-7.78 0L12 5.67l-1.06-1.06a5.5 5.5 0 0 0-7.78 7.78l1.06 1.06L12 21.23l7.78-7.78 1.06-1.06a5.5 5.5 0 0 0 0-7.78z"/>
              </svg>
              <span>Like</span>
            </button>
            <button class="action-button">
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="action-icon">
                <path d="M21 11.5a8.38 8.38 0 0 1-.9 3.8 8.5 8.5 0 0 1-7.6 4.7 8.38 8.38 0 0 1-3.8-.9L3 21l1.9-5.7a8.38 8.38 0 0 1-.9-3.8 8.5 8.5 0 0 1 4.7-7.6 8.38 8.38 0 0 1 3.8-.9h.5a8.48 8.48 0 0 1 8 8v.5z"/>
              </svg>
              <span>Comment</span>
            </button>
            <button class="action-button">
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="action-icon">
                <path d="M4 12v8a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2v-8"/>
                <polyline points="16 6 12 2 8 6"/>
                <line x1="12" y1="2" x2="12" y2="15"/>
              </svg>
              <span>Share</span>
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.feed-container {
  height: 100dvh; /* Use dynamic viewport height */
  overflow: hidden;
  position: relative;
  background: #000;
  overscroll-behavior: none; /* Prevent pull-to-refresh */
  touch-action: none; /* Disable browser touch actions */
}

.posts-wrapper {
  height: 100%;
  transition: transform 0.8s cubic-bezier(0.16, 1, 0.3, 1);
  will-change: transform;
}

.post-section {
  height: 100dvh; /* Use dynamic viewport height */
  position: relative;
  overflow: hidden;
}

.background-overlay {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 70%;
  background: linear-gradient(to top, rgba(0, 0, 0, 0.9), transparent);
  z-index: 2;
  pointer-events: none;
}

.image-wrapper {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1;
}

.post-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.post-content {
  position: absolute;
  bottom: env(safe-area-inset-bottom, 0px); /* Add safe area for notched phones */
  left: 0;
  right: 0;
  padding: 20px;
  z-index: 3;
  display: flex;
  justify-content: space-between;
  align-items: flex-end;
}

.text-content {
  flex: 1;
  padding-right: 80px;
  padding-bottom: env(safe-area-inset-bottom, 0px); /* Add safe area padding */
  transform: translateY(20px);
  opacity: 0;
  transition: all 0.5s ease;
}

.active .text-content {
  transform: translateY(0);
  opacity: 1;
}

.post-title {
  color: #fff;
  font-size: 1.2rem;
  font-weight: 600;
  margin-bottom: 8px;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.5);
}

.post-description {
  color: rgba(255, 255, 255, 0.9);
  font-size: 0.9rem;
  margin-bottom: 16px;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.5);
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.action-buttons {
  display: flex;
  flex-direction: column;
  gap: 20px;
  align-items: center;
  padding-bottom: env(safe-area-inset-bottom, 0px); /* Add safe area padding */
}

.action-button {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 4px;
  background: none;
  border: none;
  color: white;
  cursor: pointer;
  padding: 0;
}

.action-icon {
  background: rgba(255, 255, 255, 0.1);
  padding: 10px;
  border-radius: 50%;
  transition: all 0.3s ease;
}

.action-button:hover .action-icon {
  background: rgba(255, 255, 255, 0.2);
  transform: scale(1.1);
}

.action-button span {
  font-size: 12px;
  opacity: 0.8;
}

/* Animation classes */
.post-section.prev {
  transform: scale(0.9) translateY(-100%);
  opacity: 0;
}

.post-section.next {
  transform: scale(0.9) translateY(100%);
  opacity: 0;
}

/* Mobile optimizations */
@media (max-width: 768px) {
  .text-content {
    padding-right: 60px;
  }

  .post-title {
    font-size: 1rem;
  }

  .post-description {
    font-size: 0.8rem;
  }

  .action-icon {
    padding: 8px;
  }
}
</style>