<template>
    <v-app dark>
        <v-content>
            <v-container>
                <player-title-bar></player-title-bar>
                <player-playlist-panel :playlist="playlist" @selecttrack="selectTrack" :selectedTrack="selectedTrack" @playtrack="play"></player-playlist-panel>
                <player-contols-bars @pauseTrack="pause" @playTrack="play" @stopTrack="stop" @skipTrack="skip"></player-contols-bars>
            </v-container>
        </v-content>
    </v-app>
</template>

<script>
import PlayerTitleBar from '@/components/PlayerTitleBar.vue'
import PlayerPlaylistPanel from '@/components/PlayerPlaylistPanel.vue'
import PlayerContolsBars from '@/components/PlayerContolsBars.vue'

export default {
  components: {
    PlayerTitleBar,
    PlayerPlaylistPanel,
    PlayerContolsBars
  },
  computed: {
    currentTrack () {
      return this.playlist[this.index]
    }
  },
  methods: {
    selectTrack (track) {
      this.selectedTrack = track
    },
    play (index) {
      alert(typeof index)
      if (typeof index !== 'number') {
        let selectedTrackIndex = this.playlist.findIndex(track => track === this.selectedTrack)
        index = selectedTrackIndex
      } else {
        this.selectTrack = this.playlist[index]
      }
      alert('Paly' + index)
    },
    pause () {
      alert('Pause!')
    },
    stop () {
      alert('Stop Music!')
    },
    skip (direction) {
      let idx = 0
      if (direction === 'next') {
        idx = this.index + 1
        if (idx >= this.playlist.length) {
          idx = 0
        }
      } else if (direction === 'prev') {
        idx = this.index - 1
        if (idx < 0) {
          idx = this.playlist.length - 1
        }
      }
      alert(idx)
      this.skipTo(idx)
    },
    skipTo (idx) {
      if (this.currentTrack) {
        alert('stop current music')
      }
      this.index = idx
      this.play(idx)
    }
  },
  data () {
    return {
      playlist: [
        { title: "Streets of Sant'Ivo", artist: 'Ask Again', howl: null, display: true },
        { title: 'Remember the Way', artist: 'Ask Again', howl: null, display: true },
        { title: 'hogefuga', artist: 'Ask Again', howl: null, display: true }
      ],
      selectedTrack: null,
      index: 0
    }
  }
}
</script>

<style scoped>
</style>
