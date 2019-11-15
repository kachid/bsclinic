<template>
  <v-col>
    <v-card class="mx-auto" max-width="400" shaped hover>
      <v-card-title>
        <slot name="general" />
      </v-card-title>
      <v-divider inset></v-divider>
      <v-card-text class="body-1">
        <slot name="description" />
      </v-card-text>
    </v-card>
  </v-col>
</template>

<script>
export default {}
</script>
