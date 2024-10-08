# Go Bookstore API
The Go Bookstore API is a RESTful service built using Go programming language, Gorilla Mux for HTTP routing, and GORM as the ORM for managing a MySQL database. The project demonstrates an efficient way to perform CRUD operations (Create, Read, Update, Delete) on a database of books.

This documentation details the structure, components, and functions of the project, ensuring a clear understanding of each part. The system is designed to be extendable, maintainable, and scalable for further enhancements.

## Table of Contents
- Project Structure
- Installation and Setup
- API Endpoints
- File and Package Breakdown
  - Main Application
  - Configuration
  - Controllers
  - Models
  - Routes
  - Utils
- Conclusion
## Project Structure
```bash
go-bookstore/
│
├── cmd/
│   └── main/
│       └── main.go           # Entry point for the application
│
├── pkg/
│   ├── config/
│   │   └── app.go            # Database configuration and connection
│   ├── controllers/
│   │   └── book-controller.go # Handlers for API requests
│   ├── models/
│   │   └── book.go           # Database model for the book resource
│   ├── routes/
│   │   └── bookstore-routes.go # API route definitions
│   └── utils/
│       └── utils.go          # Utility functions (e.g., request parsing)
│
├── go.mod                    # Go module definition
└── go.sum                    # Dependency checksums
```
## Installation and Setup
### Prerequisites
- Go 1.18 or above
- MySQL server
- Gorilla Mux package
- GORM (Go Object-Relational Mapping) package
### Steps to Run the Project:
#### **Clone the repository**:

```bash
git clone https://github.com/MikeOnBoard/goGORMAndMYSQL

cd go-bookstore
```
#### **Install dependencies**:

Run the following command to install the required Go packages:

```bash
go mod tidy
```
#### **Update database configuration**:

In the ``pkg/config/app.go`` file, replace the connection string with your own MySQL credentials:

```go
d, err := gorm.Open("mysql", "your_username:your_password@tcp(db-host:port)/your_database?charset=utf8&parseTime=True&loc=Local")
```
#### **Run the application**:

Execute the following command to start the server:

```bash
go run cmd/main/main.go
```
#### **Test the API**:

The API will be accessible at ``http://localhost:9010``. You can interact with it using tools like Postman or cURL.

## API Endpoints
The API exposes the following endpoints for CRUD operations on the book resource:
```
HTTP Method	    Endpoint	        Description

GET	            /book/	            Retrieve all books
GET	            /book/{bookId}	    Retrieve a single book by ID
POST            /book/	            Create a new book
PUT	            /book/{bookId}	    Update an existing book
DELETE	        /book/{bookId}	    Delete a book by ID
```
## File and Package Breakdown
### Main Application
``cmd/main/main.go``
This is the entry point of the application. It initializes the HTTP server and registers the necessary routes.

```go
func main() {
    r := mux.NewRouter()
    routes.RegisterBookStoreRoutes(r) // Register API routes
    http.Handle("/", r)
    log.Fatal(http.ListenAndServe("localhost:9010", r)) // Start the server on port 9010
}
```
### Configuration
``pkg/config/app.go``
The ``app.go`` file manages the database connection setup using GORM. The ``Connect()`` function establishes the connection to the MySQL database, and ``GetDB()`` returns the database instance for use throughout the application.

```go
func Connect() {
    d, err := gorm.Open("mysql", "your_username:your_password@tcp(db-host:port)/your_database?charset=utf8&parseTime=True&loc=Local")
    if err != nil {
        panic(err) // Handle connection errors
    }
    db = d
}
```
### Controllers
``pkg/controllers/book-controller.go``

This file contains the logic for handling the HTTP requests related to books. Each function corresponds to a specific API endpoint:

- **GetBook**: Retrieves all books from the database.
- **GetBookById**: Fetches a single book by its ID.
- **CreateBook**: Adds a new book to the database.
- **DeleteBook**: Removes a book by its ID.
- **UpdateBook**: Updates an existing book record.

Example: Creating a new book:

```go
func CreateBook(w http.ResponseWriter, r *http.Request) {
    CreateBook := &models.Book{}
    utils.ParseBody(r, CreateBook)
    b := CreateBook.CreateBook()
    res, _ := json.Marshal(b)
    w.WriteHeader(http.StatusOK)
    w.Write(res)
}
```
### Models
``pkg/models/book.go``
This file defines the Book model, which represents the book resource in the database. It uses GORM’s ORM features to interact with the database.

- **Book struct**: Defines the schema for a book.
- **CreateBook()**: Inserts a new book into the database.
- **GetAllBooks()**: Retrieves all books.
- **GetBookById()**: Fetches a book by its ID.
- **DeleteBook()**: Deletes a book by its ID.

Example: Book model definition:

```go
type Book struct {
    gorm.Model
    Name        string `json:"name"`
    Author      string `json:"author"`
    Publciation string `json:"publication"`
}
```
### Routes
``pkg/routes/bookstore-routes.go``

This file defines all the API routes. It maps the HTTP endpoints to their corresponding controller functions.

```go
func RegisterBookStoreRoutes(router *mux.Router) {
    router.HandleFunc("/book/", controllers.CreateBook).Methods("POST")
    router.HandleFunc("/book/", controllers.GetBook).Methods("GET")
    router.HandleFunc("/book/{bookId}", controllers.GetBookById).Methods("GET")
    router.HandleFunc("/book/{bookId}", controllers.UpdateBook).Methods("PUT")
    router.HandleFunc("/book/{bookId}", controllers.DeleteBook).Methods("DELETE")
}
```
### Utils
``pkg/utils/utils.go``

This file provides utility functions, such as parsing the HTTP request body. The ParseBody() function reads the request body and unmarshals the JSON data into a provided Go struct.

```go
func ParseBody(r *http.Request, x interface{}) {
    if body, err := io.ReadAll(r.Body); err == nil {
        if err := json.Unmarshal(body, x); err != nil {
            return
        }
    }
}
```
## Conclusion
The Go Bookstore API project showcases how to build a fully functional and modular REST API using Go, Gorilla Mux, and GORM. The API enables the management of book records with CRUD operations while interacting with a MySQL database.

By following best practices such as package organization, reusable utility functions, and structured API routes, this project offers a strong foundation for further development. The use of Go’s efficient concurrency and networking capabilities, paired with the power of GORM for database management, makes the application both performant and scalable.

The well-organized structure allows developers to easily navigate and extend the project, making it a solid example of clean code and Go application development.