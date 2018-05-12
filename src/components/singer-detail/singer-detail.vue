<template>
    <transition name="slide">
        <music-list :songs="songs" :bgImage="bgImage" :title="title"></music-list>
    </transition>
</template>

<script>
import MusicList from 'components/music-list/music-list.vue'
import {mapGetters} from 'vuex'
import {getSingerDetail} from 'api/singer'
import {ERR_OK} from 'api/config'
import {createSong, isValidMusic, processSongsUrl} from 'common/js/song'

export default {
    data() {
        return {
            songs: []
        }
    },
    computed: {
        title() {
            return this.singer.name
        },
        bgImage() {
            return this.singer.avatar
        },
        ...mapGetters([
                'singer'
            ])
    },
    created() {
        this._getdetail()
        this.$nextTick(() => {
            console.log(this.songs)
        })
    },
    mounted() {
        window.vue = this
    },
    methods: {
        _getdetail() {
            if (!this.singer.id) {
                this.$router.push('/singer')
                return
            }
            getSingerDetail(this.singer.id).then((res) => {
                if (res.code === ERR_OK) {
                    processSongsUrl(this._normalizeSongs(res.data.list)).then((songs) => {
                        this.songs = songs
                    })
                }
            })
        },
        _normalizeSongs(list) {
            let ret = []
            list.forEach((item) => {
                let musicData = item.musicData
                if (isValidMusic(musicData)) {
                    ret.push(createSong(musicData))
                }
            })
            return ret
        }
    },
    components: {
        MusicList
    }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
    @import "~common/stylus/variable"

    .slide-enter-active,.slide-leave-active
        transition: all 0.2s
    .slide-enter,.slide-leave-to
        opacity: 0
</style>