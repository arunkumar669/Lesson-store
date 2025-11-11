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
            :disabled="lesson.spaces === 0"
          >
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
            <span>{{ item.icon }} {{ item.subject }} in {{ item.location }}</span>
            <p>Price: ${{ item.price }}</p>
          </div>
          <button @click="removeFromCart(item.id)" class="remove-button">
            Remove
          </button>
        </div>

        <hr>

        <h3>Customer Information</h3>
        <form @submit.prevent="submitCheckout" class="checkout-form">
          <div class="form-group">
            <label for="name">Name:</label>
            <input type="text" id="name" v-model="checkout.name" required>
          </div>
          <div class="form-group">
            <label for="phone">Phone:</label>
            <input type="tel" id="phone" v-model="checkout.phone" pattern="[0-9]*" required>
          </div>
          
          <button 
            type="submit" 
            :disabled="!isFormValid"
            class="submit-button"
          >
            Place Order
          </button>
          <p v-if="!isFormValid" class="validation-message">Please fill in both name and a numeric phone number.</p>
        </form>

      </div>
      <p v-else>Your cart is empty.</p>
    </main>

  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

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

// --- NEW STATE FOR CHECKOUT FORM ---
const checkout = ref({
  name: '',
  phone: ''
});

// --- COMPUTED PROPERTY FOR FORM VALIDATION ---
const isFormValid = computed(() => {
  // Check if name is not empty and phone contains only digits (basic validation)
  return checkout.value.name.trim() !== '' && /^\d+$/.test(checkout.value.phone);
});

// --- METHODS (FUNCTIONS) ---

/**
 * Adds a lesson to the cart and decrements its space count.
 * @param {Object} lessonToAdd - The lesson object to add.
 */
function addToCart(lessonToAdd) {
  // 1. Decrement the space count for the lesson
  const lessonIndex = lessons.value.findIndex(l => l.id === lessonToAdd.id);
  if (lessonIndex !== -1 && lessons.value[lessonIndex].spaces > 0) {
    lessons.value[lessonIndex].spaces--;
    
    // 2. Add the lesson (or a copy of it) to the cart
    // Note: In a real app, you'd manage quantities. Here, we add the whole object.
    cart.value.push({ ...lessonToAdd });
  }
}

/**
 * Removes a lesson from the cart and increments the space count.
 * @param {number} lessonId - The ID of the lesson to remove.
 */
function removeFromCart(lessonId) {
  // 1. Find and remove the first instance of the item from the cart
  const cartIndex = cart.value.findIndex(item => item.id === lessonId);
  if (cartIndex !== -1) {
    cart.value.splice(cartIndex, 1);
    
    // 2. Increment the space count in the lessons list
    const lessonIndex = lessons.value.findIndex(l => l.id === lessonId);
    if (lessonIndex !== -1) {
      lessons.value[lessonIndex].spaces++;
    }
  }
}

/**
 * Handles the submission of the checkout form.
 */
function submitCheckout() {
  if (isFormValid.value) {
    console.log("Order Placed!");
    console.log("Customer:", checkout.value.name, "Phone:", checkout.value.phone);
    console.log("Items:", cart.value.map(item => item.subject));
    
    // Alert the user and reset the state
    alert(`Order placed successfully for ${cart.value.length} lessons! Thank you, ${checkout.value.name}.`);
    
    // Clear the cart and reset the form
    cart.value = [];
    checkout.value.name = '';
    checkout.value.phone = '';
    isCartVisible.value = false; // Go back to the lessons view
  } else {
    alert('Please complete the form correctly.');
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
    /* Added style for the Cart View header */
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
    cursor: pointer;
}
.remove-button:hover {
    background-color: #c82333;
}
/* --- Checkout Form Styles --- */
.checkout-form {
    padding: 15px;
    border: 1px solid #ddd;
    border-radius: 5px;
    background-color: #f9f9f9;
}
.form-group {
    margin-bottom: 15px;
}
.form-group label {
    display: block;
    margin-bottom: 5px;
    font-weight: bold;
}
.form-group input {
    width: 100%;
    padding: 8px;
    border: 1px solid #ccc;
    border-radius: 4px;
    box-sizing: border-box; /* Ensures padding doesn't affect overall width */
}
.submit-button {
    padding: 10px 20px;
    background-color: #4CAF50;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    font-weight: bold;
}
.submit-button:hover:not([disabled]) {
    background-color: #45a049;
}
.submit-button[disabled] {
    background-color: #aaa;
    cursor: not-allowed;
}
.validation-message {
    color: #dc3545;
    margin-top: 10px;
    font-size: 0.9em;
}
</style>