<script>
    import Swal from 'sweetalert2'
    import { createEventDispatcher } from 'svelte';
    const dispatch = createEventDispatcher();

    let rating = '';
    let thoughts = '';
    let showAddMovie = false;
    let now = new Date();
    let datetime = now.toLocaleString();
    let searchQuery = '';
    let movieResults = [];
    let selectedMovie = null;
    let moviePreview = '';
    let disableSearch = false;

    function toggleAddMovie() {
        showAddMovie = !showAddMovie;
    }

    function hideAddMovie() {
        showAddMovie = false;
    }

    const apiKey = '0096777d7833f21a0d85007d126fe9ec';

    async function searchMovies(query) {
        if (query.length >= 3 && !disableSearch) {
            const response = await fetch(`https://api.themoviedb.org/3/search/movie?api_key=${apiKey}&query=${query}`);
            const data = await response.json();
            movieResults = data.results;
        } else {
            movieResults = [];
        }
    }

    function selectMovie(movie) {
        selectedMovie = movie;
        moviePreview = `https://image.tmdb.org/t/p/w300${movie.poster_path}`;
        disableSearch = true;
        searchQuery = movie.title;
        console.log(movie);
        setTimeout(() => {
            disableSearch = false;
        }, 100);
        movieResults = [];
    }

    function resetForm() {
        searchQuery = '';
        movieResults = [];
        selectedMovie = null;
        rating = '';
        thoughts = '';
        datetime = new Date().toLocaleString();
        moviePreview = "";
    }

    const genreMap = {
        28: "Action",
        12: "Adventure",
        16: "Animation",
        35: "Comedy",
        80: "Crime",
        99: "Documentary",
        18: "Drama",
        10751: "Family",
        14: "Fantasy",
        36: "History",
        27: "Horror",
        10402: "Music",
        9648: "Mystery",
        10749: "Romance",
        878: "Science Fiction",
        10770: "TV Movie",
        53: "Thriller",
        10752: "War",
        37: "Western"
    };

    function submitForm() {
        if (selectedMovie && rating && thoughts) {
            const tags = selectedMovie.genre_ids.map(id => genreMap[id] || 'Unknown');
            dispatch('addMovie', {
                name: selectedMovie.title,
                rating: rating,
                userReview: thoughts,
                reviewTime: datetime,
                description: selectedMovie.description || '',
                tags: tags || [],
                posterUrl: "https://image.tmdb.org/t/p/w300/" + selectedMovie.poster_path || ''
            });
            hideAddMovie();
            resetForm();
            Swal.fire({
                    title: 'MOVIE ADDED',
                    text: 'Your movie is logged onto CINEPATH!',
                    icon: 'success',
                    confirmButtonText: 'SICK!',
                    confirmButtonColor: "#000"
            })
        }

        else{
            Swal.fire({
                    title: 'FAILED TO ADD',
                    text: 'Looks like your movie failed to add, check to make sure all fields are filled in and a movie was selected from the results.',
                    icon: 'error',
                    confirmButtonText: 'I WILL TRY AGAIN!',
                    confirmButtonColor: "#000"
            })
        }
    }

    $: searchMovies(searchQuery);
    </script>

<style>
    .addmovie {
        display: none;
    }
    .addmovie.show {
        display: block;
    }
    .moviepopup {
        position: fixed;
        z-index: 4;
        line-height: 1.5;
        backdrop-filter: blur(20px);
        height: 100%;
        width: 40%;
        top: 0;
        left: 0;
        overflow-y: auto;
        padding: 20px;
        font-size: 0.8em;
    }

    .overlay {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background-image: linear-gradient(to bottom right, #000, #000);
        opacity: 0.8;
        z-index: 3;
    }

    .star-rating {
        display: flex;
        direction: row;
        align-items: center;
    }

    .star-rating input[type="radio"] {
        display: none;
    }

    .star-rating label {
        color: #6d6d6d;
        cursor: pointer;
        margin: 0 5px;
    }

    .star-rating input[type="radio"]:checked ~ label {
        color: #fff;
    }

    .star-rating input[type="radio"]:checked ~ label ~ label {
        color: #6d6d6d;
    }

    .moviepreview{
        width: 300px;
        height: 400px;
        background-color: #000;
        background-size: cover;
        background-position: center;
    }

    .movie-list {
        list-style-type: none;
        padding: 0;
        margin: 0;
    }

    button {
        margin: 4px;
        padding: 5px 20px;
        border-radius: 20px;
        background-color: white;
        color: black;
        border: 1px solid white;
        text-transform: uppercase;
        cursor: pointer;
        text-decoration: none;
        -webkit-transition: all 0.5s;
        transition: all 0.5s;
        }

    button:hover {
        background-color: transparent;
        color: white;
    }

    .alwaysbtn {
        position: fixed;
        right:0;
        bottom: 0;
        margin: 20px;
        border-radius: 50%;
        padding: 20px;
        background-color: white;
        color: black;
        border: 1px solid white;
        text-transform: uppercase;
        cursor: pointer;
        text-decoration: none;
        -webkit-transition: all 0.5s;
        transition: all 0.5s;
        }

    .alwaysbtn:hover {
        background-color: transparent;
        color: white;
    }

    </style>

<br /><br /><br />

<a href="#add" class="alwaysbtn" on:click|preventDefault={toggleAddMovie}>+</a>

<a href="#add" class="btn" on:click|preventDefault={toggleAddMovie}>I JUST WATCHED A MOVIE</a>

<div class="addmovie {showAddMovie ? 'show' : ''}">
    <div class="overlay"></div>
    <div class="moviepopup">
        <a  on:click={hideAddMovie} class="btn_close" href="#close">✖</a>
        <br />
        <h2>ADD A MOVIE</h2>
        <br />
        {#if moviePreview}
        <div style="background-image: url('{moviePreview || ''}');" class="moviepreview"></div>
        <br />
        {/if}
        <p>What is the movie called?</p>
        <input
            placeholder="Search for a movie"
            type="text"
            bind:value={searchQuery}
        />
        {#if movieResults.length > 0}
            <div class="movie-list">
                {#each movieResults as movie}
                    <button 
                        class="movieoptions"
                        on:click={() => selectMovie(movie)}
                        on:keydown={(e) => e.key === 'Enter' && selectMovie(movie)}
                        role="option"
                        aria-selected={selectedMovie === movie ? 'true' : 'false'}
                        aria-label={`Select ${movie.title}`}
                    >
                        {movie.title}
                    </button>
                {/each}
            </div>
        {/if}
        <br /><br /><br />
        <p>How many stars would you give?</p>
        <div class="star-rating">
            <input type="radio" id="star5" name="rating" value="5" bind:group={rating}>
            <label for="star5">5 Stars</label>
            <input type="radio" id="star4" name="rating" value="4" bind:group={rating}>
            <label for="star4">4 Stars</label>
            <input type="radio" id="star3" name="rating" value="3" bind:group={rating}>
            <label for="star3">3 Stars</label>
            <input type="radio" id="star2" name="rating" value="2" bind:group={rating}>
            <label for="star2">2 Stars</label>
            <input type="radio" id="star1" name="rating" value="1" bind:group={rating}>
            <label for="star1">1 Star</label>
        </div>
        <br /><br />
        <p>Tell us your thoughts!</p>
        <textarea
        placeholder="Start writing"
        id="thoughts"
        name="thoughts"
        bind:value={thoughts}
        />
        <br /><br /><br />
        <p>Log time: {datetime}</p>
        <br /><br />
        <a on:click={submitForm} href="#add" class="btn">ADD THIS MOVIE</a>
        <br /><br /><br />
        </div>
    </div>