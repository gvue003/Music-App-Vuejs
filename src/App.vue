<template>
  <div id="app">
    <header>
      <h1>My Music</h1>
    </header>
    <main>
      <section class="player">
        <h2 class="song-title">{{ current.title }} - <span>{{ current.artist }}</span></h2>
        <p class="song-duration">{{ formattedDuration }}</p>
        <div class="controls">
          <button class="prev" @click="prev">Prev</button>
          <button class="play" v-if="!isPlaying" @click="play">Play</button>
          <button class="pause" v-else @click="pause">Pause</button>
          <button class="next" @click="next">Next</button>
        </div>
      </section>
      <section class="playlist">
        <h3>The Playlist</h3>
        <button
          v-for="song in songs"
          :key="song.src"
          @click="play(song)"
          :class="(song.src === current.src) ? 'song playing' : 'song'"
        >
          {{ song.title }} - {{ song.artist }}
        </button>
      </section>
    </main>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';
import ArkanaEinSof from './assets/Arkana - Ein Sof.mp3';
import ConfettiHot from './assets/Confetti - Hot.mp3';

export default {
  name: 'app',
  setup() {
    const isPlaying = ref(false);
    const current = ref({
      title: 'Song Title',
      duration: 0
    });

    const songs = ref([
      {
        title: 'Ein Sof',
        artist: 'Arkana',
        src: ArkanaEinSof,
        duration: 0
      },
      {
        title: 'Hot',
        artist: 'Confetti',
        src: ConfettiHot,
        duration: 0
      }
    ]);

    const player = new Audio();
    let isPlayerReady = false;

    const play = (song) => {
      if (typeof song.src !== 'undefined') {
        current.value = song;
        player.src = current.value.src;
      }
      player.play();
      isPlaying.value = true;
    };

    const pause = () => {
      player.pause();
      isPlaying.value = false;
    };

    const prev = () => {
      const currentIndex = songs.value.findIndex(
        (song) => song.src === current.value.src
      );
      const prevIndex = (currentIndex - 1 + songs.value.length) % songs.value.length;
      play(songs.value[prevIndex]);
    };

    const next = () => {
      const currentIndex = songs.value.findIndex(
        (song) => song.src === current.value.src
      );
      const nextIndex = (currentIndex + 1) % songs.value.length;
      play(songs.value[nextIndex]);
    };

    player.addEventListener('canplay', () => {
      isPlayerReady = true;
      current.value.duration = player.duration;
    });

    player.addEventListener('timeupdate', () => {
      if (isPlayerReady) {
        current.value.duration = player.duration;
      }
    });

    onMounted(() => {
      current.value = songs.value[0];
      player.src = current.value.src;
    });

    return {
      current,
      songs,
      player,
      isPlaying,
      play,
      pause,
      prev,
      next,
      isPlayerReady
    };
  },
  computed: {
    formattedDuration() {
      if (!this.isPlayerReady || isNaN(this.current.duration)) {
        return '';
      }
      const duration = Math.floor(this.current.duration);
      const minutes = Math.floor(duration / 60);
      const seconds = duration % 60;
      return `${minutes}:${seconds < 10 ? '0' : ''}${seconds}`;
    }
  }
};
</script>


<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.song-duration {
  font-size: 14px;
  text-align: center;
  margin-top: 10px;
  color: #212121;
}

body {
  font-family: sans-serif;
}

header {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 15px;
  background-color: #212121;
  color: #fff;
}

main {
  width: 100%;
  max-width: 768px;
  margin: 0 auto;
  padding: 25px;
}

.song-title {
  color: #53565A;
  font-size: 32px;
  font-weight: 700;
  text-transform: uppercase;
  text-align: center;
}

.song-title span {
  font-weight: 400;
  font-style: italic;
}

.controls {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 30px 10px;
}

button {
  appearance: none;
  background: none;
  border: none;
  outline: none;
  cursor: pointer;
}

button:hover {
  opacity: 0.8;
}

.play, .pause {
  font-size: 20px;
  font-weight: 700;
  padding: 15px 25px;
  margin: 0px 15px;
  border-radius: 8px;
  color: #FFF;
  background-color: #cc2e5d;
}

.next, .prev {
  font-size: 16px;
  font-weight: 700;
  padding: 10px 20px;
  margin: 0px 15px;
  border-radius: 6px;
  color: #FFF;
  background-color: #ff58ff;
}

@media (max-width: 768px) {
  .controls {
    flex-direction: column;
    align-items: center;
    padding: 20px;
  }

  .play, .pause, .next, .prev {
    width: 100%;
  }
}

.playlist {
  padding: 0px 30px;
}

.playlist h3 {
  font-size: 28px;
  font-weight: 400;
  margin-bottom: 30px;
  color: #212121;
  text-align: center;
}

.playlist .song {
  display: block;
  width: 100%;
  padding: 15px;
  font-size: 20px;
  font-weight: 700;
  cursor: pointer;
}

.playlist .song:hover {
  color: #ff5858;
}

.playlist .song.playing {
  color: #fff;
  background: linear-gradient(to right, #cc2e5d, #ff5858);
}
</style>

