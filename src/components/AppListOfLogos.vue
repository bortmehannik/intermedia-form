<template>
  <div class="list-logo">
    <img :src="require('../assets/images/' + getByKey(images, image) + '.png')"
         class="list-logo__img"
         v-for="(image, i) in images"
         :alt="image"
         :key="i">
  </div>
</template>

<script>
export default {
  props: ['images', 'getByKey']
}
</script>
