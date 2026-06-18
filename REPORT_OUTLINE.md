# Technical Report Outline

Target length: 2,800-3,200 words, excluding code snippets and figure captions.

## 1. Introduction (~300 words)

- Problem statement: movie studios, analysts, and investors need reliable views of genre performance, ratings, and box-office returns.
- Real-world relevance: budgeting, portfolio planning, genre trend analysis, and release strategy.
- Users: entertainment analysts, producers, distributors, and data teams.

## 2. Data Source & API (~300 words)

- Source: TMDB API.
- Endpoints:
  - `/discover/movie` for bounded movie discovery and pagination.
  - `/movie/{movie_id}` for budget, revenue, ratings, popularity, and genres.
  - `/genre/movie/list` for genre reference data.
- Fields ingested: movie ID, title, release date, genre IDs, budget, revenue, vote average, vote count, popularity.
- Data quality notes: missing/zero budget or revenue, incomplete release dates, vote-count bias, API rate limits, and TMDB community-maintained metadata.

## 3. Pipeline Architecture (~400 words)

- Architecture: TMDB -> Python/dlt -> PostgreSQL raw schema -> dbt staging/mart -> Streamlit.
- Explain ELT: raw JSON-derived records land first, transformations happen inside PostgreSQL with dbt.
- Tool justification:
  - dlt handles API extraction, schema inference, and PostgreSQL loading.
  - PostgreSQL stores raw and analytical schemas.
  - dbt creates documented, tested SQL transformations.
  - Streamlit serves an interactive dashboard.

## 4. Data Modeling (~400 words)

- Raw schema:
  - `raw.movies`
  - `raw.genres`
- Staging model:
  - `analytics.stg_movies`
  - Cleans dates, calculates release year, filters invalid budget/revenue, calculates ROI.
- Mart model:
  - `analytics.genre_decade_summary`
  - Joins movies to genres and aggregates by genre plus decade.
- Modeling decision: keep raw close to the API, use staging for reusable cleaned movie records, use a mart for dashboard-ready OLAP analysis.

## 5. Transformation Logic (~400 words)

- `stg_movies`:
  - Casts `release_date`.
  - Extracts `release_year`.
  - Filters `budget > 0` and `revenue > 0`.
  - Calculates `roi = (revenue - budget) / budget`.
- `genre_decade_summary`:
  - Expands JSON genre IDs.
  - Joins to the genre lookup table.
  - Derives decade using release year.
  - Calculates average rating, average ROI, and film count.

## 6. Data Quality (~300 words)

- dbt tests:
  - `not_null` on movie ID, title, and release year.
  - `unique` on movie ID.
  - Custom test for vote average between 0 and 10.
  - Custom checks for positive budget/revenue and non-null ROI.
- Include a screenshot or pasted summary of `dbt test --profiles-dir .` results.

## 7. Dashboard & Visualisation (~400 words)

- KPI row: total movies loaded, average rating, highest grossing film.
- Bar chart: top 10 genres by average ROI.
- Line chart: average rating by release year.
- Scatter plot: budget vs revenue, colored by genre.
- Filters: genre, decade, minimum vote count.
- Explain why each chart matches the business question.

## 8. Challenges & Lessons Learned (~300 words)

- TMDB discover does not include budget/revenue, so the pipeline fetches movie details per discovered movie.
- Budget and revenue often contain zeros, requiring quality filters.
- Genre is many-to-many, so genre IDs must be expanded before aggregation.
- Live-demo reliability required bounded pagination and environment variables.

## 9. Conclusion (~200 words)

- Summarize the completed ELT pipeline and dashboard.
- Discuss production readiness: scheduling, incremental loading, monitoring, richer quality checks.
- Future enhancements: credits/directors, correlation analysis, and larger historical ingestion.

