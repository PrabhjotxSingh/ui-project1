<script>
  import WelcomeBanner from "./lib/WelcomeBanner.svelte";
  import Swal from "sweetalert2";

  let goal = 10;
  let showEdit = false;
  let editIndex = null;
  let editName = "";
  let editRating;
  let editUserReview = "";
  let editSnack = [""];
  let editSnackThoughts = "";
  let editPeopleAmount = 0;
  let editPeopleThink = "";
  let moreDetail = "";

  let movies = [
    {
      name: "Interstellar",
      rating: 5,
      userReview: "I liked this movie a lot, one of my favs!",
      reviewTime: "9/1/2024, 12:09:17 AM",
      description:
        "The adventures of a group of explorers who make use of a newly discovered wormhole to surpass the limitations on human space travel and conquer the vast distances involved in an interstellar voyage.",
      tags: ["Sci-Fi", "Drama", "Space"],
      posterUrl:
        "https://image.tmdb.org/t/p/w300/gEU2QniE6E77NI6lCU6MxlNBvIx.jpg",
      snack: ["Popcorn", "Candy"],
      snacks_exp: "It was okay, felt thirsty.",
      people_amount: 0,
      people_think: "I will show this to my family.",
    },
    {
      name: "The Matrix",
      rating: 4,
      userReview: "Everyone talks about it, it's pretty good!",
      reviewTime: "9/2/2024, 12:09:17 AM",
      description:
        "Set in the 22nd century, The Matrix tells the story of a computer hacker who joins a group of underground insurgents fighting the vast and powerful computers who now rule the earth.",
      tags: ["Sci-Fi", "Action", "Philosophical"],
      posterUrl:
        "https://image.tmdb.org/t/p/w300/f89U3ADr1oiB1s9GkdPOEpXUk5H.jpg",
      snack: ["Popcorn", "Pop"],
      snacks_exp: "It was okay.",
      people_amount: 1,
      people_think: "He liked it.",
    },
    {
      name: "Inception",
      rating: 5,
      userReview: "Had me on edge!",
      reviewTime: "9/3/2024, 12:09:17 AM",
      description:
        "Dom Cobb (Leonardo DiCaprio) is a thief with the rare ability to enter people's dreams and steal their secrets from their subconscious.",
      tags: ["Sci-Fi", "Action"],
      posterUrl:
        "https://image.tmdb.org/t/p/w300/ljsZTbVsrQSqZgWeep2B1QiDKuh.jpg",
      snack: ["Popcorn"],
      snacks_exp: "It was good.",
      people_amount: 3,
      people_think: "They liked it.",
    },
    {
      name: "Cats",
      rating: 1,
      userReview: "Just awful!",
      reviewTime: "9/4/2024, 12:09:17 AM",
      description:
        "A tribe of cats compete during the annual Jellicle Ball, where one lucky feline will be chosen to ascend to the Heaviside Layer and be granted a new life.",
      tags: ["Musical", "Family"],
      posterUrl:
        "https://image.tmdb.org/t/p/w300/aCNch5FmzT2WaUcY44925owIZXY.jpg",
      snack: ["Popcorn", "Chips"],
      snacks_exp: "It was good.",
      people_amount: 1,
      people_think: "He liked it.",
    },
    {
      name: "The Silence of the Lambs",
      rating: 4,
      userReview: "Spooky and interesting!",
      reviewTime: "9/19/2024, 12:09:17 AM",
      description:
        "A young FBI cadet must receive the help of an incarcerated and manipulative cannibal killer to help catch another serial killer, a madman who skins his victims.",
      tags: ["Crime", "Horror"],
      posterUrl:
        "https://image.tmdb.org/t/p/w300/uS9m8OBk1A8eM9I042bx8XXpqAq.jpg",
      snack: ["Popcorn"],
      snacks_exp: "It was nice.",
      people_amount: 0,
      people_think: "I will show it to more.",
    },
  ];

  const firstReviewDate = new Date(Date.parse(movies[0].reviewTime));
  let now = new Date();
  const diffInMilliseconds = now.getTime() - firstReviewDate.getTime();
  const diffInDays = Math.round(diffInMilliseconds / (1000 * 60 * 60 * 24));

  function toggleEdit(
    movieName,
    movieRating,
    movieUserReview,
    movieSnack,
    movieSnackThoughts,
    moviePeople,
    moviePeopleThoughts,
    index
  ) {
    showEdit = !showEdit;
    if (showEdit) {
      editName = movieName;
      editRating = movieRating;
      editUserReview = movieUserReview;
      editSnack = movieSnack;
      editSnackThoughts = movieSnackThoughts;
      editPeopleAmount = moviePeople;
      editPeopleThink = moviePeopleThoughts;
      editIndex = index;
    } else {
      editIndex = null;
    }
  }

  function saveEdits() {
    movies[editIndex] = {
      ...movies[editIndex],
      name: editName,
      rating: editRating,
      userReview: editUserReview,
      snack: editSnack,
      snacks_exp: editSnackThoughts,
      people_amount: editPeopleAmount,
      people_think: editPeopleThink,
    };
    showEdit = false;
  }

  function hideAddMovie() {
    showEdit = false;
  }

  function handleAddMovie(event) {
    movies = [...movies, event.detail];
    sortMoviesByDate();
  }

  function sortMoviesByDate() {
    movies.sort((a, b) => {
      const dateA = new Date(a.reviewTime);
      const dateB = new Date(b.reviewTime);
      return dateB.getTime() - dateA.getTime();
    });
  }

  function statsButton() {
    Swal.fire({
      title: "YOUR STATS",
      html: `You have watched <b>${movies.length}</b> out of your goal of <b>${goal}</b>!<br />You are <b>${Math.round((movies.length / goal) * 100)}%</b> of the way there.<br /><br />You have been here for <b>${diffInDays}</b> days and have logged for <b>${movies.length}</b>. This means you log <b>${Math.round((movies.length / diffInDays) * 100)}%</b> of the days.`,
      showCancelButton: true,
      confirmButtonText: "CLOSE",
      cancelButtonText: "CHANGE GOAL",
      confirmButtonColor: "#000",
      cancelButtonColor: "#555",
      allowOutsideClick: false,
      allowEscapeKey: false,
    }).then((result) => {
      if (result.isDismissed) {
        Swal.fire({
          title: "NEW GOAL",
          input: "number",
          inputLabel: "Enter your new goal:",
          inputPlaceholder: "New goal...",
          confirmButtonText: "Set Goal",
          confirmButtonColor: "#000",
          allowOutsideClick: false,
          allowEscapeKey: false,
          inputValidator: (value) => {
            const intGoal = parseInt(value, 10);
            if (!intGoal || intGoal <= 0 || intGoal < movies.length) {
              return `Please enter a valid number greater than zero and it's not below your current movies watched of ${movies.length}.`;
            }
          },
        }).then((newGoalResult) => {
          if (newGoalResult.isConfirmed) {
            const newGoal = parseInt(newGoalResult.value, 10);
            goal = newGoal;
            Swal.fire({
              title: "GOAL UPDATED",
              text: `Your new goal is set to ${goal}. Keep watching!`,
              confirmButtonText: "Great!",
              confirmButtonColor: "#000",
              allowOutsideClick: false,
              allowEscapeKey: false,
            });
          }
        });
      }
    });
  }

  function openMovie(
    movieName,
    movieRating,
    movieUserReview,
    movieSnack,
    movieSnackThoughts,
    moviePeople,
    moviePeopleThoughts,
    index
  ) {

    moreDetail = "";

    if (movieSnack.length > 0 || movieSnackThoughts.trim() !== "") {
      if (movieSnack.length > 0) {
        moreDetail += `During this movie you ate ${movieSnack.join(", ")}.<br />`;
      } else {
        moreDetail += `No snacks were recorded.<br />`;
      }

      if (movieSnackThoughts.trim() !== "") {
        moreDetail += `You stated: ${movieSnackThoughts}<br /><br />`;
      } else {
        moreDetail += `No thoughts about snacks were recorded.<br /><br />`;
      }
    } else {
      moreDetail += `Both snacks and details were not recorded.<br /><br />`;
    }

    if (moviePeople !== null || moviePeopleThoughts.trim() !== "") {
      if (moviePeople !== null) {
        moreDetail += `You watched this movie with ${moviePeople} other person(s).<br />`;
      } else {
        moreDetail += `No people details were recorded.<br />`;
      }

      if (moviePeopleThoughts.trim() !== "") {
        moreDetail += `You stated: ${moviePeopleThoughts}<br /><br />`;
      } else {
        moreDetail += `No thoughts about people were recorded.<br /><br />`;
      }
    } else {
      moreDetail += `Both people and details were not recorded.<br /><br />`;
  }

    Swal.fire({
      title: `${movieName.toUpperCase()}`,
      html: moreDetail,
      confirmButtonText: "DONE",
      confirmButtonColor: "#000",
      showCancelButton: true,
      cancelButtonText: "EDIT",
      cancelButtonColor: "#555",
      allowOutsideClick: false,
      allowEscapeKey: false,
    }).then((result) => {
      if (result.isDismissed) {
        toggleEdit(
          movieName,
          movieRating,
          movieUserReview,
          movieSnack,
          movieSnackThoughts,
          moviePeople,
          moviePeopleThoughts,
          index
        );
      }
    });
  }

  sortMoviesByDate();
</script>

<main>
  <div class="edit-popup {showEdit ? 'show' : ''}">
    <div class="overlay"></div>
    <div class="edit-container">
      <h2>EDIT {editName.toUpperCase()}</h2>
      <p>Type in the fields below to edit content for this entry.</p>
      <p>How many stars:</p>
      <div class="star-rating">
        {#each Array(5) as _, i}
          <input
            type="radio"
            id="editStar{i + 1}"
            name="editRating"
            value={i + 1}
            bind:group={editRating}
          />
          <label for="editStar{i + 1}">{i + 1} Star{i + 1 > 1 ? "s" : ""}</label
          >
        {/each}
      </div>
      <br /><br />
      <p>Your thoughts:</p>
      <textarea bind:value={editUserReview}></textarea>
      <br /><br />
      <p>Select all the snacks you ate!</p>
      <div class="snack-selection">
        {#each ['Popcorn', 'Candy', 'Nachos', 'Soda', 'Pretzels', 'Chips'] as snack}
          <input
            type="checkbox"
            id="edit{snack}"
            value={snack}
            checked={editSnack.includes(snack)}
            on:change={() => {
              editSnack = editSnack.includes(snack)
                ? editSnack.filter(item => item !== snack)
                : [...editSnack, snack];
            }}
          />
          <label for="edit{snack}">{snack}</label><br />
        {/each}
      </div>
      <br /><br />
      <p>Thoughts about the snacks:</p>
      <textarea bind:value={editSnackThoughts}></textarea>
      <br /><br />
      <p>How many people did you watch with:</p>
      <input
        type="number"
        id="people_amount"
        name="people_amount"
        bind:value={editPeopleAmount}
        min="0"
        max="100"
      />
      <br /><br />
      <p>What did they think, if no one can it be enjoyed with others:</p>
      <textarea bind:value={editPeopleThink}></textarea>
      <br />
      <br />
      <button class="btn" on:click={hideAddMovie}>Cancel</button>
      <button class="btn" on:click={saveEdits}>Save Changes</button>
    </div>
  </div>

  <div class="statscontainer">
    <a class="stats" on:click={statsButton} href="#stats">STATS</a>
  </div>
  <WelcomeBanner on:addMovie={handleAddMovie} />
  <div class="centered-container">
    <div class="movies-container">
      {#each movies as movie, index}
        <!-- svelte-ignore a11y-click-events-have-key-events -->
        <!-- svelte-ignore a11y-no-static-element-interactions -->
        <div
          class="movie-item"
          on:click={() =>
            openMovie(
              movie.name,
              movie.rating,
              movie.userReview,
              movie.snack,
              movie.snacks_exp,
              movie.people_amount,
              movie.people_think,
              index
            )}
        >
          <img
            title={movie.description}
            draggable="false"
            src={movie.posterUrl}
            alt="Poster"
            class="movie-poster"
          />
          <p class="movie-content">
            <b>{movie.name}</b> ★ {movie.rating}<br />{movie.tags.join(", ")}<br
            />{movie.userReview}<br />{movie.reviewTime}
          </p>
        </div>
      {/each}
    </div>
  </div>
</main>

<style>
  .edit-popup {
    display: none;
  }
  .edit-popup.show {
    display: block;
  }

  .edit-container {
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
    font-size: 1.2em;
  }

  .overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-image: linear-gradient(to bottom right, #000, #000);
    opacity: 0.9;
    z-index: 3;
  }

  .statscontainer {
    position: absolute;
    top: 0;
    right: 0;
    z-index: 2;
    margin-top: 20px;
    margin-right: 20px;
  }

  .stats {
    font-weight: 700;
    padding: 10px;
    background-color: white;
    color: black;
    border: 1px solid white;
    text-transform: uppercase;
    cursor: pointer;
    text-decoration: none;
    -webkit-transition: all 0.5s;
    transition: all 0.5s;
  }

  .stats:hover {
    background-color: transparent;
    color: white;
  }

  .centered-container {
    width: 80%;
    margin: 0 auto;
    padding: 20px;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    text-align: center;
  }

  .movies-container {
    display: flex;
    gap: 20px;
    justify-content: center;
    flex-wrap: wrap;
    padding: 20px;
  }

  .movie-item {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 300px;
    cursor: pointer;
    transition: all 0.5s;
  }

  .movie-item:hover {
    transform: scale(1.1);
  }

  .movie-poster {
    width: 100%;
  }

  .movie-content {
    margin-top: 10px;
    font-size: 18px;
    text-align: center;
  }
</style>
