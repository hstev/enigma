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
              label="Password"
              v-model="state.password"
              hint="This is the password to decrypt the message"
              persistent-hint
            />

            <v-btn 
              prepend-icon="mdi-refresh"
              color="orange"
              @click="state.password = generateNewPassword()"
              density="compact"
            >Generate new one</v-btn>

            <v-textarea
              clearable
              counter
              :rules="[v => v.length <= 250 || 'Max 250 characters']"
              v-model="state.message"
              label="Message"
              class="mt-2"
            ></v-textarea>

            <v-textarea
              :rules="[v => v.length <= 250 || 'Max 250 characters']"
              label="Encrypted message"
              :counter="250"
              clearable
              v-model="state.encryptedMessage"
            ></v-textarea>

            <div>
              <v-btn 
              prepend-icon="mdi-lock"
              color="orange"
              @click="encryptMesssage"
              density="compact"
              class="mx-1"
            >Encrypt</v-btn>
            <v-btn 
              prepend-icon="mdi-lock-open-variant"
              color="orange"
              @click="decryptMesssage"
              density="compact"
              class="mx-1"
            >Decrypt</v-btn>
            </div>

            <v-text-field
              class="mt-3"
              label="Share"
              readonly
              type="url"
              v-model="shareUrl"
            />
            
            <v-checkbox v-model="state.includePassword" label="Include the password? (this will decrypt the message once open the link)" />
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>
  import {reactive, computed, onMounted} from 'vue';
  const availableCharacters = '0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ';
  const endpoint = 'https://hstev.github.io/enigma';

  const generateNewPassword = () => {
    const arr = availableCharacters.split('');
    return arr.sort((a, b) => 0.5 - Math.random()).join('').toLocaleLowerCase();
  }

  const encryptMesssage = () => {
    if (state.message) {
      const orderCharactersArray = (availableCharacters + " ").split("");
      const arrayMessage = state.message.toUpperCase().trim().split("");
      const arrayPassword = state.password.toUpperCase().split("");
      const encrypted = arrayMessage.map((character) => {
        const indexOrderCharacter = orderCharactersArray.indexOf(character)
        if (indexOrderCharacter >= 0) {
          const character = arrayPassword[indexOrderCharacter];
          if (character === undefined) {
            return "_"
          }
          return character;
        }
      })
      state.encryptedMessage = encrypted.join("");
    }
  }

  const decryptMesssage = () => {
    const orderCharactersArray = availableCharacters.split("");
    const arrayEncryptedMessage = state.encryptedMessage.toUpperCase().trim().split("");
    const arrayPassword = (state.password + "_").toUpperCase().split("");
    const decrypted = arrayEncryptedMessage.map((character) => {
      const indexPassword = arrayPassword.indexOf(character)
      if (indexPassword >= 0) {
        const character = orderCharactersArray[indexPassword];
        if (character === undefined) {
          return " "
        }
        return character;
      }
    })
    state.message = decrypted.join("").toLocaleLowerCase();
  }

  const shareUrl = computed(() => {
    if (state.encryptedMessage) {
      let textUrl = endpoint + "?t=" + state.encryptedMessage;
      if (state.includePassword) {
        textUrl += "." + state.password
      }
      return textUrl;
    }
    return endpoint;
  })

  const state = reactive({
    password: generateNewPassword(),
    message: null,
    encryptedMessage: null,
    url: endpoint,
    includePassword: false
  })

  onMounted(() => {
    const params = new Proxy(new URLSearchParams(window.location.search), {
      get: (searchParams, prop) => searchParams.get(prop),
    });
    
    if (params.t) {
      const content = params.t.split(".");
      if (content.length == 2) {
        state.encryptedMessage = content[0];
        state.password = content[1];
        decryptMesssage();
      }
      else {
        state.encryptedMessage = content[0];
      }
    }
  })
</script>


