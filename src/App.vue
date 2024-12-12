<template>
  <div id="app">
    <h1>Song List</h1>
    <div>
      <input v-model="searchTitle" placeholder="Search by title" />
      <input v-model="searchArtist" placeholder="Search by artist" />
      <select v-model="searchGenre">
        <option value="">All Genres</option>
        <option v-for="genre in uniqueGenres" :key="genre" :value="genre">
          {{ genre }}
        </option>
      </select>
    </div>
    <div class="checkbox_wrapper">
      <div v-for="key in availableKeys" :key="key" class="checkbox-item">
    <label :for="`checkbox-${key}`">
      <input
        type="checkbox"
        v-model="selectedKeys"
        :value="key"
        :id="`checkbox-${key}`"
      />
      {{ key }}
    </label>
  </div>
    </div>
    <table>
      <thead>
        <tr>
          <th v-for="key in selectedKeys" :key="key">
            {{ key }}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="song in fs" :key="song._id">
          <td v-for="key in selectedKeys" :key="key">
            {{ key == "ARTIST_ID" ? this.getArtistName(song[key]) : song[key] }}
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import songsData from "./data/songs.js";
import artistData from "./data/artists.js";

export default {
  data() {
    return {
      songs: songsData,
      artists: artistData,
      searchTitle: "",
      searchArtist: "",
      searchGenre: "",
      excl: ["_id", "artist_id", "GENRE", "YEAR"],
      selectedKeys: [],
    };
  },
  computed: {
    uniqueGenres() {
      return [...new Set(this.songs.map((song) => song.GENRE))].filter(Boolean);
    },
    fs() {
      return this.songs.filter((song) => {
        const artistName = this.getArtistName(song.ARTIST_ID).toLowerCase();
        return (
          (!this.searchTitle ||
            song.TITLE.toLowerCase().includes(
              this.searchTitle.toLowerCase()
            )) &&
          (!this.searchArtist ||
            artistName.includes(this.searchArtist.toLowerCase())) &&
          (!this.searchGenre || song.GENRE === this.searchGenre)
        );
      });
    },
    availableKeys() {
      if (!this.fs || this.fs.length === 0) {
        return [];
      }
      return Object.keys(this.fs[0]);
    },
  },
  methods: {
    getArtistName(artistId) {
      const artist = this.artists.find((a) => a._id === artistId);
      return artist ? artist.artist : "Unknown";
    },
  },
  mounted() {
    this.selectedKeys = this.availableKeys;
  },
};
</script>

<style>
/* Importing Google Fonts for Roboto */
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;500;700&display=swap');


.checkbox_wrapper {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-around;
}

body {
  font-family: 'Roboto', sans-serif;
  color: #ddd; /* Light grey text for contrast */
  background: 
    linear-gradient(45deg, #333 25%, transparent 25%) -50px 0,
    linear-gradient(-45deg, #333 25%, transparent 25%) -50px 0,
    linear-gradient(45deg, #444 25%, transparent 25%) 50px 0,
    linear-gradient(-45deg, #444 25%, transparent 25%) 50px 0;
  background-size: 100px 100px;
  background-color: #2f2f2f; /* Darker grey background */
}

input,
select {
  margin: 5px;
  padding: 10px;
  border: 1px solid #ff6600; /* Orange border */
  border-radius: 5px;
  background-color: #444; /* Dark grey input background */
  color: #ff6600; /* Orange text */
}

table {
  width: 100%;
  min-width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
  background-color: #333; /* Dark grey table background */
  border: 2px solid #ff6600; /* Orange border */
  border-radius: 10px; /* Rounded corners */
  overflow: hidden; /* Ensures rounded corners are visible */
}

th,
td {
  border: 1px solid #ff6600; /* Orange border */
  padding: 10px;
  text-align: left;
}

th {
  background-color: #ff6600; /* Orange background */
  color: #fff;
}

tbody tr:nth-child(odd) {
  background-color: #555; /* Darker grey background for odd rows */
}

tbody tr:nth-child(even) {
  background-color: #444; /* Slightly darker grey background for even rows */
}

tbody tr:hover {
  background-color: #666; /* Lighter grey hover effect */
}

</style>