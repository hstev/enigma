<template>
  <v-app>
    <v-main>
      <v-container fluid>
        <v-row>
          <v-col cols="12" md="8" lg="8" class="mx-auto">
            <v-breadcrumbs>
              <v-breadcrumbs-item href="https://hstev.github.io" title="hstev.github.io" />
              <v-breadcrumbs-divider></v-breadcrumbs-divider>
              <v-breadcrumbs-item :disabled="true" title="ENIGMA" />
            </v-breadcrumbs>

            <v-text-field
              label="Available characters"
              readonly
              v-model="availableCharacters"
              hint="These are the letters and number available for write a message"
              persistent-hint
            />
            <v-text-field
              label="Keyboard KEY"
              readonly
              v-model="keyboardHash"
              hint="This is the password to decrypt your message"
              persistent-hint
              class="mt-2"
            />
            <v-btn 
              prepend-icon="mdi-refresh"
              color="orange"
              class="mt-2"
              @click="regenerateKeyboard"
            >Generate</v-btn>


            <v-textarea
              class="mt-3"
              clearable
              counter
              :rules="[v => v.length <= 250 || 'Max 250 characters']"
              v-model="message"
              label="Message"
            ></v-textarea>

            <v-textarea
              class="mt-3"
              label="Encrypted message"
              clearable
            ></v-textarea>

            <v-text-field
              label="Share"
              readonly
              class="mt-2"
              type="url"
              v-model="shareUrl"
            />
            <v-checkbox label="Include the key?" />
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>
  import {ref} from 'vue';
  const availableCharacters = ref('0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ');
  let keyboardHash = ref('0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ');
  let message = ref('');
  let shareUrl = ref('https://hstev.github.io/enigma?t=');


  const regenerateKeyboard = () => {
    const chars = keyboardHash.value;
    const arr = chars.split('');
    keyboardHash = arr.sort((a, b) => 0.5 - Math.random()).join('');
  }
</script>


