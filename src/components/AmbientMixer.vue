<template>
  <div class="ambient-mixer">
    <h1>Ambient Mixer</h1>
    <div class="master-controls">
      <div class="master-volume">
        <p>Master Volume</p>
        <input
          type="range"
          min="0"
          max="1"
          step="0.01"
          v-model="masterVolume"
          @input="updateMasterVolume"
        />
      </div>

      <button @click="toggleMaster">{{ isMasterPlaying ? 'Pause All' : 'Play All' }}</button>
    </div>
    <div v-for="(sound, index) in sounds" :key="index" class="sound-control">
      <p>{{ sound.name }}</p>

      <div class="soundsContainer">
        <div class="toggleContainer">
          <input type="checkbox" :id="'check-' + index" />
          <label :for="'check-' + index" class="buttonToggle" @click="toggleSound(index)"></label>
        </div>

        <input
          type="range"
          min="0"
          max="1"
          step="0.01"
          v-model="sound.volume"
          @input="updateVolume(index)"
        />
        <br />
      </div>
    </div>
  </div>
</template>

<script>
import { Howl, Howler } from 'howler' //Audio library for handling the audio files

export default {
  name: 'AmbientMixer',
  data() {
    return {
      masterVolume: 0.5,
      isMasterPlaying: true,
      sounds: [
        {
          name: 'Light rain',
          src: 'src/components/audio/light-rain.wav',
          volume: 0.5,
          howl: null,
          isPlaying: false
        },
        {
          name: 'Heavy rain',
          src: 'src/components/audio/heavy-rain.wav',
          volume: 0.5,
          howl: null,
          isPlaying: false
        },
        {
          name: 'Rain on roof',
          src: 'src/components/audio/rain-on-roof.wav',
          volume: 0.5,
          howl: null,
          isPlaying: false
        },
        {
          name: 'Light waves',
          src: 'src/components/audio/light-waves.wav',
          volume: 0.5,
          howl: null,
          isPlaying: false
        },
        {
          name: 'Strong waves',
          src: 'src/components/audio/strong-waves.wav',
          volume: 0.5,
          howl: null,
          isPlaying: false
        },
        {
          name: 'Campfire',
          src: 'src/components/audio/campfire.wav',
          volume: 0.5,
          howl: null,
          isPlaying: false
        },
        {
          name: 'Forest',
          src: 'src/components/audio/forest.wav',
          volume: 0.5,
          howl: null,
          isPlaying: false
        },
        {
          name: 'Coffee shop',
          src: 'src/components/audio/cafe-atmo.wav',
          volume: 0.5,
          howl: null,
          isPlaying: false
        }
        // More sounds can be added here
      ]
    }
  },
  methods: {
    toggleSound(index) {
      this.sounds[index].isPlaying = !this.sounds[index].isPlaying
      if (!this.isMasterPlaying) {
        // Only toggle isPlaying state if the master is paused
        return
      }
      if (!this.sounds[index].howl) {
        this.sounds[index].howl = new Howl({
          src: [this.sounds[index].src],
          volume: this.sounds[index].volume,
          loop: true
        })
      }
      if (this.sounds[index].isPlaying && !this.sounds[index].howl.playing()) {
        this.sounds[index].howl.play()
      } else if (!this.sounds[index].isPlaying && this.sounds[index].howl.playing()) {
        this.sounds[index].howl.pause()
      }
    },
    toggleMaster() {
      this.isMasterPlaying = !this.isMasterPlaying
      this.sounds.forEach((sound) => {
        if (!sound.howl) {
          sound.howl = new Howl({
            src: [sound.src],
            volume: sound.volume,
            loop: true
          })
        }

        // Play or pause based on master control and individual sound state
        if (this.isMasterPlaying && sound.isPlaying && !sound.howl.playing()) {
          sound.howl.play()
        } else if (!this.isMasterPlaying && sound.howl.playing()) {
          sound.howl.pause()
        }
      })
    },
    updateVolume(index) {
      if (this.sounds[index].howl) {
        this.sounds[index].howl.volume(this.sounds[index].volume)
      }
    },
    updateMasterVolume() {
      Howler.volume(this.masterVolume)
    }
  },
  mounted() {
    Howler.volume(this.masterVolume)

    // Start playing all sounds that are set to play
    this.sounds.forEach((sound, index) => {
      if (sound.isPlaying) {
        this.toggleSound(index)
      }
    })
  }
}
</script>

<style scoped>
.sound-control {
  margin-bottom: 20px;
}

.master-controls {
  margin-bottom: 30px;
  display: flex;
  align-items: center;
}

.master-volume {
  margin-right: 10px;
}
</style>
