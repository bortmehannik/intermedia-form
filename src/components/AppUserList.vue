<template>
  <v-list>
    <v-progress-linear
      color="deep-purple accent-4"
      indeterminate
      rounded
      height="6"
      v-if="isLoading">
    </v-progress-linear>
    <v-list-item
      v-for="order in orders"
      :key="order.id"
    >
      <v-tooltip top>
        <template v-slot:activator="{ on, attrs }">
          <v-list-item-icon>
            <v-icon
              :color="order.fullPrice > 1600 ? 'pink' : 'gray'"
              v-bind="attrs"
              v-on="on">
              mdi-star
            </v-icon>
          </v-list-item-icon>
        </template>
        <span>Buy more than $1600 licenses and get the King's Star!</span>
      </v-tooltip>

      <v-list-item-content>
        <strong>
          <v-list-item-title v-text="order.userName"></v-list-item-title>
        </strong>
      </v-list-item-content>

      <p class="mb-0">This user has purchased licenses for: <strong>{{ order.fullPrice }}</strong></p>

      <v-list-item-icon v-if="isAdmin">
        <v-icon
          color="red"
          @click="$emit('remove-order', order.id)">
          mdi-delete
        </v-icon>
      </v-list-item-icon>
    </v-list-item>
  </v-list>
</template>

<script>
export default {
  emits: ['remove-order'],
  props: {
    orders: Array,
    isAdmin: Boolean,
    isLoading: Boolean
  }
}
</script>
