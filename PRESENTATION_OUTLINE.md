# Presentation Outline

Total time: 12 minutes demo + slides, then 3 minutes Q&A.

## Slide 1 - Project Title (1 min)

- Movie Industry Analytics.
- Group members.
- Stack: TMDB, dlt, PostgreSQL, dbt, Streamlit.

## Slide 2 - Data Source (2 min)

- TMDB API and entertainment analytics use case.
- Explain the three endpoint roles:
  - Discover movies.
  - Fetch movie details for budget/revenue.
  - Load genre lookup data.
- Mention API key handling through `.env`.

## Slide 3 - Architecture (2 min)

- Walk through: TMDB -> `pipeline.py` -> PostgreSQL `raw` -> dbt `analytics` -> Streamlit.
- Explain why this is ELT.
- Explain raw/staging/mart separation.

## Slide 4 - Live Demo (4 min)

Run:

```bash
docker compose up -d
python pipeline.py
cd analytics
dbt run --profiles-dir .
dbt test --profiles-dir .
cd ..
streamlit run dashboard.py
```

Show:

- Raw tables loaded.
- dbt models built.
- Dashboard filters and charts.

## Slide 5 - Data Quality (1 min)

- Show `dbt test` output.
- Explain:
  - Not-null checks.
  - Unique movie ID.
  - Vote average range.
  - Positive budget/revenue and non-null ROI.

## Slide 6 - Challenges And Lessons Learned (1 min)

- Discover endpoint lacked budget/revenue.
- Zero budgets/revenues required filtering.
- Genre IDs required many-to-many expansion.
- Bounded pagination improved live-demo reliability.

## Q&A Preparation

Each group member should be ready to explain:

- Why the pipeline uses `/movie/{id}` after `/discover/movie`.
- What dlt does automatically.
- Why raw and analytics schemas are separate.
- How `stg_movies` calculates ROI.
- How `genre_decade_summary` joins genres and groups decades.
- What each dbt test protects against.
- How dashboard filters change the charts.

