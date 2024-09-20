<script>
  import AddMovie from "./AddMovie.svelte";
  import { createEventDispatcher } from "svelte";

  const dispatch = createEventDispatcher();
  let movieName = "";
  let backdropUrl = "";
  let genres = [];
  let movieGenres;

  const API_KEY = "0096777d7833f21a0d85007d126fe9ec";
  const API_URL = `https://api.themoviedb.org/3/movie/popular?api_key=${API_KEY}&language=en-US&page=1`;
  const IMAGE_BASE_URL = "https://image.tmdb.org/t/p/original";

  // API stores genres as numbers, which is stupid
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
    37: "Western",
  };

  const fetchPopularMovie = async () => {
    try {
      const response = await fetch(API_URL);
      const data = await response.json();
      if (data && data.results.length > 0) {
        const randomIndex = Math.floor(
          Math.random() * Math.min(data.results.length, 19)
        );
        const movie = data.results[randomIndex];
        movieName = movie.title;
        backdropUrl = `${IMAGE_BASE_URL}${movie.backdrop_path}`;
        genres = movie.genre_ids;
        movieGenres = genres.map((id) => genreMap[id] || "Unknown").join(", ");
      }
    } catch (error) {
      console.error("Error fetching the popular movie:", error);
    }
  };

  function handleAddMovie(event) {
    dispatch("addMovie", event.detail);
  }

  fetchPopularMovie();
</script>

<a href="/"><div class="branding"><b>CINE</b>PATH</div></a>
<div class="banner" style="background-image: url('{backdropUrl}');">
  <div class="centered-text">
    <h2>Hello Prabh, here's what's trending</h2>
    <h1>{movieName}</h1>
    <h2><div class="tags">{movieGenres}</div></h2>
    <AddMovie on:addMovie={handleAddMovie} />
  </div>
</div>

<style>
  .banner {
    width: 100%;
    height: 90vh;
    background-color: black;
    display: flex;
    justify-content: flex-start;
    align-items: center;
    padding-left: 20px;
    background-size: cover;
    background-position: center;
    position: relative;
    overflow: hidden;
  }

  .banner::before {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: linear-gradient(
        to bottom,
        rgba(0, 0, 0, 0.6) 0%,
        rgba(0, 0, 0, 0.6) 60%,
        rgba(0, 0, 0, 0) 70%
      ),
      linear-gradient(to bottom, rgba(0, 0, 0, 0) 60%, black 100%);
    z-index: 1;
    pointer-events: none;
  }

  .branding {
    position: absolute;
    z-index: 2;
    padding: 20px;
    color: white;
    font-size: 20px;
  }

  .centered-text {
    position: relative;
    z-index: 2;
    color: white;
    font-size: 24px;
    line-height: 0.5;
    margin-left: 5%;
  }

  h2 {
    font-size: 1em;
    font-weight: 300;
  }

  .tags {
    color: lightgray;
  }
</style>
