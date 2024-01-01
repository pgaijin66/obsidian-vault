
#### What is DDD ?

DDD is an approach to software development that focuses on building complex systems by placing the domain at the heart of the design process. It emphasizes a deep understanding of the business domain and aligns the software design with domain concepts and terminology. Here is a high level overview of the key principles and concepts of DDD.


#### DDD Key principles

1. Ubiquitous language
2. Bounded contexts
3. Domain Model
4. Aggregate
5. Value Object
6. Domain Services
7. Repositories
8. Aggregates and Consistency
9. Event-Driven Architecture
10. Continuous Refinement


### Method 1

```
package main

import (
    "fmt"
    "log"
)

// User is an aggregate in the domain.
type User struct {
    ID int
    Name string
    Email string
}

// UserRepository is an interface that provides access to the user data.
type UserRepository interface {
    GetUser(id int) (*User, error)
    CreateUser(user *User) error
    UpdateUser(user *User) error
    DeleteUser(id int) error
}

// UserService is a service that performs business operations on users.
type UserService struct {
    repository UserRepository
}

// CreateUser creates a new user.
func (s *UserService) CreateUser(user *User) error {
    // Validate the user input.
    if user.Name == "" {
        return fmt.Errorf("Name is required")
    }
    if user.Email == "" {
        return fmt.Errorf("Email is required")
    }

    // Save the user to the repository.
    err := s.repository.CreateUser(user)
    if err != nil {
        return err
    }

    return nil
}

// UpdateUser updates an existing user.
func (s *UserService) UpdateUser(user *User) error {
    // Validate the user input.
    if user.ID == 0 {
        return fmt.Errorf("ID is required")
    }
    if user.Name == "" {
        return fmt.Errorf("Name is required")
    }
    if user.Email == "" {
        return fmt.Errorf("Email is required")
    }

    // Update the user in the repository.
    err := s.repository.UpdateUser(user)
    if err != nil {
        return err
    }

    return nil
}

// DeleteUser deletes an existing user.
func (s *UserService) DeleteUser(id int) error {
    // Check if the user exists.
    user, err := s.repository.GetUser(id)
    if err != nil {
        return err
    }
    if user == nil {
        return fmt.Errorf("User with ID %d does not exist", id)
    }

    // Delete the user from the repository.
    err = s.repository.DeleteUser(id)
    if err != nil {
        return err
    }

    return nil
}

func main() {
    // Create a user repository.
    repository := NewInMemoryUserRepository()

    // Create a user service.
    service := NewUserService(repository)

    // Create a user.
    user := &User{
        Name: "John Doe",
        Email: "johndoe@example.com",
    }
    err := service.CreateUser(user)
    if err != nil {
        log.Fatal(err)
    }

    // Update the user.
    user.Name = "Jane Doe"
    err = service.UpdateUser(user)
    if err != nil {
        log.Fatal(err)
    }

    // Delete the user.
    err = service.DeleteUser(user.ID)
    if err != nil {
        log.Fatal(err)
    }

    fmt.Println("Done")
}

```


### Method 2

```
// main.go
package main

import (
    "fmt"
    "log"
    "net/http"
)

// Customer is an entity that represents a customer.
type Customer struct {
    ID     int
    Name    string
    Address string
}

// Repository is an interface that provides access to the customer data.
type Repository interface {
    GetCustomer(id int) (*Customer, error)
    SaveCustomer(*Customer) error
}

// Service is a unit of work that performs a specific business operation.
type Service struct {
    repository Repository
}

// NewService creates a new Service instance.
func NewService(repository Repository) *Service {
    return &Service{repository: repository}
}

// GetCustomer gets a customer by ID.
func (s *Service) GetCustomer(id int) (*Customer, error) {
    return s.repository.GetCustomer(id)
}

// SaveCustomer saves a customer.
func (s *Service) SaveCustomer(customer *Customer) error {
    return s.repository.SaveCustomer(customer)
}

// Controller is a Golang function that handles HTTP requests.
func Controller(w http.ResponseWriter, r *http.Request) {
    // Get the customer ID from the request.
    id := r.URL.Query().Get("id")

    // Get the customer from the repository.
    customer, err := service.GetCustomer(id)
    if err != nil {
        log.Fatal(err)
    }

    // Write the customer to the response.
    fmt.Fprintf(w, "%+v", customer)
}

func main() {
    // Create a new repository.
    repository := NewRepository()

    // Create a new service.
    service := NewService(repository)

    // Create a new http.Handler.
    handler := http.HandlerFunc(Controller)

    // Serve the handler on port 8080.
    http.ListenAndServe(":8080", handler)
}
```


### Method 3

```
package main

import (
    "fmt"
    "log"
    "net/http"
)

// User is an aggregate root.
type User struct {
    ID       int
    Name     string
    Email    string
    Password string
}

// UserRepository is an interface that provides access to the user data.
type UserRepository interface {
    GetUser(id int) (*User, error)
    CreateUser(user *User) error
    UpdateUser(user *User) error
    DeleteUser(id int) error
}

// UserService is a service that performs operations on users.
type UserService struct {
    repo UserRepository
}

// CreateUser creates a new user.
func (s *UserService) CreateUser(user *User) error {
    err := s.repo.CreateUser(user)
    if err != nil {
        return err
    }
    return nil
}

// GetUser gets a user by ID.
func (s *UserService) GetUser(id int) (*User, error) {
    user, err := s.repo.GetUser(id)
    if err != nil {
        return nil, err
    }
    return user, nil
}

// UpdateUser updates a user.
func (s *UserService) UpdateUser(user *User) error {
    err := s.repo.UpdateUser(user)
    if err != nil {
        return err
    }
    return nil
}

// DeleteUser deletes a user.
func (s *UserService) DeleteUser(id int) error {
    err := s.repo.DeleteUser(id)
    if err != nil {
        return err
    }
    return nil
}

func main() {
    // Create a user repository.
    repo := &InMemoryUserRepository{}

    // Create a user service.
    service := &UserService{
        repo: repo,
    }

    // Create a router.
    router := http.NewRouter()

    // Register the user service routes.
    router.HandleFunc("/users", service.CreateUser).Methods("POST")
    router.HandleFunc("/users/{id}", service.GetUser).Methods("GET")
    router.HandleFunc("/users/{id}", service.UpdateUser).Methods("PUT")
    router.HandleFunc("/users/{id}", service.DeleteUser).Methods("DELETE")

    // Listen on port 8080.
    http.ListenAndServe(":8080", router)
}

// InMemoryUserRepository is a simple in-memory implementation of the UserRepository interface.
type InMemoryUserRepository struct {
    users map[int]*User
}

// GetUser gets a user by ID.
func (r *InMemoryUserRepository) GetUser(id int) (*User, error) {
    user, ok := r.users[id]
    if !ok {
        return nil, fmt.Errorf("user with id %d not found", id)
    }
    return user, nil
}

// CreateUser creates a new user.
func (r *InMemoryUserRepository) CreateUser(user *User) error {
    r.users[user.ID] = user
    return nil
}

// UpdateUser updates a user.
func (r *InMemoryUserRepository) UpdateUser(user *User) error {
    _, ok := r.users[user.ID]
    if !ok {
        return fmt.Errorf("user with id %d not found", user.ID)
    }
    r.users[user.ID] = user
    return nil
}

// DeleteUser deletes a user.
func (r *InMemoryUserRepository) DeleteUser(id int) error {
    delete(r.users, id)
    return nil
}
```


Folder structure

```
- cmd
  - your_application_name
    - main.go
- internal
  - app
    - handlers
      - handler.go
      - handler_test.go
    - services
      - service.go
      - service_test.go
  - domain
    - model.go
    - repository.go
    - repository_test.go
    - service.go
    - service_test.go
  - infrastructure
    - persistence
      - repository.go
      - repository_test.go
- pkg
  - your_package_name
    - utility.go
    - utility_test.go
- configs
  - config.go
- scripts
  - deploy.sh
- test
  - integration
    - integration_test.go
- Dockerfile
- go.mod
- README.md
```


