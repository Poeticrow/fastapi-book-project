# FastAPI Book Management API

## Overview

This project is a RESTful API built with FastAPI for managing a book collection. It provides comprehensive CRUD (Create, Read, Update, Delete) operations for books with proper error handling, input validation, and documentation. The project is deployed using **Google Cloud Compute Engine**, with **Nginx as a reverse proxy**, and an automated **CI/CD pipeline using GitHub Actions**.

## Features

- 📚 Book management (CRUD operations)
- ✅ Input validation using Pydantic models
- 🔍 Enum-based genre classification
- 🧪 Complete test coverage
- 📝 API documentation (auto-generated by FastAPI)
- 🔒 CORS middleware enabled
- 🚀 **Automated deployment using GitHub Actions & Google Cloud**
- 🔄 **Reverse proxy setup with Nginx**

## Project Structure

```
fastapi-book-project/
├── api/
│   ├── db/
│   │   ├── __init__.py
│   │   └── schemas.py      # Data models and in-memory database
│   ├── routes/
│   │   ├── __init__.py
│   │   └── books.py        # Book route handlers
│   └── router.py           # API router configuration
├── core/
│   ├── __init__.py
│   └── config.py           # Application settings
├── tests/
│   ├── __init__.py
│   └── test_books.py       # API endpoint tests
├── .github/workflows/      # GitHub Actions CI/CD workflows
├── main.py                 # Application entry point
├── requirements.txt        # Project dependencies
└── README.md
```

## Technologies Used

- Python 3.12
- FastAPI
- Pydantic
- pytest
- uvicorn
- Nginx (for reverse proxy)
- Google Cloud Compute Engine (Deployment)
- GitHub Actions (CI/CD Automation)

## Installation

1. Clone the repository:

```bash
git clone https://github.com/hng12-devbotops/fastapi-book-project.git
cd fastapi-book-project
```

2. Create a virtual environment:

```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. Install dependencies:

```bash
pip install -r requirements.txt
```

## Running the Application

1. Start the server:

```bash
uvicorn main:app --host 0.0.0.0 --port 8000
```

2. Access the API documentation:

- Swagger UI: http://localhost:8000/docs
- ReDoc: http://localhost:8000/redoc

## API Endpoints

### Books

- `GET /api/v1/books/` - Get all books
- `GET /api/v1/books/{book_id}` - Get a specific book
- `POST /api/v1/books/` - Create a new book
- `PUT /api/v1/books/{book_id}` - Update a book
- `DELETE /api/v1/books/{book_id}` - Delete a book

### Health Check

- `GET /healthcheck` - Check API status

## Book Schema

```json
{
  "id": 1,
  "title": "Book Title",
  "author": "Author Name",
  "publication_year": 2024,
  "genre": "Fantasy"
}
```

Available genres:

- Science Fiction
- Fantasy
- Horror
- Mystery
- Romance
- Thriller

## Running Tests

```bash
pytest
```

## Error Handling

The API includes proper error handling for:

- Non-existent books
- Invalid book IDs
- Invalid genre types
- Malformed requests

## CI/CD Pipeline (GitHub Actions)

This project has an automated **CI/CD pipeline** using GitHub Actions:

### **1️⃣ CI: Continuous Integration**

- Runs **pytest** on every pull request to the `main` branch.
- Ensures all tests pass before merging.

### **2️⃣ CD: Continuous Deployment**

- On merging to `main`, the workflow:
  - **SSHs into the Google Cloud VM**
  - **Pulls the latest code from GitHub**
  - **Installs dependencies**
  - **Restarts FastAPI & Nginx services**

#### **Deployment Workflow (`.github/workflows/deploy.yml`):**

```yaml
name: Deploy FastAPI

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v3

      - name: Set up SSH
        run: |
          echo "${{ secrets.SSH_PRIVATE_KEY }}" > private_key
          chmod 600 private_key

      - name: Deploy to Google Cloud VM
        run: |
          ssh -o StrictHostKeyChecking=no -i private_key ${{ secrets.SERVER_USER }}@${{ secrets.SERVER_IP }} << 'EOF'
            cd /home/${{ secrets.SERVER_USER }}/fastapi-book-project
            git pull origin main
            source venv/bin/activate
            pip install -r requirements.txt
            sudo systemctl restart fastapi
            sudo systemctl restart nginx
          EOF
```

## Deployment Details

### **Google Cloud VM + Nginx Setup**

- The API is hosted on **Google Cloud Compute Engine**.
- **Nginx** is used as a **reverse proxy** to serve FastAPI.
- The server listens on `0.0.0.0:8000` and is proxied via Nginx.

To check the running application, visit:

```
http://YOUR_SERVER_IP/api/v1/books
```

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add AmazingFeature'`)
4. Push to branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support

For support, please open an issue in the GitHub repository.
