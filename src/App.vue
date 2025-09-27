    <!-- App.vue or a dedicated component -->
    <template>
      <button v-if="installPrompt" @click="installPWA">Install App</button>
    </template>

    <script setup>
    import { ref, onMounted } from 'vue';

    const installPrompt = ref(null);

    onMounted(() => {
      window.addEventListener('beforeinstallprompt', (e) => {
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