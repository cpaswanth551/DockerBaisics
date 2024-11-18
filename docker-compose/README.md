
---

# Django with Docker Compose

This README serves as a guide to set up a Django project using Docker Compose. It includes an overview of concepts and commands for creating and running containers for both the Django application and the PostgreSQL database.

---

## Prerequisites
Before you begin, ensure the following are installed on your system:
- **Docker**: [Install Docker](https://www.docker.com/get-started)
- **Docker Compose**: [Install Docker Compose](https://docs.docker.com/compose/install/)

---

## Project Structure
Here’s the structure for this setup:

```
.
├── docker-compose.yml
├── Dockerfile
├── data
│   └── db
├── core
│   ├── settings.py
│   ├── urls.py
│   ├── ...
```

- **`docker-compose.yml`**: Defines services (`app` for Django, `db` for PostgreSQL).
- **`Dockerfile`**: Instructions for building the Django app image.
- **`data/db`**: Volume to persist database data.

---

## Docker Compose Workflow

### Part 1: Build and Run Docker Without Compose
1. Build the Docker image manually:
   ```bash
   docker build --tag python-django .
   ```
2. Run the container and map port 8000:
   ```bash
   docker run --publish 8000:8000 python-django
   ```

---

### Part 2: Setting Up Using Docker Compose
1. **Create the Django Project**:
   Build the service and start a Django project:
   ```bash
   docker-compose build
   docker-compose run --rm app django-admin startproject core .
   ```

2. **Start the Services**:
   Use `docker-compose up` to start both Django and PostgreSQL containers:
   ```bash
   docker-compose up
   ```

3. **Access the Django App**:
   Navigate to `http://localhost:8000` in your browser.

---

### Part 3: Interactive Shell Access
To access the Django container's shell:
```bash
docker exec -it django_container /bin/bash
```

---

## Docker Compose File Explanation

### `docker-compose.yml`

```yaml
version: "3.8"

services:
  app:
    build: .
    volumes:
      - .:/django
    ports:
      - 8000:8000
    image: app:django
    container_name: django_container
    command: python manage.py runserver 0.0.0.0:8000

  db:
    image: postgres
    volumes:
      - ./data/db:/var/lib/postgresql/data
    environment:
      - POSTGRES_DB=postgres
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
    container_name: postgres_db
```

- **`app` Service**:
  - Builds the Django app image from the current directory.
  - Maps volume to sync code between the host and the container.
  - Maps container port 8000 to the host port 8000.
  - Runs the Django development server.

- **`db` Service**:
  - Uses the official PostgreSQL image.
  - Persists database data using a volume.
  - Configures database name, user, and password via environment variables.

---

## Commands Reference

### General Commands
- Build the services:
  ```bash
  docker-compose build
  ```
- Run a specific command (e.g., creating a Django project):
  ```bash
  docker-compose run --rm app django-admin startproject core .
  ```
- Start all services:
  ```bash
  docker-compose up
  ```
- Stop all services:
  ```bash
  docker-compose down
  ```

### Debugging and Development
- Access a container shell:
  ```bash
  docker exec -it django_container /bin/bash
  ```
- Check logs for a service:
  ```bash
  docker-compose logs app
  ```

---

## Notes
1. The database data is stored in the `data/db` directory, ensuring it persists across container restarts.
2. You can modify `POSTGRES_DB`, `POSTGRES_USER`, and `POSTGRES_PASSWORD` in `docker-compose.yml` as needed.

---

This README will help you understand and quickly set up Docker Compose for Django projects in the future.