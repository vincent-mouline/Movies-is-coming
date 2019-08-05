<template>
    <div id="app" class="container-fluid">
        <Navbar v-if="catReady" :categories="categories"
                v-on:SearchRequested="movieSearch"
                v-on:categorySorts="categorySorts"
                v-on:favoris="favoris"
                v-on:toSee="toSee"
                v-on:initialLoading="initialLoading"
                v-on:recommended="recommended"
                v-on:actually="actually"

        ></Navbar>
        <Modal v-if="showModal" @close="showModal = false" :trailer="trailer" :trailerTitle="trailerTitle"></Modal>
        <div class="row">
            <div class="mx-auto text-center">
                <h1 class="title flux">{{ title }}</h1>
            </div>
        </div>
        <div class="container">
            <div v-if="movieReady" class="justify-content-center">
                <Carousel :movies="movies"
                          v-on:filmography="filmography"
                          v-on:trailers="trailers"
                ></Carousel>
            </div>
            <div v-else class="justify-content-center loading">
                <pulse-loader :loading="loading" :size="size" :color="color"></pulse-loader>
            </div>
        </div>
    </div>
</template>

<script>

    let imdb = "https://api.themoviedb.org/3";
    let apiKey = "b8c8a4b3bbd7188f58cec6523ebdd9ce";
    let lang = "language=fr";

    import Navbar from "./components/Navbar";
    import Carousel from "./components/carousel";
    import ModalMovie from "./components/ModalMovie";
    import PulseLoader from 'vue-spinner/src/ClipLoader.vue';
    import VueNeonLight from 'vue-neon-light';
    import Modal from './components/modal';


    export default {
        name: "app",
        components: {
            Carousel,
            Navbar,
            ModalMovie,
            PulseLoader,
            VueNeonLight,
            Modal,
        },
        data() {
            return {
                movies: [],
                like: "favorite_border",
                categories: [],
                category: [],
                movieReady: false,
                catReady: false,
                color: "#ff0400",
                loading: true,
                size: '150px',
                font: './src/assets/fonts/beon-webfont.ttf ',
                flash: true,
                title: 'Tendances',
                showModal: false,
                trailer: String,
                trailerTitle: String,
            };
        },

        methods: {
            filmography(actor, name) {
                this.movieReady = false;
                fetch(imdb + "/discover/movie?api_key=" + apiKey + "&with_people=" + actor + "&" + lang)
                    .then((response) => {
                        return response.json()
                    })
                    .then((response) => {
                        this.movies = response;
                        this.movieReady = true;
                        this.title = 'filmographie de ' + name
                    })
            },
            movieSearch(query) {
                this.movieReady = false;
                fetch(imdb + "/search/movie?api_key=" + apiKey + "&query=" + query + "&" + lang)
                    .then((response) => {
                        return response.json()
                    })
                    .then((response) => {
                        this.movies = response;
                        this.movieReady = true;
                        this.title = 'Recherche'
                    })
            },
            categorySorts(category) {
                this.movieReady = false;
                fetch(imdb + "/discover/movie?api_key=" + apiKey + "&query=" + category.id +
                    "&" + lang + "&with_genres=" + category.id +
                    "&append_to_response=images&include_image_language=fr,null")
                    .then((response) => {
                        return response.json()
                    })
                    .then((json) => {
                        this.movies = json;
                        this.movieReady = true;
                        this.title = category.name

                    })
            },
            actually() {
                this.movieReady = false;
                fetch(imdb + "/movie/now_playing?api_key=" + apiKey +
                    "&append_to_response=images&include_image_language=fr&language=fr-FR&page=1&region=fr")
                    .then((response) => {
                        return response.json()
                    })
                    .then((json) => {
                        this.movies = json;
                        this.movieReady = true;
                        this.title = 'Actuellement en salle'

                    })
            },
            recommended() {
                this.movieReady = false;
                let movieLiked_json = localStorage.getItem('movieLiked');
                let movieLiked = JSON.parse(movieLiked_json);
                let movies = Object.entries(movieLiked);
                let result;
                result = Math.floor(Math.random() * Object.keys(movieLiked).length);
                console.log(result);
                fetch(imdb + "/movie/" + movies[result] + "/recommendations?api_key=" + apiKey +
                    "&append_to_response=images&include_image_language=fr&language=fr-FR&page=1&region=fr")
                    .then((response) => {
                        return response.json()
                    })
                    .then((json) => {
                        this.movies = json;
                        this.movieReady = true;
                        this.title = 'films recommandes'
                    })
            },
            favoris() {
                let movieLiked_json = localStorage.getItem('movieLiked');
                let movieLiked = JSON.parse(movieLiked_json);
                this.movieReady = false;
                let promises = [];
                for (var movie in movieLiked) {
                    promises.push(fetch(imdb + "/movie/" + movie + "?api_key=" + apiKey + "&" + lang)
                        .then((response) => {
                            return response.json()
                        }));
                }
                Promise.all(promises).then(results => {
                    this.movies['results'] = results;
                    this.movieReady = true;
                    this.title = 'Vos films favoris'
                });
            },
            toSee() {
                let movieToSee_json = localStorage.getItem('movieToSee');
                let movieToSee = JSON.parse(movieToSee_json);
                this.movieReady = false;
                let promises = [];
                for (var movie in movieToSee) {
                    promises.push(fetch(imdb + "/movie/" + movie + "?api_key=" + apiKey + "&" + lang)
                        .then((response) => {
                            return response.json()
                        }));
                }
                Promise.all(promises).then(results => {
                    this.movies['results'] = results;
                    this.movieReady = true;
                    this.title = 'Les films a voir'
                });
            },
            initialLoading() {
                this.movieReady = false;
                fetch(
                    imdb +
                    "/discover/movie?api_key=" +
                    apiKey +
                    "&sort_by=popularity.desc&" +
                    lang + "&append_to_response=images&include_image_language=fr,null&sort_by=popularity.desc&append_to_response=videos"
                )
                    .then(response => response.json())
                    .then(json => {
                        this.movies = json;
                        this.movieReady = true;
                        this.title = 'Tendances'
                    });
                {
                    fetch(imdb + "/genre/movie/list?api_key=" + apiKey + "&" + lang +
                        "&append_to_response=images&include_image_language=fr,null")
                        .then(response => response.json())
                        .then(json => {
                            this.categories = json;
                            this.catReady = true;
                        });
                }
            },
            trailers(movieId, title) {
                this.trailer = '';
                this.showModal = true;
                this.trailerTitle = title;
                let object;
                fetch(imdb + "/movie/" + movieId + "/videos?api_key=" + apiKey + "&" + lang)
                    .then((response) => {
                        return response.json()
                    })
                    .then((response) => {
                        object = response;
                        if (object.results === null || object.results < 1 ) {
                            this.trailersUS(movieId, title)
                        } else {
                            this.trailer = object.results[0].key;
                        }
                    })
            },
            trailersUS(movieId, title) {
                this.showModal = true;
                this.trailerTitle = title;
                let object;
                fetch(imdb + "/movie/" + movieId + "/videos?api_key=" + apiKey)
                    .then((response) => {
                        return response.json()
                    })
                    .then((response) => {
                        object = response;
                        if (object.results === null || object.results < 1) {
                            this.trailer = 'TQSVcvnm4yY';
                        } else {
                            this.trailer = object.results[0].key;
                        }
                        console.log(this.trailer);
                    })
            },
        },
        created() {
            this.initialLoading()
        }
    }
</script>

<style>
    @import url('https://fonts.googleapis.com/css?family=Caveat|Open+Sans|Bungee+Shade|Roboto&display=swap');
    @import url('https://fonts.googleapis.com/css?family=Monoton');

    @font-face {
        font-family: neon;
        src: url(https://s3-us-west-2.amazonaws.com/s.cdpn.io/707108/neon.ttf);
    }

    body {
        height: 100vh;
        background-color: rgba(0, 0, 0, 0.6);
        background-image: url("./assets/background.jpg");
        background-repeat: no-repeat;
        background-position: top;
        background-size: cover;
        background-blend-mode: multiply;
    }

    .loading {
        position: relative;
        size: 90px;
        top: 34vh;
    }


    /* title neon */


    @keyframes neon-title {
        0%,
        100% {
            text-shadow: 0 0 1vw #FA1C16, 0 0 3vw #FA1C16, 0 0 10vw #FA1C16, 0 0 10vw #FA1C16, 0 0 .4vw #FED128, .5vw .5vw .1vw #806914;
            color: #FED128;
        }
        50% {
            text-shadow: 0 0 .5vw #800E0B, 0 0 1.5vw #800E0B, 0 0 5vw #800E0B, 0 0 5vw #800E0B, 0 0 .2vw #800E0B, .5vw .5vw .1vw #40340A;
            color: #806914;
        }
    }

    .title {
        position: relative;
        /*top: 60px;*/
        margin-left: auto;
        margin-right: auto;
        text-transform: capitalize;
    }

    .neon-title {
        font-family: neon;
        color: #FB4264;
        font-size: 9vw;
        line-height: 9vw;
        text-shadow: 0 0 3vw #F40A35;
    }

    .neon-title {
        animation: neon 1s ease infinite;
        -moz-animation: neon 1s ease infinite;
        -webkit-animation: neon 1s ease infinite;
    }

    .flux {
        animation: flux 3s linear infinite;
        /*-moz-animation: flux 3s linear infinite;*/
        /*-webkit-animation: flux 3s linear infinite;*/
        /*-o-animation: flux 3s linear infinite;*/
    }

    .flux {
        font-family: neon;
        color: #426DFB;
        font-size: 3vw;
        line-height: 3vw;
        text-shadow: 0 0 3vw #2356FF;
    }


    @keyframes flux {
        0%,
        100% {
            text-shadow: 0 0 1vw #1041FF, 0 0 3vw #1041FF, 0 0 10vw #1041FF, 0 0 10vw #1041FF, 0 0 .4vw #8BFDFE, .5vw .5vw .1vw #147280;
            color: #fe162a;
        }
        50% {
            text-shadow: 0 0 .5vw #082180, 0 0 1.5vw #082180, 0 0 5vw #082180, 0 0 5vw #082180, 0 0 .2vw #082180, .5vw .5vw .1vw #0A3940;
            color: #800d28;
        }
    }


</style>
