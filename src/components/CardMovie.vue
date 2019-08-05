<template>
    <div id="card">
        <vue-flip :active-click="true" class="movie-card mx-auto" width="18rem">

            <div slot="front" class="card text-white mx-auto my-4" v-on:click="getActors(movie.id)">
                <img class="card-img-top" v-bind:src="poster(movie)"/>
                <div class="card-body p-0 text-center">
                    <h2 class="card-title my-2 ">{{truncate(movie.title, 26)}}</h2>
                    <button class="liked mx-5">
                        <i class="material-icons" v-on:click.stop="likeIt(movie.id)"
                           v-text="liked(movie.id)"></i>
                    </button>
                    <button class="see mx-5">
                        <i class="material-icons" v-on:click.stop="seeIt(movie.id)"
                           v-text="toSee(movie.id)"></i>
                    </button>
                    <p class="card-description vote-average mb-2">{{ movie.vote_average }} / 10</p>
                </div>
            </div>

            <div slot="back" class="card">
                <div class="card-header">
                    <h2 class="card-title mx-2 my-2 text-center">{{movie.title}}</h2>
                    <ul class="nav nav-tabs nav-fill" id="movie-list" role="tablist">
                        <li class="nav-item">
                            <a class="nav-link active show"
                               @click="tabLinkEvent" ref="tabLink"
                               v-bind:href="'#film' + movie.id" role="tab"
                               v-bind:id="'film' + movie.id + 'tab'"
                               v-bind:aria-controls="'film' + movie.id"
                               data-toggle="tab" aria-selected="true">Film</a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link"
                               @click="tabLinkEvent"
                               ref="tabLink"
                               v-bind:href="'#actors' + movie.id" role="tab"
                               v-bind:id="'actors' + movie.id + 'tab'"
                               v-bind:aria-controls="'actors' + movie.id "
                               data-toggle="tab" aria-selected="false">Acteurs</a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link"
                               @click="tabLinkEvent" ref="tabLink"
                               v-bind:href="'#notes' + movie.id" role="tab"
                               v-bind:id="'notes' + movie.id + 'tab'"
                               v-bind:aria-controls="'notes' + movie.id"
                               data-toggle="tab" aria-selected="false">Notes</a>
                        </li>
                    </ul>
                </div>

                <div class="tab-content mt-3">
                    <div class="tab-pane active"
                         v-bind:id="'film' + movie.id" role="tabpanel"
                         v-bind:aria-labelledby="'#film' + movie.id + 'tab'">
                        <div class="card-text actor-div scrollbar">
                            <p class="mx-2 my-2 text">
                                {{ movie.overview ? movie.overview : 'Aucun synopsis disponible' }}</p>
                            <p class="synopsis mx-2 mt-3 text-center actor-info">Date de sortie :
                                <span class="text">{{movie.release_date ? dateFormat(movie) : 'Aucune date fournie'}}</span>
                            </p>
                        </div>
                    </div>

                    <div class="tab-pane"
                         v-bind:id="'actors' + movie.id" role="tabpanel"
                         v-bind:aria-labelledby="'#actors' + movie.id + 'tab'">
                        <div class="card-text row actor-div scrollbar">
                            <div v-if="actorsReady" class="col-6"
                                 v-for="cast in actors.cast">
                                <img v-bind:src="actor(cast)"
                                     @click.stop="filmography(cast.id, cast.name)"
                                     class="actor-picture mt-2">
                                <div class="actor-info-background">
                                    <div class=" mt-0 pb-2 actor-info text-center" v-text="cast.character"></div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div class="tab-pane"
                         v-bind:id="'notes' + movie.id" role="tabpanel"
                         v-bind:aria-labelledby="'#notes' + movie.id + 'tab'">
                        <div class="card-text text-center">
                            <textarea
                                    v-bind:id="'text-' + movie.id"
                                    @click.stop="selectText(movie.id)"
                                    @input="updateNotes($event.target.value, movie.id)"
                                    v-model="notes"
                                    class="mx-auto text-center mx-0 px-1 notes-textarea scrollbar"
                                    cols="29"
                                    rows="11"
                            ></textarea>
                        </div>
                    </div>

                </div>
                <div class="card-footer text-center">
                    <a href="" id="show-modal"  class="btn btn-primary trailer neon"
                            v-on:click.prevent.stop="trailers(movie.id, movie.title)"
                    >
                        Trailer
                    </a>
                </div>
            </div>
        </vue-flip>
    </div>
</template>

<script>
    import VueFlip from "vue-flip";

    export default {
        data() {
            return {
                like: String,
                see: String,
                actorsReady: false,
                actors: [],
                notes: '',

            }
        },
        props: {
            movie: Object
        },
        components: {
            VueFlip,
        },
        methods: {
            filmography(actor, name) {
                this.$emit("filmography", actor, name)
            },
            selectText: function (movieId) {
                const input = document.getElementById('text-' + movieId);
                input.focus();
            },
            likeIt: function (movieId) {
                let movieLiked_json = localStorage.getItem('movieLiked');
                let movieLiked = JSON.parse(movieLiked_json);
                if (movieLiked === null) {
                    movieLiked = {};
                }
                if (!movieLiked[movieId]) {
                    movieLiked[movieId] = true;
                } else {
                    delete movieLiked[movieId];
                }
                movieLiked_json = JSON.stringify(movieLiked);
                localStorage.setItem('movieLiked', movieLiked_json);
                this.liked(movieId);
            }
            ,
            liked: function (movieId) {
                let movieLiked_json = localStorage.getItem('movieLiked');
                let movieLiked = JSON.parse(movieLiked_json);
                if (movieLiked === null) {
                    movieLiked = {};
                }
                if (movieLiked[movieId]) {
                    this.like = 'favorite'
                } else {
                    this.like = 'favorite_border'
                }
                return this.like;
            }
            ,
            seeIt: function (movieId) {
                let movieToSee_json = localStorage.getItem('movieToSee');
                let movieToSee = JSON.parse(movieToSee_json);
                if (movieToSee === null) {
                    movieToSee = {};
                }
                if (!movieToSee[movieId]) {
                    movieToSee[movieId] = true;
                } else {
                    delete movieToSee[movieId];
                }
                movieToSee_json = JSON.stringify(movieToSee);
                localStorage.setItem('movieToSee', movieToSee_json);
                this.toSee(movieId);
            }
            ,
            toSee: function (movieId) {
                let movieToSee_json = localStorage.getItem('movieToSee');
                let movieToSee = JSON.parse(movieToSee_json);
                if (movieToSee === null) {
                    movieToSee = {};
                }
                if (movieToSee[movieId]) {
                    this.see = 'add_circle'
                } else {
                    this.see = 'add_circle_outline'
                }
                return this.see;
            }
            ,
            poster: function (movie) {
                if (!movie.poster_path) {
                    return '../assets/default_poster.jpg'
                } else {
                    return 'http://image.tmdb.org/t/p/w780' + movie.poster_path
                }
            }
            ,
            actor: function (cast) {
                if (!cast.profile_path) {
                    return '../assets/default_actor.jpg'
                } else {
                    return 'http://image.tmdb.org/t/p/w185' + cast.profile_path
                }
            }
            ,
            dateFormat: function (movie) {
                let date = movie.release_date;
                date = date.split("-").reverse().join("/");
                return date;
            }
            ,
            tmdbHref: function (movie) {
                href = 'https://www.themoviedb.org/movie/' + movie.id;
                return href;
            }
            ,
            truncate: function (value, size) {
                if (value.length > size) {
                    value = value.substring(0, size) + '...';
                }
                return value
            }
            ,
            tabLinkEvent() {
                const elem = this.$refs.tabLink;
                elem.click()
            },
            trailers(movieId, title) {
                this.$emit("trailers", movieId, title);
            },
            getActors: function (movieId) {
                fetch(
                    "https://api.themoviedb.org/3/movie/" + movieId +
                    "/credits?api_key=b8c8a4b3bbd7188f58cec6523ebdd9ce"
                )
                    .then(response => response.json())
                    .then(json => {
                        this.actors = json;
                        this.actorsReady = true;
                    });

                let movieNoted_json = localStorage.getItem('movieNoted');
                let movieNoted = JSON.parse(movieNoted_json);
                if (movieNoted === null) {
                    movieNoted = {};
                }
                this.notes = movieNoted[movieId];
            }
            ,
            updateNotes: function (value, movieId) {
                let movieNoted_json = localStorage.getItem('movieNoted');
                let movieNoted = JSON.parse(movieNoted_json);
                if (movieNoted === null) {
                    movieNoted = {};
                }
                movieNoted[movieId] = value;

                movieNoted_json = JSON.stringify(movieNoted);
                localStorage.setItem('movieNoted', movieNoted_json);
            }
        }
    }
</script>

<style>

    .bg-dark {
        background: rgba(40, 40, 40, 0.9) !important;
    }

    .movie-card {
        font-family: 'Open sans', sans-serif;
    }

    .card-img-top {
        height: 400px;
    }

    h2 {
        font-size: 0.5em;
    }

    .card {
        width: 18rem;
        max-height: 33rem;
        color: white;
        background: rgba(46, 46, 46, 0.8);
    }

    .card .card-title {
        font-size: 18px;
        font-weight: bold;
    }

    .card-text {
        height: 290px;
    }

    .actor-div {
        margin-left: 1px;
        height: 290px;
        max-height: 290px;
        overflow: auto;
        width: 100%
    }

    .back {
        color: white;
        background: rgba(0, 0, 0, 0.8);
        margin-top: 20px;
    }

    .synopsis {
        font-weight: bold;
        font-size: 15px;
    }

    .text {
        font-size: 14px;
        font-weight: normal;
    }

    .liked {
        background: unset;
        border: unset;
        color: #ff3009;
    }

    .liked:hover {
        cursor: pointer;
    }

    .see {
        background: unset;
        border: unset;
        color: #4b86c7;
    }

    .see:hover {
        cursor: pointer;
    }


    .scrollbar::-webkit-scrollbar-track {
        position: relative;
        left: 50px;
        -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
        background-color: transparent;
    }

    .scrollbar::-webkit-scrollbar {
        width: 6px;
        background-color: transparent;
    }

    .scrollbar::-webkit-scrollbar-thumb {
        background-color: #bcb6b9;
    }

    .nav-tabs {
        background-color: transparent;
    }


    .nav-tabs .nav-item.show .nav-link, .nav-tabs .nav-link.active {
        color: #f3f3f3;
        background-color: transparent;
        border-color: transparent transparent #f3f3f3;
        border-bottom: 4px solid !important;
        font-weight: bold;
    }

    .nav-tabs .nav-link {
        border: 1px solid transparent;
        color: #eee;
    }

    .nav-tabs .nav-link:hover {
        border-color: transparent;
    }

    .actor-picture {
        border: 1px solid black;
        border-radius: 15px;
        width: 50%;
    }


    .actor-info {
        font-size: 15px;
        font-family: 'Dosis', 'Source Sans Pro', 'Helvetica Neue', Arial, sans-serif;
    }

    .notes-textarea {
        background-color: transparent;
        color: white;
        border: none;
        resize: none;
    }

    .trailer:hover {
        background-color: transparent;
        border: 1px solid rgb(255, 57, 111);
    }

    .trailer {
        background-color: transparent;
        border: 1px solid rgb(148, 253, 255);
    }

</style>
