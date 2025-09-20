# architecure


#### Front-End
>HTML, CSS, and JavaScript (with Bootstrap): The BookBuddy frontend relies on standard web technologies to provide a clear and responsive interface.
 HTML defines the structure of pages such as Home, Discovery/Form, Recommendations, My Library, Book Detail, and Profile. 
 
CSS applies styling to maintain a consistent and user-friendly design across devices, while Bootstrap ensures responsiveness for different screen sizes. JavaScript manages dynamic interactions like handling search results, updating reading progress, validating forms, and submitting user preferences. These interactions follow a logical flow from user actions to local state updates and server communication, which can be represented in a flowchart diagram that traces the journey from UI input to backend processing.

#### Back-End
>Flask and SQLAlchemy: The backend uses Flask for routing and request handling, chosen for its modularity and lightweight design that allows easy integration of new features. Flask routes power key functionalities such as login, registration, book search, reading list management, profile updates, and recommendation generation. SQLAlchemy provides ORM-based database interaction, ensuring persistence for entities like users, preferences, books, and reading lists.

 External APIs such as Google Books and Goodreads are integrated via adapter modules, allowing seamless retrieval of book metadata. The interactions between the frontend browser logic, Flask routes, services, repositories, and external APIs can be represented through a sequence diagram, highlighting the flow of requests and responses when, for example, a user searches for a book or adds one to their personal library.
 
 
# interaction

After the registration and login, users are redirected to the homepage. From here, they can search for new books or manage their personal library. The Flowchart Diagram below maps the logical flow from entering a search query to adding a book to the user’s library, with updates saved both in the database and visible on the dashboard.

#### When a user requests recommendations, the system follows this flow: 
1. The request is initiated via the frontend from the Recommendations page. 
2. The backend retrieves user preferences and reading history from the database. 
3. The Recommendation Service calls the Google Books and Goodreads APIs to fetch candidate books. 
4. AI ranking is applied, and the top results are returned to the frontend. 
5. The frontend displays the recommended books with covers, descriptions, and buy links, while storing relevant history for better future suggestions.




# Behaviour 

##### The diagram gives a high-level view of BookBuddy’s behavior:

1. **Start:** The app initializes with no user logged in.
2. **Authentication** The user registers or logs in to access their dashboard.
3. **Browsing/Searching:** The user explores the catalog or submits a search query.
4. **Book Detail:** After selecting a book, metadata is displayed.
5. **Add to Library:** The user adds the book to their personal reading list with a chosen status.
6. **Recommendation Processing:** Preferences and history are analyzed, and external APIs are queried.
7. **Result Displayed:** Personalized recommendations are shown with covers, descriptions, and links.
8. **Error State:** If login fails, a query returns no results, or external APIs fail, the user is notified with an error message.



# Domain-driven Design (DDD) — BookBuddy
###### BookBuddy centers on managing personal reading and discovery. Users track reading lists, set preferences, and receive AI-based book recommendations. Target users: readers, students, book clubs.


#### Bounded Contexts
1.  Accounts: registration, authentication, and profile management.
2.  Catalog:** book metadata from Google Books / Goodreads APIs.
3.  Reading Lists:** personal shelves with status and progress tracking.
4.  Preferences:** user-selected genres, languages, and reading goals.
5.  Recommendations:** AI-driven su

### Domain Concepts
**Entities / Aggregates:**
 1.  User (Accounts) → id, email, username, passwordHash.
 2.  Book (Catalog) → id, title, authors, genre, cover, summary.
 3.  ReadingList (Reading Lists) → id, userId, items (status, progress, timestamps).
 4.  UserPreferences (Preferences) → genres, language, goals, notifications.
 5.  RecommendationSession (Recommendations) → inputs (prefs + history), candidates, ranked results.
 
**Value Objects:** ReadingStatus (Want, Reading, Finished), Progress value, Genre, Language.

### Repositories / Services / Factories
 1. Repositories:UserRepository, BookRepository, ReadingListRepository, UserPreferencesRepository.
 2. Services: SearchService (Catalog), RecommendationService (Recommendations), ProfileService (Preferences).
3. Factories: BookFactory (creates Book aggregates from external API data)


### Relevant Domain Events
1. Accounts: UserRegistered, UserLoggedIn.
2. Catalog: BookImported, BookEnriched.
3. Reading Lists: BookAddedToReadingList, ReadingProgressUpdated, ReadingStatusChanged.
4. Preferences: UserPreferencesUpdated.
5. Recommendations: RecommendationsGenerated, RecommendationClicked.


# Data-related aspects
#### There are some data need to be store such as:  
1. user:(id, email, username, password_hash, privacy), 
2. 	user preferences : (genres, language, goals, notifications), 
3.  books: (external_id/ISBN, title, authors, cover, year, language, summary),
4. reading list entries : (user_id, book_id, status, progress, timestamps).



##### Keep them in a SQL database (SQLite for dev, PostgreSQL for prod). Cache optional short-lived recommendation results in memory or Redis.

>**Use relational storage to store persistent data**
Reason: strong relations (user ↔ preferences, user ↔ reading_list ↔ book), integrity constraints, ACID transactions, and efficient joins for library views and stats. Use a key-value cache only to reduce latency for hot book metadata and recommendation payloads.


#### Querying components:
1.	Auth/Accounts on register/login:** SELECT user by email, INSERT user.
2. Profile/Preferences on dashboard load/update:** SELECT/UPDATE preferences by user_id.
3. Catalog/Books on first view/import:** UPSERT book by external_id/ISBN; SELECT details for pages.
4.	ReadingList on add/status/progress:** INSERT reading_list_item, UPDATE progress/status; SELECT list (JOIN books) for “My Library.”
5. RecommendationService when generating results:** SELECT preferences and reading history; optional INSERT of a transient recommendation session.









