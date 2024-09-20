<script>
  import WelcomeBanner from "./lib/WelcomeBanner.svelte";
  import Swal from "sweetalert2";

  let goal = 10;

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
      snack: ["Popcorn,", "Candy"],
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
    Swal.fire({
      title: `${movieName}`,
      html: `During this movie you ate ${movieSnack.join(", ")}.<br />You stated: ${movieSnackThoughts}<br /><br />You watched this movie with ${moviePeople} other person(s).<br />You stated: ${moviePeopleThoughts}`,
      confirmButtonText: "DONE",
      confirmButtonColor: "#000",
      showCancelButton: true,
      cancelButtonText: "EDIT",
      cancelButtonColor: "#555",
      allowOutsideClick: false,
      allowEscapeKey: false,
    }).then((result) => {
      if (result.isDismissed) {
        alert("EDIT");
      }
    });
  }

  sortMoviesByDate();
</script>

<main>
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
              movie.userReviews,
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
  .statscontainer {
    position: absolute;
    top: 0;
    right: 0;
    z-index: 2;
    margin-top: 20px;
    margin-right: 20px;
  }

  .stats {
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
