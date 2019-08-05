<template>
    <div v-if="movies" class="carousel3d">
            <carousel-3d :controls-visible="true" :controls-prev-html="'&#10092'" :controls-next-html="'&#10093'"
                         :controls-width="40" :controls-height="0" :clickable="false" :height="600"
                         :autoplay="false" :autoplay-timeout="5000">
                <slide v-for="(movie, idx) in movies.results" :index="idx" v-bind:key="idx" class="my-auto">
                    <figure>
                    <CardMovie :movie="movie"
                               v-on:filmography="filmography"
                               v-on:trailers="trailers"
                    ></CardMovie>
                    </figure>
                </slide>
            </carousel-3d>
    </div>
    <div v-else>
        <p> aucun film a visualiser</p>
    </div>
</template>


<script>
    import CardMovie from "./CardMovie";
    import {Carousel3d, Slide} from 'vue-carousel-3d';

    export default {
        props: [
            'movies',
        ],
        components: {
            Carousel3d,
            Slide,
            CardMovie
        },
        methods: {
            filmography(actor, name) {
                this.$emit("filmography", actor, name)
            },
            trailers(movieId, title) {
                this.$emit("trailers", movieId, title);
            },
        }
    };

</script>

<style>

    .carousel-3d-container figure {
        margin:0;
    }

    .carousel-3d-container figcaption {
        background-color: rgba(0, 0, 0, 0);
        color: #fff;
        bottom: 0;
        padding: 15px;
        font-size: 12px;
        min-width: 100%;
        box-sizing: border-box;
    }

    .carousel-3d-slide {
        background-color: transparent !important;
        border: none !important;
    }

    .carousel-3d-container {
        margin: 0!important;
        position: relative;
        top: 10px;
        max-height: 560px;
    }

    .prev, .next {
        color: rgba(255, 7, 8, 0.63) !important;
    }
</style>