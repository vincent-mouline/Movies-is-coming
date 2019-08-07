<template>
    <transition name="modal">
        <div class="modal-mask">
            <div class="modal-wrapper">
                <div class="modal-container" @click="$emit('close')">

                    <div class="modal-header">
                        <slot name="header">
                            <h2 class="flux mx-auto text-center">{{ trailerTitle }}</h2>
                        </slot>
                    </div>

                    <div class="modal-body">
                        <slot name="body">
                            <youtube :video-id="trailer" :player-vars="{autoplay: 1}" player-width="730"
                                     player-height="340"></youtube>
                        </slot>
                    </div>

                    <div class="modal-footer">
                        <slot name="footer">
                            <button class="modal-default-button flux" @click="$emit('close')">&nbsp;
                            </button>
                        </slot>
                    </div>
                </div>
            </div>
        </div>
    </transition>
</template>


<script>
    export default {
        data() {
            return {
                videoId: '',
                playerVars: {
                    autoplay: 1
                },
            }
        },
        props: [
            'trailer',
            'trailerTitle'
        ],
        methods: {
            close() {
                this.$emit('close');
            },
            playing() {
                console.log('\o/ we are watching!!!')
            },
        },
    };
</script>

<style scoped>
    .no-movie {
        height: 340px;
        width: 730px;
        line-height: 170px;
        text-align: center;
        font-size: 50px;
        font-family: 'Roboto', sans-serif;
        color: black;
    }

    .modal-mask {
        position: fixed;
        z-index: 9998;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background-color: rgba(0, 0, 0, .9);
        display: table;
        transition: opacity .3s ease;
    }

    .modal-wrapper {
        display: table-cell;
        vertical-align: middle;
    }

    .modal-container {
        width: auto;
        height: auto;
        margin: 0 auto;
        background-color: rgba(0, 0, 0, 0.9);
        background-image: url('../assets/modal.png');
        background-repeat: no-repeat;
        background-position: center;
        transition: all .3s ease;
        font-family: Helvetica, Arial, sans-serif;
    }

    .modal-header {
        border: none;
    }

    .modal-footer {
        border: none;
    }

    .modal-header h3 {
        margin-top: 0;
        color: #42b983;
    }

    .modal-body {
        overflow-x: auto;
        display: flex;
        flex-direction: column;
        position: relative;
        text-align: center;
        margin: auto auto;
    }

    .modal-default-button {
        background-color: transparent;
        border: none;
        float: left;
        font-size: 18px;
        font-family: 'Dosis', 'Source Sans Pro', 'Helvetica Neue', Arial, sans-serif;
    }


    h2 {
        position: absolute;
        top: 10%;
        left: 50%;
        transform: translate(-50%, -50%);
    }


    .modal-enter {
        opacity: 0;
    }

    .modal-leave-active {
        opacity: 0;
    }



</style>