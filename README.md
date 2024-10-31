# Vue 3 Vertical Scroll Feed Component

A smooth, responsive, and interactive vertical scrolling feed component built with Vue 3, similar to TikTok or YouTube Shorts. This component features full-screen posts with smooth transitions, touch controls, and keyboard navigation.

## 🌟 Features

- Full-screen vertical scrolling feed
- Smooth transitions between posts
- Multiple navigation methods:
  - Mouse wheel scrolling
  - Touch swipe gestures
  - Keyboard arrow controls
- Responsive design with mobile optimization
- Dynamic content loading from API
- Like, Comment, and Share actions
- Lazy loading of images
- Pull-to-refresh prevention
- Safe area handling for notched phones
- Gradient overlay for better text visibility

## 🛠️ Technical Implementation

### Core Technologies
- Vue 3 Composition API
- CSS3 Animations
- Fetch API for data retrieval

### Key Components

#### State Management
```javascript
const posts = ref([]);              // Stores post data
const currentIndex = ref(0);        // Tracks current post index
const isScrolling = ref(false);     // Prevents scroll overlap
const touchStart = ref(0);          // Tracks touch gestures
```

#### Navigation Controls
The component implements three navigation methods:

1. **Wheel Scrolling**
```javascript
const handleScroll = (event) => {
  // Throttled scroll handling with 800ms delay
  // Vertical delta detection for direction
}
```

2. **Touch Controls**
```javascript
const handleTouchMove = (event) => {
  // 50px threshold for swipe detection
  // Directional swipe handling
}
```

3. **Keyboard Navigation**
```javascript
const handleKeyDown = (event) => {
  // Arrow key detection
  // Smooth transition between posts
}
```

### Animations and Transitions

The component uses CSS transforms and transitions for smooth animations:

```css
.posts-wrapper {
  transition: transform 0.8s cubic-bezier(0.16, 1, 0.3, 1);
  will-change: transform;
}
```

Post animations are handled through classes:
- `.active`: Current visible post
- `.prev`: Previous post (scaled and translated up)
- `.next`: Next post (scaled and translated down)

## 🚀 Getting Started

### Installation

1. Copy the component files into your Vue 3 project
2. Import the component:

```javascript
import VerticalFeed from './components/VerticalFeed.vue';
```

### Basic Usage

```vue
<template>
  <VerticalFeed />
</template>
```

### API Integration

The component expects data in the following format:

```javascript
{
  articles: [
    {
      title: string,
      description: string,
      urlToImage: string
      // ... other properties
    }
  ]
}
```

## ⚙️ Customization

### Styling

The component uses scoped CSS with customizable variables. Key areas for customization:

1. **Transition Timing**
```css
.posts-wrapper {
  transition: transform 0.8s cubic-bezier(0.16, 1, 0.3, 1);
}
```

2. **Gradient Overlay**
```css
.background-overlay {
  background: linear-gradient(to top, rgba(0, 0, 0, 0.9), transparent);
}
```

3. **Content Layout**
```css
.post-content {
  padding: 20px;
  /* Adjust spacing and layout */
}
```

### Performance Optimizations

1. **Scroll Throttling**
- 800ms delay between scroll actions
- Prevents animation overlap

2. **Image Loading**
- Lazy loading enabled for images
- Reduces initial load time

3. **Transform Optimizations**
- Uses `will-change: transform`
- Hardware acceleration for smooth animations

## 📱 Mobile Considerations

1. **Safe Areas**
```css
.post-content {
  bottom: env(safe-area-inset-bottom, 0px);
}
```

2. **Touch Actions**
```css
.feed-container {
  overscroll-behavior: none;
  touch-action: none;
}
```

3. **Responsive Design**
```css
@media (max-width: 768px) {
  /* Mobile-specific adjustments */
}
```

## ⚠️ Known Limitations

1. Current implementation requires full viewport height
2. Single-direction scrolling only
3. Limited to vertical orientation
4. Requires modern browser support for smooth animations

## 🤝 Contributing

Feel free to submit issues and enhancement requests!

## 📄 License

MIT License - feel free to use this component in your projects!