# alx-project-0x14

## API Overview

The Movie Database API provides access to a large collection of movies and TV-shows metadata: you can retrieve detailed information about movies and series (titles, descriptions, release dates, genres, runtime, ratings, images, cast/crew, and more), perform searches by keyword, get lists of popular or top-rated items, and support multiple languages. This makes it ideal for building applications that display movie catalogs, search results, or personalized watchlists. :contentReference[oaicite:1]{index=1}

## Version

As of this documentation, the API version used is **v3** (the common, key-based version). :contentReference[oaicite:2]{index=2}

## Available Endpoints

Here are some of the main endpoints provided by the API: :contentReference[oaicite:3]{index=3}

| Endpoint (path) | Description |
|-----------------|-------------|
| `/search/movie` | Search for movies by a keyword. Returns a list of movies matching the search. :contentReference[oaicite:4]{index=4} |
| `/movie/{movie_id}` | Retrieve detailed information for a specific movie (title, overview, release date, genres, runtime, poster path, ratings, etc.). :contentReference[oaicite:5]{index=5} |
| `/search/tv` | Search for TV shows by keyword (if you’re also dealing with series). :contentReference[oaicite:6]{index=6} |
| `/tv/{tv_show_id}` | Get details for a specific TV show (similar metadata as movies). :contentReference[oaicite:7]{index=7} |
| (other endpoints: lists, credits, images, etc.) | The API offers many additional endpoints for cast/crew, movie images/posters, trending/popular lists, ratings, and more. :contentReference[oaicite:8]{index=8} |

> Note: This list is not exhaustive — the API supports 100+ endpoints per its docs. :contentReference[oaicite:9]{index=9}

## Request and Response Format

- **Request format**: Typical requests are HTTP GET calls to a URL based on the base URL plus endpoint path, with query parameters (e.g. API key, search query, language, etc.). Example (search by keyword):
  ```
  GET https://api.themoviedb.org/3/search/movie?api_key=YOUR_KEY&query=Avengers&language=en-US
  ```
  :contentReference[oaicite:10]{index=10}

- **Response format**: The API returns JSON. For example, a search response might look like:
  ```json
  {
    "page": 1,
    "results": [
      {
        "id": 299534,
        "title": "Avengers: Endgame",
        "overview": "...",
        "release_date": "2019-04-24",
        "genre_ids": [12, 878, 28],
        ...
      },
      ...
    ],
    "total_results": 100,
    "total_pages": 5
  }
  ```
  And a movie-details response (for `/movie/{movie_id}`) might include fields like `"id"`, `"title"`, `"overview"`, `"release_date"`, `"genres"`, `"runtime"`, `"poster_path"`, `"vote_average"`, etc. :contentReference[oaicite:11]{index=11}

Because you're using TypeScript, you can mirror this structure by defining interfaces such as:

```ts
interface MovieSummary {
  id: number;
  title: string;
  overview: string;
  release_date: string;
  genre_ids: number[];
  // …other fields
}

interface MovieDetails {
  id: number;
  title: string;
  overview: string;
  release_date: string;
  genres: { id: number; name: string }[];
  runtime: number;
  poster_path: string | null;
  vote_average: number;
  vote_count: number;
  // …other fields
}

interface SearchResponse {
  page: number;
  results: MovieSummary[];
  total_results: number;
  total_pages: number;
}
```

## Authentication

To use the API you typically need an **API key**. For version v3, this API key is passed as a query parameter `api_key` in each request. :contentReference[oaicite:12]{index=12}  
If you use version v4 (some APIs offer it), authentication may require an access token in the HTTP header (e.g. `Authorization: Bearer YOUR_V4_TOKEN`). :contentReference[oaicite:13]{index=13}

## Error Handling

Common error responses may include:
- **401 Unauthorized** — when the API key is missing or invalid.
- **404 Not Found** — when requesting a movie/TV show by an invalid ID.
- **429 Too Many Requests** — if you exceed usage limits (if there are rate limits).  
In your code, you should check the HTTP status code and handle such cases appropriately (e.g. show error message, retry, fallback).  

Also, always validate that the JSON response has the expected fields before using them (since sometimes optional fields like `poster_path` may be null).

## Usage Limits and Best Practices

- Respect the API’s rate limits. For many public movie-database APIs, there are request-per-second or daily request limits (e.g. free tiers). For example, some movie APIs on marketplaces allow up to 60 requests per second for paid plans. :contentReference[oaicite:14]{index=14}  
- Cache responses when possible (e.g. movie details) to avoid repeated calls for the same resource: this reduces latency and conserves your quota.  
- Use pagination if you’re requesting large result sets (search results, lists) to avoid overloading both your app and the API.  
- Handle missing or optional data gracefully (e.g. optional poster paths, missing fields).  
- Consider internationalization/localization if your app supports multiple languages (many APIs support a `language` parameter). :contentReference[oaicite:15]{index=15}  

