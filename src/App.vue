<template>
  <v-app id="app" :style="{background: bgColor}">
    <!-- Calling the component Alert and sending data to it. alert - an object with displayed values -->
    <app-alert
      :alert="alert"
      @close="alert = null">
    </app-alert>
    <div class="container">
      <div class="color-container">
        <p class="color-container__title mb-0">If you get tired of the gray background color, change it!</p>
        <!-- Calling the Select component to select a color scheme on the site -->
        <v-autocomplete
          v-model="bgColor"
          :items="colors"
        ></v-autocomplete>
      </div>

      <h2 class="mt-10 mb-5">We cooperate with the largest companies</h2>
      <!-- Calling a component with logos output. images - an object with image paths -->
      <app-list-logos :images="images" :get-by-key="getByKey" class="mb-10"></app-list-logos>

      <v-divider></v-divider>

      <h2 class="mt-15 mb-3">We cooperate with the largest companies</h2>
      <p class="more-partners mb-10">See how our partners look great in the slider.</p>
      <!-- Calling the SlickCarousel component. settings - slider parameters, images - object with image paths -->
      <vue-slick-carousel v-bind="settings" class="mb-16">
        <div
          class="slick-element"
          v-for="(image, i) in images"
          :key="i">
          <img
            :src="require('./assets/images/' + getByKey(images, image) + '.png')"
            :alt="image"
            class="slick-element__img">
        </div>
      </vue-slick-carousel>

      <v-divider></v-divider>

      <!-- Developing a calculator card -->
      <v-card
        outlined
        class="card-calculator">
        <img class="card-calculator__img" src="./assets/images/license.jpeg" alt="">
        <div class="card-calculator__content">
          <h2 class="align-left">Buy license:</h2>
          <v-form>

            <v-text-field
              label="Your name"
              v-model.trim="name">
            </v-text-field>
            <!-- Calling the RangeSlider component -->
            <v-slider
              v-model="value"
              v-on:input="updatePrice"
              :value="value"
              step="5"
              max="100"
              thumb-label>
            </v-slider>

            <p class="card-calculator__info">License cost: <strong>{{ price }}$</strong></p>
            <p class="card-calculator__info">Total price: <strong>{{ fullPrice }}$</strong></p>

            <div class="box-container align-right">
              <v-btn
                depressed
                :disabled="value === 0 || isLoading"
                color="success"
                @click="onSubmit">
                Order {{ value }} licenses
              </v-btn>
            </div>
          </v-form>
        </div>
      </v-card>

      <h3
        class="align-center mb-5"
        v-if="orders.length === 0">The list is still empty, but those who bought licenses will be displayed here!</h3>
      <!-- Calling a component with displaying users. removeOrder - gets the id of the clicked element,
      orders - an array of users, isAdmin - check for admin or not, isLoading - whether to start the loader or not. -->
      <app-user-list
        :orders="orders"
        class="mb-5"
        @remove-order="removeOrder"
        :is-admin="isAdmin"
        :is-loading="isLoading"
        v-else>
      </app-user-list>
    </div>
    <!-- Calling the Popup component. easterEgg - a flag that determines whether to show / not show the easter egg,
     dialog - whether the popup is displayed or not, addNewFunction - a function that adds a new function on click -->
    <app-dialog
      :easter-egg="easterEgg"
      :dialog="dialog"
      @add="addNewFunction">
    </app-dialog>
  </v-app>
</template>

<script>
import AppListLogos from './components/AppListOfLogos'
import AppAlert from './components/AppAlert'
import AppUserList from './components/AppUserList'
import AppDialog from './components/AppDialog'

import VueSlickCarousel from 'vue-slick-carousel'
import 'vue-slick-carousel/dist/vue-slick-carousel.css'
import 'vue-slick-carousel/dist/vue-slick-carousel-theme.css'
import axios from 'axios'

export default {
  name: 'App',
  mounted () {
    // Methods called immediately after loading the page
    this.updatePrice()
    this.loadOrders()
    this.activateEasterEgg()
  },
  data: () => ({
    name: '',
    value: 5,
    alert: null,
    price: 23,
    fullPrice: null,
    orders: [],
    easterEgg: false,
    dialog: true,
    isAdmin: false,
    isLoading: false,
    bgColor: '#f5f5f5',
    colors: ['#e8feff', '#f5f5f5', '#d7ffd9'],
    images: {
      'awards-tsia': 'Tsia',
      'awards-tsr': 'Tsr',
      'awards-comm-sol': 'Comm-Sol',
      'awards-unified-comm': 'Unified-Comm',
      'awards-pandemictech': 'Pandemictech',
      'awards-unified-comm-excel': 'Unified-comm-excel',
      'awards-training': 'Trainings',
      'awars-pc-mag': 'Pc-mag',
      'awards-jd-square': 'Jd-square'
    },
    settings: {
      dots: true,
      arrows: true,
      focusOnSelect: true,
      infinite: true,
      speed: 500,
      slidesToShow: 5,
      slidesToScroll: 3,
      touchThreshold: 5,
      responsive: [{
        breakpoint: 792,
        settings: {
          slidesToShow: 4,
          slidesToScroll: 2
        }
      }, {
        breakpoint: 576,
        settings: {
          slidesToShow: 2,
          slidesToScroll: 2,
          arrows: false
        }
      }]
    }
  }),
  methods: {
    getByKey (obj, value) {
      // Get keys from an object for use in image paths
      return Object.keys(obj).find(key => obj[key] === value)
    },
    onSubmit () {
      if (this.value === 0) {
        this.alert = {
          type: 'warning',
          text: 'You cannot buy 0 licenses'
        }
      } else if (this.name === '') {
        this.alert = {
          type: 'error',
          text: 'You forgot to enter your name'
        }
      } else {
        this.addOrder()
      }
    },
    async addOrder () {
      try {
        this.isLoading = true
        const url = 'https://intermedia-form-default-rtdb.firebaseio.com/orders.json'
        const { data } = await axios.post(url, {
          userName: this.name,
          fullPrice: this.fullPrice
        })
        this.orders.push({
          id: data.id,
          fullPrice: this.fullPrice,
          userName: this.name
        })
        this.alert = {
          type: 'success',
          text: `Hi, ${this.name}, it will cost you $${this.fullPrice}`
        }
        this.name = ''
        this.isLoading = false
      } catch (e) {
        this.alert = {
          type: 'error',
          text: e.message
        }
        this.isLoading = false
      }
    },
    async loadOrders () {
      try {
        this.isLoading = true
        const { data } = await axios.get('https://intermedia-form-default-rtdb.firebaseio.com/orders.json')
        if (!data) {
          throw new Error('No download data available')
        }
        this.orders = Object.keys(data).map(key => {
          return {
            id: key,
            ...data[key]
          }
        })
        this.isLoading = false
      } catch (e) {
        this.alert = {
          type: 'error',
          text: e.message
        }
        this.isLoading = false
      }
    },
    async removeOrder (id) {
      try {
        this.isLoading = true
        const name = this.orders.find(order => order.id === id).userName
        console.log(this.orders)
        await axios.delete(`https://intermedia-form-default-rtdb.firebaseio.com/orders/${id}.json`)
        this.orders = this.orders.filter(order => order.id !== id)
        this.alert = {
          type: 'success',
          text: `An order named ${name} has been deleted`
        }
        this.isLoading = false
      } catch (e) {
        this.alert = {
          type: 'error',
          text: e.message
        }
        this.isLoading = false
      }
    },
    activateEasterEgg () {
      setTimeout(() => {
        // Let's show the Easter egg after waiting 25 seconds
        this.easterEgg = true
      }, 25000)
    },
    async addNewFunction () {
      // A function that opens up new functionality for the user. It will work after activating the Easter egg.
      this.dialog = false
      this.isAdmin = true
      this.alert = {
        type: 'success',
        text: 'Hurray, the cat allowed to delete data from the database. Thanks to him! You won\'t see him again.'
      }
    },
    updatePrice () {
      this.value >= 50 ? this.price = 20 : this.price = 23
      this.fullPrice = this.value * this.price
    }
  },
  components: { VueSlickCarousel, AppListLogos, AppAlert, AppUserList, AppDialog }
}
</script>
