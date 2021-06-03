<template>
  <div>
    <v-alert
      dense
      :type="alert.type"
      v-if="alert">
      {{ alert.text }}
      <button type="button"
              class="v-alert__dismissible v-btn v-btn--icon v-btn--round theme--dark v-size--small"
              @click="$emit('close')">
        <span class="v-btn__content">
          <i aria-hidden="true" class="v-icon notranslate mdi mdi-close-circle theme--dark"></i>
        </span>
      </button>
    </v-alert>
  </div>
</template>

<script>
export default {
  emits: ['close'],
  props: ['alert']
}
</script>
