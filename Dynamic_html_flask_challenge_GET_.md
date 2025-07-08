# Dynamic Route Problem-Solving Notes

Challenge: Building /albums/<id> Route with Artist Data

## Initial Problem Breakdown

Goal: Create a route that shows a single album with artist name (not just ID)

Starting point: Had working /albums route that shows all albums

Key insight: Single item routes are similar to list routes, just with different data fetching

## Problem-Solving Journey
### Step 1: Route Pattern Research

Challenge: How to capture URL parameters in Flask

Solution: Research "flask route variable parameters"

Pattern learned: @app.route('/albums/<album_id>') captures album_id as function parameter

Key insight: Building on existing working code rather than starting from scratch

### Step 2: Data Dependency Discovery

Template needs artist name, but album.artist_id only gives ID number

Would have seen "Artist: 2" in browser instead of "Artist: Pixies"

Problem-solving approach: Trace the data flow - what do I have vs what do I need?

### Step 3: Repository Method Gap

Challenge: Needed ArtistRepository.find(id) method that didn't exist

Solution: Use existing AlbumRepository.find() as template

Pattern: Copy similar working code and modify for new use case

TDD approach: Write test first, then implement method

### Step 4: Route Data Assembly

- Pattern learned: Complex routes often need multiple repository calls

- Solution structure:
```python
# Get album first
album = album_repository.find(album_id)
# Use album data to get related data
artist = artist_repository.find(album.artist_id)
# Pass both to template
return render_template("albums/show.html", album=album, artist=artist)
```


### Step 5: Template Creation

- File organization: Follow existing pattern (albums/index.html → albums/show.html)

- HTML reality check: Line breaks in HTML code ≠ line breaks in browser display

- Solution: Use <br> tags or separate elements for visual formatting

Key Problem-Solving Strategies That Worked




