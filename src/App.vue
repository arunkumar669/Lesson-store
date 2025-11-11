<template>
  <div id="app">
    <header class="app-header">
      <h1>Lesson Store</h1>
      <button 
        @click="isCartVisible = !isCartVisible"
        :disabled="cart.length === 0 && !isCartVisible"
        class="cart-button"
      >
        {{ isCartVisible ? '<< Back to Lessons' : 'View Cart' }} ({{ cart.length }})
      </button>
    </header>

    <main v-if="!isCartVisible" class="lessons-view">
      
      <div class="lesson-list">
        <div 
          v-for="lesson in lessons" 
          :key="lesson.id" 
          class="lesson-card"
        >
          <div class="lesson-details">
            <span class="lesson-icon">{{ lesson.icon }}</span> 
            <h3>{{ lesson.subject }}</h3>
            <p><strong>Location:</strong> {{ lesson.location }}</p>
            <p><strong>Price:</strong> ${{ lesson.price }}</p>
            <p>
              <strong>Spaces Left:</strong> 
              <span :style="{ color: lesson.spaces === 0 ? 'red' : 'green', fontWeight: 'bold' }">
                {{ lesson.spaces }}
              </span>
            </p>
          </div>
          <button
            class="add-to-cart-button"
            @click="addToCart(lesson)"
            :disabled="lesson.spaces === 0" >
            {{ lesson.spaces === 0 ? 'Out of Stock' : 'Add to Cart' }}
          </button>
        </div>
      </div>
    </main>

    <main v-else class="cart-view">
      <h2>🛒 Shopping Cart</h2>
      
      <div v-if="cart.length > 0">
        <div 
          v-for="item in cart"
          :key="item.id"
          class="cart-item"
        >
          <div class="cart-item-details">
            <span>{{ item.icon }} **{{ item.subject }}** in {{ item.location }}</span>
            <p>Price: **${{ item.price }}**</p>
          </div>
          <button class="remove-button" disabled>
            Remove
          </button>
        </div>

        <hr>

        <h3>Customer Information</h3>
        <p>Checkout form goes here...</p>

      </div>
      <p v-else>Your cart is empty.</p>
    </main>

  </div>
</template>

<script setup>
import { ref } from 'vue';

// --- INITIAL DATA STRUCTURE ---
const initialLessons = [
  { id: 1, subject: 'Math', location: 'London', price: 100, spaces: 5, icon: '📐' },
  { id: 2, subject: 'Science', location: 'Paris', price: 150, spaces: 5, icon: '🔬' },
  { id: 3, subject: 'English', location: 'London', price: 90, spaces: 5, icon: '📚' },
  { id: 4, subject: 'History', location: 'Berlin', price: 110, spaces: 5, icon: '📜' },
  { id: 5, subject: 'Art', location: 'Rome', price: 130, spaces: 5, icon: '🎨' },
  { id: 6, subject: 'Coding', location: 'Dubai', price: 200, spaces: 5, icon: '💻' },
  { id: 7, subject: 'Music', location: 'Tokyo', price: 120, spaces: 5, icon: '🎶' },
  { id: 8, subject: 'PE', location: 'London', price: 80, spaces: 5, icon: '⚽' },
  { id: 9, subject: 'Geography', location: 'Sydney', price: 95, spaces: 5, icon: '🌍' },
  { id: 10, subject: 'Drama', location: 'Paris', price: 105, spaces: 5, icon: '🎭' },
  { id: 11, subject: 'Yoga', location: 'London', price: 70, spaces: 0, icon: '🧘' },
];

const lessons = ref(initialLessons);
const cart = ref([]); 
const isCartVisible = ref(false); 

// --- METHOD: addToCart (STOCK DECREMENT LOGIC HERE) ---
function addToCart(lessonToAdd) {
  // 1. Find the lesson's index in the main list
  const lessonIndex = lessons.value.findIndex(l => l.id === lessonToAdd.id);
  
  // 2. Check if the lesson exists and has spaces left
  if (lessonIndex !== -1 && lessons.value[lessonIndex].spaces > 0) {
    // 3. DECREMENT STOCK
    lessons.value[lessonIndex].spaces--;
    
    // 4. Add the lesson to the cart
    cart.value.push({ ...lessonToAdd });
  }
}
</script>

<style scoped>
/* Basic CSS for the static layout */
#app {
  font-family: Arial, sans-serif;
  padding: 20px;
}
.app-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  border-bottom: 2px solid #ccc;
  padding-bottom: 10px;
}
h1 {
    font-size: 2.2em;
    color: #333;
}
h2 {
    border-bottom: 2px solid #4CAF50;
    padding-bottom: 5px;
    margin-bottom: 20px;
}
.lesson-list {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(230px, 1fr));
  gap: 20px;
  margin-top: 20px;
  width: 100%; 
}
.lesson-card {
  border: 1px solid #ddd;
  padding: 15px;
  border-radius: 8px;
  height: 100%; 
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.05);
  transition: transform 0.2s;
}
.lesson-card:hover {
    transform: translateY(-3px);
    box-shadow: 4px 4px 10px rgba(0, 0, 0, 0.1);
}
.lesson-details h3 {
    color: #007bff;
    margin-top: 5px;
    margin-bottom: 10px;
    font-size: 1.3em;
}
.lesson-icon {
    font-size: 2.5em;
    display: block;
    margin-bottom: 5px;
}
.cart-button {
  padding: 10px 15px;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
.cart-button:disabled {
  background-color: #aaa;
  cursor: not-allowed;
}
.add-to-cart-button {
  margin-top: 10px;
  padding: 8px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
.add-to-cart-button:hover:not([disabled]) {
    background-color: #0056b3;
}
.add-to-cart-button[disabled] {
    background-color: #ccc;
    cursor: not-allowed;
}
/* --- Cart View Styles --- */
.cart-view {
    max-width: 600px;
    margin: 0 auto;
}
.cart-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px 0;
    border-bottom: 1px dashed #eee;
}
.cart-item:last-of-type {
    border-bottom: none;
    margin-bottom: 20px;
}
.remove-button {
    background-color: #dc3545;
    color: white;
    border: none;
    padding: 5px 10px;
    border-radius: 4px;
    cursor: not-allowed;
}
</style>