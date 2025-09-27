1. Install dependencies

In your Vite Vue 3 project, install the PWA plugin:

npm install vite-plugin-pwa --save-dev

2. Configure Vite (vite.config.ts or vite.config.js)

Add the plugin and configure the PWA options:

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

3. Add icons

Place your PWA icons (e.g. pwa-192x192.png and pwa-512x512.png) inside the public/ folder.
That way they’re available at /pwa-192x192.png, /pwa-512x512.png.

4. Register Service Worker

In your main.ts, add this snippet after creating your Vue app:

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

5. Build & Test

Run dev mode:

npm run dev


Build for production:

npm run build
npm run preview


Open your app in Chrome, then check Application > Manifest and Service Workers in DevTools. You should see your PWA installable.