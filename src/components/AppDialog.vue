<template>
  <v-dialog
    v-if="dialog"
    width="500">
    <template v-slot:activator="{ on, attrs }">
      <v-btn
        id="easter-egg"
        v-bind="attrs"
        v-on="on"
        v-show="easterEgg">
      </v-btn>
    </template>

    <v-card>
      <v-card-title class="text-h5 grey lighten-2">
        This is the cat admin
      </v-card-title>

      <v-card-text>
        It can add some functionality. Shall we ask him about it?
      </v-card-text>

      <v-divider></v-divider>

      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn
          color="primary"
          text
          @click="$emit('add')">
          Beg the cat!
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  emit: ['add'],
  props: ['easterEgg', 'dialog']
}
</script>
