<template>
  <router-view />
</template>

<script lang="ts">
import { Platform } from 'quasar';

import { defineComponent } from 'vue';
import { App } from '@capacitor/app';
import * as LiveUpdates from '@capacitor/live-updates';

export default defineComponent({
  name: 'App',
  mounted() {
    this.initializeApp();
  },
  methods: {
    async initializeApp() {
      if (!Platform.is.capacitor) return;
      // Register event to fire each time user resumes the app
      App.addListener('resume', async () => {
        console.log('localStorage', localStorage);

        if (localStorage.shouldReloadApp) {
          await LiveUpdates.reload();
        } else {
          const result = await LiveUpdates.sync();
          console.log('else result', result);

          localStorage.shouldReloadApp = result.activeApplicationPathChanged;
        }
      });

      // First sync on app load
      const result = await LiveUpdates.sync();
      console.log('ok result', result);

      localStorage.shouldReloadApp = result.activeApplicationPathChanged;
    },
  },
});
</script>
