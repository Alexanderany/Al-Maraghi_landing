<template>
  <div>
    <!-- Header, Hero, About, Services, Properties sections (same as before) -->
    <!-- ... -->
    <!-- Contact Section -->
    <section class="contact" id="contact">
      <div class="container">
        <div class="contact-content">
          <div class="contact-info animate-on-scroll">
            <h2>Ready to Find Your Dream Property?</h2>
            <!-- ... -->
            <div class="contact-details">
              <!-- ... -->
            </div>
          </div>
          <form class="contact-form animate-on-scroll" @submit.prevent="handleContactSubmit">
            <div class="form-group">
              <label for="name">Full Name *</label>
              <input type="text" v-model="form.name" required />
            </div>
            <div class="form-group">
              <label for="email">Email Address *</label>
              <input type="email" v-model="form.email" required />
            </div>
            <div class="form-group">
              <label for="phone">Phone Number *</label>
              <input type="tel" v-model="form.phone" required />
            </div>
            <div class="form-group">
              <label for="propertyType">Property Type</label>
              <select v-model="form.propertyType">
                <option value="">Select Property Type</option>
                <option value="apartment">Apartment</option>
                <option value="villa">Villa</option>
                <option value="penthouse">Penthouse</option>
                <option value="townhouse">Townhouse</option>
                <option value="commercial">Commercial</option>
                <option value="land">Land</option>
              </select>
            </div>
            <div class="form-group">
              <label for="budget">Budget Range</label>
              <select v-model="form.budget">
                <option value="">Select Budget Range</option>
                <option value="500k-1m">AED 500K - 1M</option>
                <option value="1m-2m">AED 1M - 2M</option>
                <option value="2m-5m">AED 2M - 5M</option>
                <option value="5m+">AED 5M+</option>
              </select>
            </div>
            <div class="form-group">
              <label for="message">Message</label>
              <textarea v-model="form.message" placeholder="Tell me about your property requirements..."></textarea>
            </div>
            <button type="submit" class="btn btn-primary" :disabled="loading">
              <i class="fas fa-paper-plane"></i> 
              {{ loading ? "Sending..." : "Send Message" }}
            </button>
          </form>
        </div>
      </div>
    </section>
    <!-- ... Footer & WhatsApp Button ... -->
  </div>
</template>

<script setup lang="ts">
// Import necessary composables/libraries
import { ref } from 'vue'

// Form state
const form = ref({
  name: "",
  email: "",
  phone: "",
  propertyType: "",
  budget: "",
  message: ""
})
const loading = ref(false)

// Handle contact form submission
const handleContactSubmit = async () => {
  loading.value = true

  // 1. Gmail Integration (send email to agent)
  await fetch("/api/send-gmail", {
    method: "POST",
    body: JSON.stringify(form.value),
    headers: { "Content-Type": "application/json" }
  })

  // 2. Supabase Integration (store lead)
  await fetch("/api/store-lead-supabase", {
    method: "POST",
    body: JSON.stringify(form.value),
    headers: { "Content-Type": "application/json" }
  })

  // 3. HubSpot Integration (create CRM contact/lead)
  await fetch("/api/hubspot-lead", {
    method: "POST",
    body: JSON.stringify(form.value),
    headers: { "Content-Type": "application/json" }
  })

  // 4. Optionally, WhatsApp redirect
  const whatsappMsg = `Hello, I'm interested in Dubai real estate. Name: ${form.value.name}, Email: ${form.value.email}, Phone: ${form.value.phone}, Type: ${form.value.propertyType}, Budget: ${form.value.budget}, Message: ${form.value.message}`
  window.open(`https://wa.me/971501234567?text=${encodeURIComponent(whatsappMsg)}`)

  alert("Thank you for your inquiry! You will be contacted soon.")
  loading.value = false
  form.value = { name: "", email: "", phone: "", propertyType: "", budget: "", message: "" }
}

// Vercel: Deploy this Vue file as part of your Vercel project for automatic hosting and SSL.
// Gmail: Implement /api/send-gmail on your backend (Node.js/Express or Vercel Serverless Function) using Gmail API.
// Supabase: Implement /api/store-lead-supabase using Supabase JS client (insert into leads table).
// HubSpot: Implement /api/hubspot-lead using HubSpot API (create contact/lead).
</script>

<style scoped>
/* ... Your original CSS goes here ... */
</style>