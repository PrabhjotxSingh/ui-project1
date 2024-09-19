<script>
    import WelcomeBanner from './lib/WelcomeBanner.svelte';

    let movies = [
        {
            name: "Interstellar",
            rating: 5,
            userReview: "I liked this movie a lot, one of my favs!",
            reviewTime: "9/19/2024, 12:09:17 AM",
            description: "The adventures of a group of explorers who make use of a newly discovered wormhole to surpass the limitations on human space travel and conquer the vast distances involved in an interstellar voyage.",
            tags: ["Sci-Fi", "Drama", "Space"],
            posterUrl: "https://image.tmdb.org/t/p/w300/gEU2QniE6E77NI6lCU6MxlNBvIx.jpg"
        },
        {
            name: "The Matrix",
            rating: 4,
            userReview: "Everyone talks about it, it's pretty good!",
            reviewTime: "9/19/2024, 12:09:17 AM",
            description: "Set in the 22nd century, The Matrix tells the story of a computer hacker who joins a group of underground insurgents fighting the vast and powerful computers who now rule the earth.",
            tags: ["Sci-Fi", "Action", "Philosophical"],
            posterUrl: "https://image.tmdb.org/t/p/w300/f89U3ADr1oiB1s9GkdPOEpXUk5H.jpg"
        },
        {
            name: "Inception",
            rating: 5,
            userReview: "Had me on edge!",
            reviewTime: "9/19/2024, 12:09:17 AM",
            description: "Dom Cobb (Leonardo DiCaprio) is a thief with the rare ability to enter people's dreams and steal their secrets from their subconscious.",
            tags: ["Sci-Fi", "Action"],
            posterUrl: "https://image.tmdb.org/t/p/w300/ljsZTbVsrQSqZgWeep2B1QiDKuh.jpg"
        },
        {
            name: "Cats",
            rating: 1,
            userReview: "Just awful!",
            reviewTime: "9/19/2024, 12:09:17 AM",
            description: "A tribe of cats compete during the annual Jellicle Ball, where one lucky feline will be chosen to ascend to the Heaviside Layer and be granted a new life.",
            tags: ["Musical", "Family"],
            posterUrl: "https://image.tmdb.org/t/p/w300/aCNch5FmzT2WaUcY44925owIZXY.jpg"
        },
        {
            name: "The Silence of the Lambs",
            rating: 4,
            userReview: "Spooky and interesting!",
            reviewTime: "9/19/2024, 12:09:17 AM",
            description: "A young FBI cadet must receive the help of an incarcerated and manipulative cannibal killer to help catch another serial killer, a madman who skins his victims.",
            tags: ["Crime", "Horror"],
            posterUrl: "https://image.tmdb.org/t/p/w300/uS9m8OBk1A8eM9I042bx8XXpqAq.jpg"
        },
    ];

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

    sortMoviesByDate();
</script>

<main>
    <WelcomeBanner on:addMovie={handleAddMovie} />
    <div class="centered-container">
        <div class="movies-container">
            {#each movies as movie}
            <div class="movie-item">
              <img title="{movie.description}" draggable="false" src="{movie.posterUrl}" alt="Poster" class="movie-poster" />
              <p class="movie-content"><b>{movie.name}</b> ★ {movie.rating}<br>{movie.tags.join(", ")}<br>{movie.userReview}<br />{movie.reviewTime}</p>
          </div>
          {/each}
      </div>
</main>

<style>
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