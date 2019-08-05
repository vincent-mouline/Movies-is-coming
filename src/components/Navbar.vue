<template>
    <nav class="navbar navbar-expand-lg navbar-dark pt-1">
        <a class="navbar-brand" href="#"><img class="logo" src="../assets/logo.png"></a>

        <button
                class="navbar-toggler"
                type="button"
                data-toggle="collapse"
                data-target="#navbarSupportedContent"
                aria-controls="navbarSupportedContent"
                aria-expanded="false"
                aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>

        <div class="collapse navbar-collapse" id="navbarSupportedContent">
            <ul class="navbar-nav mr-auto">
                <li class="nav-item navbar-link dropdown mx-2">
                    <a class="nav-link dropdown-toggle neon "
                       href="#"
                       id="navbarDropdown"
                       role="button"
                       data-toggle="dropdown"
                       aria-haspopup="true"
                       aria-expanded="false"
                    >Catégories</a>
                    <div class="dropdown-menu disabled" aria-labelledby="navbarDropdown">
                        <a class="dropdown-item nav-link neon" href="" v-on:click.prevent="categorySorts(category)"
                           v-bind:key="category.id" v-for="category in categories.genres">{{category.name}}</a>
                    </div>
                </li>
                <li class="nav-item navbar-link mx-2">
                    <a href="" class="nav-link neon" v-on:click.prevent="initialLoading()">
                        Tendances
                    </a>
                </li>
                <li class="nav-item navbar-link mx-2 neon">
                    <a href="" class="nav-link " v-on:click.prevent="actually()">En salle</a>
                </li>
                <li class="nav-item navbar-link mx-2 neon">
                    <a href="" class="nav-link " v-on:click.prevent="favoris()">Mes Favoris</a>
                </li>
                <li class="nav-item navbar-link mx-2 neon">
                    <a href="" class="nav-link " v-on:click.prevent="toSee()">A regarder</a>
                </li>
                <li class="nav-item navbar-link mx-2 neon">
                    <a href="" class="nav-link " v-on:click.prevent="recommended()">Recommandé</a>
                </li>
            </ul>
            <form class="form-inline my-0 my-lg-0">
                <input class="form-control mr-sm-2 search-bar" size="12" type="text" name="movieSearch"
                       placeholder="..."
                       v-model="query" @keypress.enter.prevent="movieSearch"/>
                <div class="navbar-nav">
                    <div class="nav-item navbar-link">
                        <a class="my-2 px-1 my-sm-0 nav-link neon search-button"
                           v-on:click.prevent="movieSearch"
                        >Rechercher
                        </a>
                    </div>
                </div>
            </form>
        </div>
    </nav>
</template>

<script>

    export default {

        name: "Navbar",

        data() {
            return {
                query: '',
                category: '',
                font: '../assets/fonts/Library 3 am.otf'
            }
        },

        props: [
            'categories'
        ],
        methods: {
            movieSearch() {
                if (this.query === "") {
                    alert("Veuillez entrer un titre de film");
                } else {
                    this.$emit("SearchRequested", this.query)
                }
            },
            categorySorts(category) {
                this.$emit("categorySorts", category);
            },
            favoris() {
                this.$emit("favoris");
            },
            toSee() {
                this.$emit("toSee");
            },
            actually() {
                this.$emit("actually");
            },
            initialLoading() {
                this.$emit("initialLoading")
            },
            recommended() {
                this.$emit("recommended")
            }
        }
    };
</script>

<style>

    .dropdown-menu {
        padding: 0;
        margin: 0;
        background: unset;
    }

    .dropdown-item {
        font-size: 16px;
        margin: 0;
        padding: 0;
    }

    .dropdown-item:hover {
        background: unset;
    }

    .navbar {
        font-family: 'Roboto', sans-serif;
        font-size: 25px;
        padding: 0;
        margin: 0;
    }

    .navbar-link {
        font-weight: 400;
        text-align: center;
        text-transform: uppercase;
        position: relative;
        font-size: 15px;
    }

    .navbar-link :after {
        position: relative;
        top: 4px;
    }

    .logo {
        position: relative;
        top: 10px;
        margin-left: 5px;
        height: 60px;
    }

    .navbar {
        position: relative;
        bottom: 11px;
    }

    .search-bar {
        margin-right: 10px;
        border: 1px solid rgba(148, 253, 255, 0.64);
        box-shadow: 0 0 10px rgba(148, 253, 255, 0.64);
        color: #fff;
        background-color: rgba(0, 0, 0, 0.63);
    }


    .search-bar:hover {
        border: 1px solid rgba(255, 3, 0, 0.46);
        box-shadow: 0 0 10px rgba(255, 3, 0, 0.46);
        background-color: rgba(0, 0, 0, 0.63);
        color: #fff;
    }

    .search-bar:focus {
        background-color: rgba(0, 0, 0, 0.63);
        box-shadow: 0 0 10px rgba(255, 3, 0, 0.46);
        color: #fff;
        border: 1px solid rgba(255, 3, 0, 0.46);
    }


    .neon {
        font-family: 'Roboto', sans-serif;
        color: #fff;
        text-shadow: 0 0 2px rgba(148, 253, 255, 0.64),
        0 0 10px #fff,
        0 0 20px #fff,
        0 0 40px #0ff,
        0 0 100px #0ff;
    }

    .neon:hover {
        color: #FA1C16;

        text-shadow: 0 0 1px rgba(255, 3, 0, 0.78),
        0 0 10px #fff,
        0 0 20px #fff,
        0 0 40px #FA1C16,
        0 0 100px #FA1C16;
    }

    .search-button {
            cursor: pointer;
    }

</style>