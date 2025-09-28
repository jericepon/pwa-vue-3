
## 1. Install dependencies

In your Vite Vue 3 project, install the PWA plugin:

```bash
npm install vite-plugin-pwa --save-dev
```

## 2. Configure Vite (vite.config.ts or vite.config.js)

Add the plugin and configure the PWA options:

```ts
// vite.config.ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import { VitePWA } from 'vite-plugin-pwa'
 
export default defineConfig({
  plugins: [
    vue(),
    VitePWA({
      registerType: 'autoUpdate', // auto update service worker
      devOptions: {
        enabled: true, // enable PWA in dev mode
      },
      manifest: {
        name: 'My Vue 3 PWA',
        short_name: 'VuePWA',
        description: 'A Vue 3 + Vite Progressive Web App',
        theme_color: '#42b883',
        background_color: '#35495e',
        display: 'standalone',
        scope: '/',
        start_url: '/',
        icons: [
          {
            src: '/pwa-192x192.png',
            sizes: '192x192',
            type: 'image/png',
          },
          {
            src: '/pwa-512x512.png',
            sizes: '512x512',
            type: 'image/png',
          },
          {
            src: '/pwa-512x512.png',
            sizes: '512x512',
            type: 'image/png',
            purpose: 'any maskable',
          },
        ],
      },
    }),
  ],
})
```

## 3. Add icons

Place your PWA icons (e.g. pwa-192x192.png and pwa-512x512.png) inside the public/ folder.
That way they’re available at `/pwa-192x192.png`,` /pwa-512x512.png`.

## 4. Register Service Worker

In your `main.ts`, add this snippet after creating your Vue app:

```js
import { registerSW } from 'virtual:pwa-register'

const updateSW = registerSW({
  onNeedRefresh() {
    if (confirm("New content available. Reload?")) {
      updateSW(true)
    }
  },
  onOfflineReady() {
    console.log("App ready to work offline")
  },
})
```

## 5. Build & Test

Run dev mode:

```bash
npm run dev
```


Build for production:

```bash
npm run build
npm run preview
```

Open your app in Chrome, then check Application > Manifest and Service Workers in DevTools. You should see your PWA installable.

##  Add a Custom Install Button in Vue 3

Mobile Chrome & Edge let you catch the event `beforeinstallprompt` to show your own button.

#### 1. Create a composable (`usePwaInstall.ts`)

```vue
    <template>
      <button v-if="installPrompt" @click="installPWA">Install App</button>
    </template>

  

    <script setup>
    import { ref, onMounted } from 'vue';
    const installPrompt = ref(null);

    onMounted(() => {
      window.addEventListener('beforeinstallprompt', (e) => {
      console.log(e);
        e.preventDefault();
        installPrompt.value = e;
      });
    });

  

    const installPWA = () => {
      if (installPrompt.value) {

        installPrompt.value.prompt();

        installPrompt.value.userChoice.then((choiceResult) => {

          if (choiceResult.outcome === 'accepted') {

            console.log('User accepted the PWA installation');

          } else {

            console.log('User dismissed the PWA installation');

          }

          installPrompt.value = null; // Clear the prompt after use

        });

      }

    };

    </script>
```

---

#### 2. Use it in a component (e.g. `App.vue`)

```vue
  <script setup
	
	  lang="ts">  import { usePwaInstall } from './composables/usePwaInstall'  const { isInstallable, install } = usePwaInstall()

  </script>
	
	<template>
	
	  <div>
	
	    <h1>Hello Vue 3 PWA 🚀</h1> <button v-if="isInstallable" @click="install" class="install-btn"> Install App </button>
	
	  </div>
	
	</template>
	
	<style>
	
	 .install-btn {
	
	   padding: 10px 20px;
	
	   background: #42b883;
	
	   color: white;
	
	   border: none;
	
	   border-radius: 6px;
	
	 }
	
	</style>
```
