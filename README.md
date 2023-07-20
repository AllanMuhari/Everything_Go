1. Basics of Go Language:
```go
package main

import "fmt"

func main() {
    // Variables
    var name string = "John"
    age := 30

    // Functions
    fmt.Println("Hello, " + name)
    fmt.Println("Age:", age)
}
```

2. HTTP and Web Frameworks:
```go
package main

import (
    "net/http"

    "github.com/gin-gonic/gin"
)

func main() {
    router := gin.Default()

    router.GET("/hello", func(c *gin.Context) {
        c.String(http.StatusOK, "Hello, World!")
    })

    router.Run(":8080")
}
```

3. Database Interaction (using MySQL with `go-sql-driver`):
```go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

func main() {
    db, err := sql.Open("mysql", "user:password@tcp(localhost:3306)/database")
    if err != nil {
        fmt.Println("Error connecting to database:", err)
        return
    }
    defer db.Close()

    // Perform database operations here
}
```

4. RESTful API Design:
```go
package main

import (
    "net/http"

    "github.com/gin-gonic/gin"
)

func main() {
    router := gin.Default()

    router.GET("/api/users", func(c *gin.Context) {
        // Fetch users from the database and return as JSON
        users := []User{
            {ID: 1, Name: "John"},
            {ID: 2, Name: "Alice"},
        }
        c.JSON(http.StatusOK, users)
    })

    router.POST("/api/users", func(c *gin.Context) {
        // Create a new user from JSON payload
        var newUser User
        if err := c.ShouldBindJSON(&newUser); err != nil {
            c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
            return
        }
        // Save the new user to the database
        // ...

        c.JSON(http.StatusCreated, newUser)
    })

    router.Run(":8080")
}

type User struct {
    ID   int    `json:"id"`
    Name string `json:"name"`
}
```

5. Authentication and Authorization (using JWT):
```go
// Example JWT authentication middleware
func AuthMiddleware() gin.HandlerFunc {
    return func(c *gin.Context) {
        tokenString := c.GetHeader("Authorization")
        if tokenString == "" {
            c.JSON(http.StatusUnauthorized, gin.H{"error": "Missing token"})
            c.Abort()
            return
        }
        // Validate and extract claims from the token
        // ...
        c.Next()
    }
}

func main() {
    router := gin.Default()

    api := router.Group("/api")
    api.Use(AuthMiddleware())

    api.GET("/private", func(c *gin.Context) {
        c.String(http.StatusOK, "This is a private endpoint")
    })

    router.Run(":8080")
}
```

6. Testing (using the built-in `testing` package):
```go
package main

import "testing"

func Sum(a, b int) int {
    return a + b
}

func TestSum(t *testing.T) {
    result := Sum(2, 3)
    if result != 5 {
        t.Errorf("Expected 5, but got %d", result)
    }
}
```

7. Concurrency and Goroutines:
```go
package main

import (
    "fmt"
    "time"
)

func main() {
    go printNumbers()
    go printLetters()

    // Wait for goroutines to finish before exiting
    time.Sleep(2 * time.Second)
}

func printNumbers() {
    for i := 1; i <= 5; i++ {
        fmt.Printf("%d ", i)
    }
}

func printLetters() {
    for i := 'a'; i <= 'e'; i++ {
        fmt.Printf("%c ", i)
    }
}
```

8. Middleware (using Gin framework):
```go
package main

import (
    "net/http"

    "github.com/gin-gonic/gin"
)

func AuthMiddleware() gin.HandlerFunc {
    return func(c *gin.Context) {
        // Perform authentication logic here
        // ...

        if isAuthenticated {
            c.Next()
        } else {
            c.AbortWithStatus(http.StatusUnauthorized)
        }
    }
}

func main() {
    router := gin.Default()

    // Apply AuthMiddleware to all routes under /api
    api := router.Group("/api")
    api.Use(AuthMiddleware())

    api.GET("/private", func(c *gin.Context) {
        c.String(http.StatusOK, "This is a private endpoint")
    })

    router.Run(":8080")
}
```

9. Deployment and Scalability:
For deployment, you can build a Docker image of your Go application and run it in a container. For scalability, consider using load balancing and deploying your application on cloud platforms like AWS, Google Cloud, or Azure.

10. Error Handling:
Error handling in Go is typically done using the `error` type. Example:
```go
func Divide(a, b int) (int, error) {
    if b == 0 {
        return 0, fmt.Errorf("division by zero")
    }
    return a / b, nil


}

func main() {
    result, err := Divide(10, 0)
    if err != nil {
        fmt.Println("Error:", err)
    } else {
        fmt.Println("Result:", result)
    }
}
```

11. Performance Optimization:
Go is known for its performance, but you can optimize further by profiling and benchmarking your code to identify bottlenecks.

In Go, you can use the built-in `testing` and `runtime` packages to profile and benchmark your code for performance optimization. Profiling helps you identify hotspots and bottlenecks in your code, while benchmarking allows you to measure the execution time of specific functions or code blocks.

Here's an example of how to profile and benchmark a simple function in Go:

1. Profiling:

```go
package main

import (
	"fmt"
	"os"
	"runtime/pprof"
)

func performTask() {
	// Simulate some time-consuming task
	for i := 0; i < 10000000; i++ {
		_ = i * i
	}
}

func main() {
	// Create a CPU profile file
	f, err := os.Create("cpu_profile.pprof")
	if err != nil {
		fmt.Println("Error creating CPU profile:", err)
		return
	}
	defer f.Close()

	// Start profiling
	err = pprof.StartCPUProfile(f)
	if err != nil {
		fmt.Println("Error starting CPU profile:", err)
		return
	}
	defer pprof.StopCPUProfile()

	// Perform the task
	performTask()

	fmt.Println("Task completed.")
}
```

In this example, we create a CPU profile file `cpu_profile.pprof`, start profiling, perform the task (which is a simple loop to simulate a time-consuming operation), and then stop profiling. The generated CPU profile file can be analyzed using tools like `go tool pprof`.

2. Benchmarking:

```go
package main

import (
	"testing"
)

func performTask() {
	// Simulate some time-consuming task
	for i := 0; i < 10000000; i++ {
		_ = i * i
	}
}

func BenchmarkPerformTask(b *testing.B) {
	for i := 0; i < b.N; i++ {
		performTask()
	}
}
```

In this example, we write a benchmark function `BenchmarkPerformTask` that calls the `performTask` function. The `testing` package automatically runs the benchmark multiple times and measures the execution time. You can run benchmarks using the `go test -bench` command.

Remember that performance optimization should be based on actual performance bottlenecks and not just for the sake of optimization. Profiling and benchmarking help you identify areas that need improvement and can lead to more efficient and performant code.

I hope these examples help you get started with backend development using Golang. Remember to explore the official Go documentation and other resources to deepen your understanding and become a proficient Go backend developer. Happy coding!