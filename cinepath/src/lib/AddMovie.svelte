<script>
  import Swal from "sweetalert2";
  import { createEventDispatcher } from "svelte";
  const dispatch = createEventDispatcher();

  let rating = "1";
  let snack = [];
  let snacks_exp = "";
  let people_amount = 0;
  let people_think = "";
  let thoughts = "";
  let showAddMovie = false;
  let now = new Date();
  let datetime = now.toLocaleString();
  let searchQuery = "";
  let movieResults = [];
  let selectedMovie = null;
  let moviePreview = "";
  let disableSearch = false;
  let showEditPopup = false;
  let showSnacksSection = true;
  let showPeopleSection = true;

  function toggleAddMovie() {
    showAddMovie = !showAddMovie;
  }

  function hideAddMovie() {
    showAddMovie = false;
  }

  const apiKey = "0096777d7833f21a0d85007d126fe9ec";

  async function searchMovies(query) {
    if (query.length >= 3 && !disableSearch) {
      const response = await fetch(
        `https://api.themoviedb.org/3/search/movie?api_key=${apiKey}&query=${query}`
      );
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
    setTimeout(() => {
      disableSearch = false;
    }, 100);
    movieResults = [];
  }

  function resetForm() {
    searchQuery = "";
    movieResults = [];
    selectedMovie = null;
    rating = "1";
    thoughts = "";
    datetime = new Date().toLocaleString();
    moviePreview = "";
    snack = [];
    snacks_exp = "";
    people_amount = 0;
    people_think = "";
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
    37: "Western",
  };

  function submitForm() {
    if (
      selectedMovie &&
      rating &&
      thoughts &&
      (showSnacksSection ? snack.length > 0 && snacks_exp : true) &&
      (showPeopleSection ? people_amount >= 0 && people_think : true)
    ) {
      const tags = selectedMovie.genre_ids.map(
        (id) => genreMap[id] || "Unknown"
      );
      dispatch("addMovie", {
        name: selectedMovie.title,
        rating: Number(rating),
        userReview: thoughts,
        reviewTime: datetime,
        description: selectedMovie.overview || "",
        tags: tags || [],
        posterUrl:
          "https://image.tmdb.org/t/p/w300/" + selectedMovie.poster_path || "",
        snack: snack,
        snacks_exp: snacks_exp,
        people_amount: people_amount,
        people_think: people_think,
      });
      hideAddMovie();
      resetForm();
      Swal.fire({
        title: "MOVIE ADDED",
        text: "Your movie is logged onto CINEPATH!",
        icon: "success",
        confirmButtonText: "SICK",
        confirmButtonColor: "#000",
        allowOutsideClick: false,
        allowEscapeKey: false,
      });
    } else {
      Swal.fire({
        title: "FAILED TO ADD",
        text: "Looks like your movie failed to add, check to make sure all fields are filled in and a movie was selected from the results.",
        icon: "error",
        confirmButtonText: "I WILL TRY AGAIN",
        confirmButtonColor: "#000",
        allowOutsideClick: false,
        allowEscapeKey: false,
      });
    }
  }

  function editActivities() {
    showEditPopup = !showEditPopup;
    hideAddMovie();
  }

  function toggleSnacks() {
    showSnacksSection = !showSnacksSection;
    resetForm();
  }

  function togglePeople() {
    showPeopleSection = !showPeopleSection;
    resetForm();
  }

  function toggleMovies() {
    Swal.fire({
      title: "UH OH!",
      text: "Movies are the main thing here, you need them!",
      icon: "error",
      confirmButtonText: "OKAY",
      confirmButtonColor: "#000",
      allowOutsideClick: false,
      allowEscapeKey: false,
    });
  }

  $: searchMovies(searchQuery);
</script>

<br /><br /><br />

<a href="#add" class="alwaysbtn" on:click|preventDefault={toggleAddMovie}>+</a>

<a href="#add" class="btn" on:click|preventDefault={toggleAddMovie}
  >LET'S TRACK TODAY</a
>

<div class="addmovie {showAddMovie ? 'show' : ''}">
  <div class="overlay"></div>
  <div class="moviepopup">
    <a on:click={hideAddMovie} class="btn_close" href="#close">✖</a>
    <br />
    <h2>TODAYS ACTIVITY</h2>
    <br />
    <h3>FIRST, LET'S START WITH A MOVIE</h3>
    {#if moviePreview}
      <div
        style="background-image: url('{moviePreview || ''}');"
        class="moviepreview"
      ></div>
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
            class="movieoptions sbutton"
            on:click={() => selectMovie(movie)}
            on:keydown={(e) => e.key === "Enter" && selectMovie(movie)}
            role="option"
            aria-selected={selectedMovie === movie ? "true" : "false"}
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
      <input
        type="radio"
        id="star1"
        name="rating"
        value="1"
        bind:group={rating}
      />
      <label for="star1">1 Star</label>
      <input
        type="radio"
        id="star2"
        name="rating"
        value="2"
        bind:group={rating}
      />
      <label for="star2">2 Stars</label>
      <input
        type="radio"
        id="star3"
        name="rating"
        value="3"
        bind:group={rating}
      />
      <label for="star3">3 Stars</label>
      <input
        type="radio"
        id="star4"
        name="rating"
        value="4"
        bind:group={rating}
      />
      <label for="star4">4 Stars</label>
      <input
        type="radio"
        id="star5"
        name="rating"
        value="5"
        bind:group={rating}
      />
      <label for="star5">5 Stars</label>
    </div>
    <br /><br />
    <p>Tell us your thoughts!</p>
    <textarea
      placeholder="Start writing..."
      id="thoughts"
      name="thoughts"
      bind:value={thoughts}
    />
    <div class="snacksContainer" class:hide={!showSnacksSection}>
      <br /><br /><br />
      <h3>NOW LET'S TALK SNACKS</h3>
      <p>Select all the snacks you ate!</p>
      <div class="snack-selection">
        <input
          type="checkbox"
          id="popcorn"
          name="snack"
          value="Popcorn"
          bind:group={snack}
        />
        <label for="popcorn">Popcorn</label><br />
        <input
          type="checkbox"
          id="candy"
          name="snack"
          value="Candy"
          bind:group={snack}
        />
        <label for="candy">Candy</label><br />
        <input
          type="checkbox"
          id="nachos"
          name="snack"
          value="Nachos"
          bind:group={snack}
        />
        <label for="nachos">Nachos</label><br />
        <input
          type="checkbox"
          id="soda"
          name="snack"
          value="Soda"
          bind:group={snack}
        />
        <label for="soda">Soda</label><br />
        <input
          type="checkbox"
          id="pretzels"
          name="snack"
          value="Pretzels"
          bind:group={snack}
        />
        <label for="pretzels">Pretzels</label><br />
        <input
          type="checkbox"
          id="chips"
          name="snack"
          value="Chips"
          bind:group={snack}
        />
        <label for="chips">Chips</label><br />
      </div>
      <br /><br />
      <p>Tell us your thoughts about the snacks?</p>
      <textarea
        placeholder="Start writing..."
        id="snacks_exp"
        name="snacks_exp"
        bind:value={snacks_exp}
      />
    </div>
    <div class="peopleContainer" class:hide={!showPeopleSection}>
      <br /><br /><br />
      <h3>LASTLY, LET'S TALK PEOPLE</h3>
      <p>How many people did you watch with?</p>
      <input
        type="number"
        id="people_amount"
        name="people_amount"
        bind:value={people_amount}
        min="0"
        max="100"
      />
      <br /><br /><br />
      <p>
        What did they think of the movie? If no one, can this movie be enjoyed
        with others?
      </p>
      <textarea
        placeholder="Start writing..."
        id="people_think"
        name="people_think"
        bind:value={people_think}
      />
    </div>
    <br /><br />
    <p>Log time: {datetime}</p>
    <br />
    <a on:click={editActivities} href="#editactivity" class="btn">EDIT</a>
    <a on:click={submitForm} href="#add" class="btn">POST THIS ENTRY</a>
    <br /><br /><br />
  </div>
</div>

{#if showEditPopup}
  <div class="overlay"></div>
  <div class="editact-popup">
    <h2>EDIT ACTIVITIES</h2>
    <p>Adjust what you would like to see on the add movies panel.</p>
    <button class="btn" on:click={toggleMovies}> HIDE MOVIES SECTION </button>
    <br />
    <br />
    <button class="btn" on:click={toggleSnacks}>
      {showSnacksSection ? "Hide Snacks Section" : "Show People Section"}
    </button>
    <br />
    <br />
    <button class="btn" on:click={togglePeople}>
      {showPeopleSection ? "Hide People Section" : "Show People Section"}
    </button>
    <br />
    <br />
    <br />
    <button class="btn" on:click={editActivities}>DONE</button>
  </div>
{/if}

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

  .moviepreview {
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

  .sbutton {
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

  .sbutton:hover {
    background-color: transparent;
    color: white;
  }

  .alwaysbtn {
    font-weight: 700;
    position: fixed;
    right: 0;
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

  .editact-popup {
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    position: fixed;
    z-index: 4;
    border-radius: 10px;
    color: rgb(255, 255, 255);
    padding: 20px 30px;
    backdrop-filter: blur(20px);
    max-height: 60%;
    overflow: auto;
    font-size: 0.6;
  }

  .hide {
    display: none;
  }
</style>
