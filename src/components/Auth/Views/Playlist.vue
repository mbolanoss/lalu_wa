<template>
    <div class="playlist">
        <div class="title"><h3 class="header">{{this.playlist_name}}</h3></div>

        <div class="songs" id="no-songs" v-if="this.playlist_songs.length===0">
            <div class="spinner-border" role="status" style = "color:var(--pink)">
            <span class="sr-only">Loading...</span>
        </div>
        </div>
        <div class="songs" v-else>
            <div class="row">
                <div class="song col-4" v-for="song in this.playlist_songs" :key="song._id">
                    <div class="content" @click="loadSongById(song)">
                        <div class="img-song"></div>
                        <div class="data">
                            <span class="song-title">{{song.title}}</span>
                            <span class="song-artist">{{song.artists[0]}}</span> 
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import gql from 'graphql-tag';
export default {
    name: 'Playlist',
    data(){
        return {
            playlist_name: 'Playlist',
            playlist_songs: [
                /* {
                    _id:'62718e54b9621e3a0066d49f',
                    title: 'Song #1',
                    artist: {
                        artist_name: 'Artist #1'
                    }
                },
                {
                    _id:'6272d9461aeac37be88a9846',
                    title: 'Song #2',
                    artist: {
                        artist_name: 'Artist #2'
                    }
                },
                {
                    _id:'6271940fb9621e3a0066d4a6',
                    title: 'Song #3',
                    artist: {
                        artist_name: 'Artist #3'
                    }
                } */
            ]
        }
    },
    methods:{
        loadSongById(song){
            this.$store.state.currentSong = song;
            this.$forceUpdate();
        }
    },
    mounted(){
        this.$apollo.query({
            query:gql`
                query Query($id: String!) {
                    getPlaylistById(_id: $id) {
                        playlist_name
                        playlist_songs {
                            _id
                            title
                            artists
                            album
                        }
                    }
                }
            `,
            variables:{
                "id":this.$route.params.id
            }
        })
        .then((data) =>{
            console.log(data)
            this.playlist_songs = data.data.getPlaylistById.playlist_songs
            this.playlist_name = data.data.getPlaylistById.playlist_name
        })
        .catch((error) =>{
            console.log(error)
        })
    }
}
</script>

<style scoped>
.playlist{
    padding: 1em 3em;
}
.title{
    width: 100%;
    height: 135px;
    background-color: var(--pink);
    border-radius: 25px;
    background: url('/images/playlist-bg.png');
    background-size: cover;

    display: flex;
    align-items: flex-end;
    align-content: flex-start;
}
.header{
    color: white;
    font-size: 2.5em;
    padding: 0.1em;
    padding-left: 1em;
    font-weight: 500;
}
#no-songs{
    display: flex;
    height: 150px;
    width: 100%;

    color: white;
    align-content: center;
    justify-content: center;

    line-height: 150px;
}
.songs{
    margin: 2em 0;
}
.song{
    padding:0.5em 2em;
}
.song .content{
    background-color: #1A1A1A;
    color: white;
    border-radius: 0.3em;

    display: flex;
    flex-direction: row;

    cursor: pointer;
}
.img-song{
    width: 4.5em;
    height: 4.5em;
    background-image: url('/images/songs_image.jpeg');
    background-size: contain;
    margin: 10px;
    border-radius: 0.3em;
}
.song .data{
    height: 100%;
    padding: 5px;
    
    font-size: 0.9em;
}
.song .data .song-title{
    margin-top: 10px;
    align-self: flex-start;
    margin-bottom: 10px;
    display: block;
}
.song .data .song-artist{
    align-self: flex-end;
    font-size: 0.8em;
    display: block;
}
</style>