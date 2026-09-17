# NewsGovInsights — Backend

FastAPI service that monitors news coverage of government ministries.
It crawls configured news sources, classifies each article by ministry,
scores its sentiment, and surfaces flagged articles through a dashboard
API and email alerts.

## How it works

1. **Source profiles** — news sources are configured in the database
   (base URL, language, crawling strategy) and seeded via `insert.sql`.
2. **Crawling & extraction** — scheduled crawls pull articles; a single
   article can also be submitted by URL.
3. **Classification** — a fine-tuned **DistilBERT** model maps each
   article to the relevant ministry.
4. **Sentiment** — a fine-tuned **RoBERTa** model scores public
   perception as positive, neutral or negative.
5. **Alerting** — flagged articles trigger email notifications.

## Stack

- **API**: FastAPI, Uvicorn
- **ML**: HuggingFace Transformers (TensorFlow), models fine-tuned and
  hosted on the HuggingFace Hub
- **Database**: PostgreSQL via SQLAlchemy
- **Crawling**: BeautifulSoup4, crawl4ai
- **Email**: Brevo
- **Deployment**: Docker

## Layout

api/
├── main.py          # app entrypoint
├── ml_models.py     # loads the fine-tuned classifier + sentiment models
├── scheduler.py     # scheduled crawl jobs
├── database.py      # SQLAlchemy session
├── routers/         # articles, dashboard, detector, emails, profiles, trigger
└── utils/           # crawl pipelines, extraction, prediction, templates

## Running locally

Set `DATABASE_URL`, `HF_TOKEN`, `BREVO_API_KEY`, `FROM_EMAIL` in a `.env`, then:

docker build -t news-sentiment-backend .
docker run -d -p 8000:8000 --env-file .env news-sentiment-backend

Docs at `http://localhost:8000/docs`.
