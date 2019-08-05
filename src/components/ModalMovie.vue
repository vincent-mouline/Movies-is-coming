<template>
    <div class="modal fade"
            :id="movie.id"
            tabindex="-1"
            role="dialog"
            :aria-labelledby="movie.id"
            aria-hidden="true">
        <div class="modal-dialog text-white" role="document">
            <div class="modal-content bg-dark">
                <div class="modal-header">
                    <h5 class="modal-title" id="exampleModalLabel">{{movie.title}}</h5>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                        <span aria-hidden="true">&times;</span>
                    </button>
                </div>
                <div class="modal-body">
                    <p>{{ movie.overview }}</p>
                    <p class="synopsis">Date de sortie :</p>
                    <span>{{dateFormat(movie)}}</span>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-dismiss="modal">Fermer</button>
                    <a type="button" target="_blank" v-bind:href="'https://www.themoviedb.org/movie/' + movie.id"
                       class="btn btn-primary">Voir sur IMDB</a>
                </div>
            </div>
        </div>
    </div>
</template>


<style>
    .synopsis {
        font-weight: bold;
        font-size: 15px;
    }
</style>