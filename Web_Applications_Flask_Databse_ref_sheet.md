# Flask Database Routes Reference Sheet
## Core Concept
Flask routes interact with database-backed Repository classes to handle HTTP requests and database operations.

## Request Flow
1. Client → HTTP request → Flask Server
2. Flask Server → calls method → Repository Class
3. Repository Class → SQL query → Database
4. Database → result set → Repository Class
5. Repository Class → objects → Flask Server
6. Flask Server → HTTP response → Client

## CRUD Operations & HTTP Methods
![alt text](<Screenshot 2025-07-01 at 09.11.17.png>)

## Path Parameters
Extract variable parts from URLs:
```python
@app.route('/books/<id>')
def get_book(id):
    # id is extracted from URL path
    return f"Book {id}"
```

## Database Connection Pattern
```python
from lib.database_connection import get_flask_database_connection
from lib.book_repository import BookRepository

@app.route('/books', methods=['GET'])
def get_books():
    connection = get_flask_database_connection(app)
    repository = BookRepository(connection)
    books = repository.all()
    # Process and return books
```

## Common Route Examples

POST Route (Create)
```python
@app.route('/albums', methods=['POST'])
def create_album():
    connection = get_flask_database_connection(app)
    repository = AlbumRepository(connection)
    
    title = request.form['title']
    release_year = request.form['release_year']
    artist_id = request.form['artist_id']
    
    album = Album(None, title, release_year, artist_id)
    repository.create(album)
    
    return '', 200  # No content response
```

GET Route (Read All)
```python
@app.route('/artists', methods=['GET'])
def get_artists():
    connection = get_flask_database_connection(app)
    repository = ArtistRepository(connection)
    artists = repository.all()
    
    return ', '.join([artist.name for artist in artists])
```

GET Route (Read One)
```python
@app.route('/books/<id>', methods=['GET'])
def get_book(id):
    connection = get_flask_database_connection(app)
    repository = BookRepository(connection)
    book = repository.find(id)
    
    return f"{book.title} by {book.author}"
```

## RESTful Routing

- REST = Representational State Transfer
- Maps HTTP methods to CRUD operations on resources
- Consistent URL patterns for predictable API design

## Key Testing Points
- Test that POST requests create resources in database
- Verify new resources appear in subsequent GET requests
- Assert correct HTTP status codes (200, 201, etc.)
- Check request/response formats match requirements

## Quick Setup Checklist

- ✅ Import get_flask_database_connection
- ✅ Import relevant Repository class
- ✅ Create database connection in route
- ✅ Instantiate repository with connection
- ✅ Call repository methods
- ✅ Return appropriate response


