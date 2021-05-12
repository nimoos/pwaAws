<template>
     <transition
        appear
        enter-active-class="animated fadeIn"
        leave-active-class="animated fadeOut"
      >
        <div
          v-if="showAppInstallBanner"
          class="banner-container bg-primary"
        >
          <div class="constrain">
            <q-banner
              class="bg-primary text-white"
              inline-actions
              dense
            >
              <template v-slot:avatar>
                <q-avatar
                  color="white"
                  icon="eva-camera-outline"
                  text-color="grey-10"
                  font-size="22px"
                />
              </template>

              <b>Install App?</b>

              <template v-slot:action>
                <q-btn
                  @click="installApp"
                  label="Yes"
                  class="q-px-sm"
                  dense
                  flat
                />
                <q-btn
                  @click="showAppInstallBanner = false"
                  label="Later"
                  class="q-px-sm"
                  dense
                  flat
                />
                <q-btn
                  @click="neverShowAppInstallBanner"
                  label="Never"
                  class="q-px-sm"
                  dense
                  flat
                />
              </template>
            </q-banner>
          </div>
        </div>
      </transition>
</template>

<script>

    export default {
  data () {
    return {
      defPrompt: {},
      showAppInstallBanner: false
    }
  },
  mounted() {
    console.log("Initializing setup on HomeScreen");
    let neverShowAppInstallBanner = this.$q.localStorage.getItem('neverShowAppInstallBanner')
    if (!neverShowAppInstallBanner) {
      window.addEventListener('beforeinstallprompt', (e) => {
        // Prevent the mini-infobar from appearing on mobile
        e.preventDefault();
        // Stash the event so it can be triggered later.
        this.defPrompt = e;
        console.log(e);
        // Update UI notify the user they can install the PWA
        setTimeout(() => {
          this.showAppInstallBanner = true
        }, 3000);
      });    
    }
  },
  methods: {
    installApp() {
      // Hide the app provided install promotion
      this.showAppInstallBanner = false
      // Show the install prompt
      console.log("The grail of colling another method of outside vue");
      this.defPrompt.prompt();
      // Wait for the user to respond to the prompt
      this.defPrompt.userChoice.then((choiceResult) => {
        if (choiceResult.outcome === 'accepted') {
          console.log('User accepted the install prompt');
          this.neverShowAppInstallBanner()
        } else {
          console.log('User dismissed the install prompt');
        }
      });
    },
    neverShowAppInstallBanner() {
      this.showAppInstallBanner = false
      this.$q.localStorage.set('neverShowAppInstallBanner', true)
    }
  },
    }
</script>

<style lang="scss" scoped>

</style>