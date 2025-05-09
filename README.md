# Project Overview

This project consists of two main components: the **Pipeline** and the **App**.

## Architecture

- **Pipeline**: Responsible for scraping data from external sources and loading it into a MySQL database.
- **App**: A Go-based CRUD API for interacting with the data stored in the MySQL database. Additional features include job management, caching with Redis, Kafka integration for message-based communication, and Elasticsearch for search functionality.

## Components

### Pipeline

The pipeline is implemented in Python and consists of:

1. **scrape.py**: 
   - Scrapes data from external sources for various categories.
   - Utilizes helper modules from `pipeline/utils/` for fetching, parsing, and indexing data.
   - **To run:**
     ```bash
     # Activate the Python virtual environment (PVM)
     source simplebe/bin/activate
     
     cd pipeline
     
     python scrape.py
     ```

2. **ingest.py**:
   - Loads the scraped data into a MySQL database.
   - Processes and validates data before insertion.
   - **To run:**
     ```bash
     # Activate the Python virtual environment (PVM)
     source simplebe/bin/activate
     
     cd pipeline
     
     python ingest.py
     ```

3. **scheduler.py**:
   - Uses APScheduler to schedule scraping jobs.
   - Automates the scraping process at defined intervals.

### App

The app is built with Go and provides a RESTful API with endpoints for CRUD operations, job fetching, status checking, and search.

- **News Endpoints:**
  - `GET /news/:category` - Retrieve news by category.
  - `POST /news/:category` - Add news for a specific category.
  - `PUT /news/:category/:id` - Update a news item.
  - `DELETE /news/:category/:id` - Delete a news item.

- **Job Endpoints:**
  - `POST /jobs/fetch/:topic` - Trigger a scraping job.
  - `GET /jobs/ping/:id` - Check job status.
  - `GET /jobs/result/:category` - Get scraped results for a category.

- **Search Endpoint:**
  - `POST /search/:simple` - Trigger a simple search query.

- **To run the app:**
  ```bash
  cd app
  
  go run .
  ```
  After startup, you can use Postman or a browser to access endpoints, for example:  
  `http://localhost:8080/news/the_gioi/`

## Docker Setup

A `docker-compose.yml` file is included to orchestrate multiple services:

- **Services:**
  - **MySQL**: Stores news, job, and scraped result data.
  - **Redis**: Provides caching for news responses.
  - **Kafka & Zookeeper**: Handle messaging for job operations.
  - **Elasticsearch**: Powers search functionality.

- **To run the full stack via Docker:**
  ```bash
  docker-compose up --build
  ```

## Database Migration

The app uses GORM for database migrations. Migration files are located in `app/migration/`.

On startup, the migration is executed automatically:
```go
err := migration.Migration(database)
if err != nil {
    log.Fatalf("Failed to migrate database: %v", err)
}
```

## Configuration

- **App Configuration:**  
  Located in `app/config/config.json` – includes settings for the database, Redis, Kafka, etc.
  
- **Pipeline Configuration:**  
  Located in `pipeline/config.py` – parses the database and Redis URLs among other settings.

## Getting Started

1. **Clone the repository.**
2. **Configure the environment:**
   - Update `app/config/config.json` according to your local or Docker environment.
3. **Run the services:**
   - Either use Docker via `docker-compose up --build`
   - Or run services individually (see instructions above for the Pipeline and App).
4. **Interact with the API:**
   - Use Postman or your terminal and test endpoints such as ` curl -X POST http://localhost:8080/news/the_gioi/`