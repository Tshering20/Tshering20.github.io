<template>
    <v-dialog
      v-model="dialog"
      max-width="290"
      persistent 
    >
      <v-card>
        <v-card-title class="headline">Overview</v-card-title>

        <v-card-text>
            <p v-for='text in dialogText' :key='text' style='font-weight: 500;'>{{text}}</p>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="#1ca9c9"
            text
            @click="$emit('closed')"
          >
            Continue
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
</template>

<script>
  export default {
    props: ['dialog', 'dialogText'],
    data () {
      return {
      }
    },
  }
</script>