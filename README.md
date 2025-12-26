# FastAPI Social Media Application

A modern social media application built with FastAPI, featuring user authentication, image/video uploads, and a social feed. The application uses ImageKit for media hosting and includes a Streamlit-based frontend.

## Features

- **User Authentication**: JWT-based authentication with FastAPI Users
  - User registration
  - Login/Logout
  - Password reset
  - Email verification
- **Media Upload**: Upload images and videos with captions
  - Supports images (PNG, JPG, JPEG)
  - Supports videos (MP4, AVI, MOV, MKV, WEBM)
  - Automatic upload to ImageKit CDN
- **Social Feed**: View all posts from all users
  - Chronological feed display
  - Caption overlays on images
  - Post ownership indicators
- **Post Management**: Delete your own posts
- **Modern UI**: Streamlit-based frontend with intuitive navigation

## Tech Stack

### Backend
- **FastAPI**: Modern, fast web framework for building APIs
- **SQLAlchemy**: Async ORM for database operations
- **SQLite**: Lightweight database (async via aiosqlite)
- **FastAPI Users**: Authentication and user management
- **ImageKit**: Image and video hosting/CDN
- **Uvicorn**: ASGI server

### Frontend
- **Streamlit**: Interactive web application framework

### Authentication
- **JWT**: JSON Web Tokens for secure authentication
- **Bearer Token**: Token-based authentication

## Project Structure

```
fastapi_tutorial/
├── app/
│   ├── app.py          # Main FastAPI application and routes
│   ├── db.py           # Database models and session management
│   ├── users.py        # User authentication configuration
│   ├── images.py       # ImageKit configuration
│   └── schemas.py      # Pydantic data schemas
├── main.py             # Application entry point
├── frontend.py         # Streamlit frontend application
├── pyproject.toml      # Project dependencies
└── README.md           # This file
```

## Installation

### Prerequisites
- Python 3.13 or higher
- ImageKit account (for media hosting)
- `uv` package manager (recommended) or `pip`

### Setup Steps

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd fastapi_tutorial
   ```

2. **Install dependencies**
   
   Using `uv` (recommended):
   ```bash
   uv sync
   ```
   
   Or using `pip`:
   ```bash
   pip install -r requirements.txt
   ```

3. **Configure environment variables**
   
   Create a `.env` file in the root directory:
   ```env
   SECRET_JWT=your-secret-jwt-key-here
   IMAGEKIT_PRIVATE_KEY=your-imagekit-private-key
   IMAGEKIT_PUBLIC_KEY=your-imagekit-public-key
   IMAGEKIT_URL=your-imagekit-url-endpoint
   ```

   You can generate a secure JWT secret using:
   ```python
   import secrets
   print(secrets.token_urlsafe(32))
   ```

4. **Initialize the database**
   
   The database will be automatically created when you first run the application.

## Running the Application

### Start the Backend Server

```bash
python main.py
```

The API will be available at `http://localhost:8000`

You can also access the interactive API documentation at:
- Swagger UI: `http://localhost:8000/docs`
- ReDoc: `http://localhost:8000/redoc`

### Start the Frontend

In a separate terminal:

```bash
streamlit run frontend.py
```

The frontend will be available at `http://localhost:8501`

## API Endpoints

### Authentication Endpoints

- `POST /auth/register` - Register a new user
  - Body: `{"email": "user@example.com", "password": "password123"}`
  
- `POST /auth/jwt/login` - Login and get JWT token
  - Body: `{"username": "user@example.com", "password": "password123"}`
  - Returns: `{"access_token": "..."}`
  
- `POST /auth/forgot-password` - Request password reset
- `POST /auth/reset-password` - Reset password with token
- `POST /auth/verify` - Verify email address

### User Endpoints

- `GET /users/me` - Get current user information (requires authentication)
- `PATCH /users/me` - Update current user (requires authentication)
- `GET /users/{user_id}` - Get user by ID (requires authentication)

### Post Endpoints

- `POST /upload` - Upload an image or video (requires authentication)
  - Form data:
    - `file`: The media file to upload
    - `caption`: Optional caption text
  
- `GET /feed` - Get all posts in chronological order (requires authentication)
  - Returns: `{"posts": [...]}`
  
- `DELETE /posts/{post_id}` - Delete a post (requires authentication, owner only)

## Usage

1. **Start both servers** (backend and frontend)
2. **Open the Streamlit app** in your browser (`http://localhost:8501`)
3. **Register a new account** or login with existing credentials
4. **Upload media** using the "Upload" page
5. **View the feed** to see all posts from all users
6. **Delete your posts** using the delete button (only visible on your own posts)

## Database Models

### User Model
- `id`: UUID (primary key)
- `email`: String (unique, required)
- `hashed_password`: String
- `is_active`: Boolean
- `is_superuser`: Boolean
- `is_verified`: Boolean

### Post Model
- `id`: UUID (primary key)
- `user_id`: UUID (foreign key to User)
- `caption`: Text (optional)
- `url`: String (ImageKit URL)
- `file_type`: String (image or video)
- `file_name`: String
- `created_at`: DateTime

## Development

### Database
The application uses SQLite with async support. The database file (`test.db`) is created automatically in the project root.

### Adding New Features
- API routes: Add to `app/app.py`
- Database models: Add to `app/db.py`
- Schemas: Add to `app/schemas.py`


Example API call:
```bash
# Login
curl -X POST "http://localhost:8000/auth/jwt/login" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "username=user@example.com&password=password123"

# Get feed (replace TOKEN with your access token)
curl -X GET "http://localhost:8000/feed" \
  -H "Authorization: Bearer TOKEN"
```

## Dependencies

- `aiosqlite==0.21.0` - Async SQLite driver
- `fastapi==0.118.0` - Web framework
- `fastapi-users[sqlalchemy]==14.0.1` - User management
- `imagekitio==4.2.0` - ImageKit SDK
- `python-dotenv==1.1.1` - Environment variable management
- `streamlit==1.50.0` - Frontend framework
- `uvicorn[standard]==0.37.0` - ASGI server

## Security Notes

- JWT tokens expire after 1 hour (3600 seconds)
- Passwords are hashed using FastAPI Users' default hashing
- Only post owners can delete their posts
- All endpoints except registration require authentication
