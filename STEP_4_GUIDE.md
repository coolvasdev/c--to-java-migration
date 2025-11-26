# Migration Step 4: Data Layer Migration

## Overview and Objectives

### Step Objective:
The goal of this step is to migrate the data layer from the source language (C#) to the target language (Java). This involves porting the database schema, converting ORM models/queries, and migrating the data access layer to ensure seamless interaction with the database in the target language.

### Key Deliverables:
1. Java-compatible database schema.
2. Converted ORM models and queries.
3. Functional Java-based data access layer integrated with the repository.

---

## Prerequisites and Dependencies

### Prerequisites:
1. **Database Access**: Ensure access to the database for schema export and testing.
2. **Source Code Repository**: Clone the repository containing the C# data layer for reference.
3. **Framework Selection**: Choose a Java ORM framework (e.g., Hibernate, JPA, or Spring Data JPA).
4. **Database Driver**: Install necessary JDBC drivers for the database your application uses (e.g., MySQL, PostgreSQL, SQL Server).
5. **Development Environment**: Set up a Java IDE (e.g., IntelliJ IDEA) and configure Maven/Gradle for dependency management.

### Dependencies:
1. Java runtime environment (JDK 8+ recommended).
2. Database connection configuration (URL, username, password).
3. Selected Java ORM framework configuration.

---

## Detailed Implementation Instructions

### Task 1: Port Database Schema

#### Steps:
1. **Export the Schema**:
   - Use the database management tool (e.g., SQL Server Management Studio for SQL Server) to export the schema as SQL scripts.
   - Example:
     ```sql
     -- Export schema in SQL Server
     SELECT * INTO NewTable
     FROM OldTable;
     ```

2. **Convert Schema to Java-Compatible Format**:
   - Ensure compatibility with Java-based frameworks by checking for:
     - Data type equivalence (e.g., `varchar` to `String`, `int` to `Integer`).
     - Constraints (primary keys, foreign keys, indexes).

3. **Create Database Tables in Java**:
   - Use Java ORM annotations for schema mapping:
     ```java
     @Entity
     @Table(name = "users")
     public class User {
         @Id
         @GeneratedValue(strategy = GenerationType.IDENTITY)
         private Long id;

         @Column(name = "username", nullable = false, length = 50)
         private String username;

         @Column(name = "email", nullable = false, unique = true)
         private String email;

         // Getters and setters
     }
     ```

4. **Verify Schema Integrity**:
   - Compare the generated schema in Java with the original schema exported from the database.

---

### Task 2: Convert ORM/Queries

#### Steps:
1. **Map C# ORM Models to Java ORM**:
   - Identify C# Entity Framework models and their relationships.
   - Translate relationships into Java annotations:
     ```java
     @OneToMany(mappedBy = "user", cascade = CascadeType.ALL)
     private List<Order> orders;
     ```

2. **Convert Queries**:
   - Migrate LINQ queries in C# to Java Query Language (JPQL) or Criteria API.
   - Example C# LINQ:
     ```csharp
     var users = dbContext.Users.Where(u => u.IsActive).ToList();
     ```
     Converted to JPQL:
     ```java
     Query query = entityManager.createQuery("SELECT u FROM User u WHERE u.isActive = true");
     List<User> users = query.getResultList();
     ```

3. **Handle Stored Procedures**:
   - Use Java for stored procedure calls:
     ```java
     StoredProcedureQuery query = entityManager.createStoredProcedureQuery("getActiveUsers");
     query.registerStoredProcedureParameter(1, Long.class, ParameterMode.IN);
     query.setParameter(1, userId);
     List<Object[]> results = query.getResultList();
     ```

---

### Task 3: Migrate Data Access Layer

#### Steps:
1. **Implement Repository Pattern**:
   - Define repository interfaces using Spring Data JPA:
     ```java
     public interface UserRepository extends JpaRepository<User, Long> {
         List<User> findByIsActive(boolean isActive);
     }
     ```

2. **Replace C# Services with Java Services**:
   - Convert C# service methods to Java service methods:
     ```java
     @Service
     public class UserService {
         @Autowired
         private UserRepository userRepository;

         public List<User> getActiveUsers() {
             return userRepository.findByIsActive(true);
         }
     }
     ```

3. **Test Database Connections**:
   - Verify connectivity using integration tests:
     ```java
     @SpringBootTest
     public class UserRepositoryTest {
         @Autowired
         private UserRepository userRepository;

         @Test
         public void testFindByIsActive() {
             List<User> users = userRepository.findByIsActive(true);
             assertFalse(users.isEmpty());
         }
     }
     ```

---

## Code Examples and Snippets

### C# to Java Schema Conversion Example:
#### C# Entity Framework Model:
```csharp
public class User {
    public int Id { get; set; }
    public string Username { get; set; }
    public string Email { get; set; }
    public ICollection<Order> Orders { get; set; }
}
```

#### Equivalent Java ORM Model:
```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "username", nullable = false)
    private String username;

    @Column(name = "email", nullable = false, unique = true)
    private String email;

    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL)
    private List<Order> orders;

    // Getters and setters
}
```

---

## Common Pitfalls and How to Avoid Them

### Pitfall 1: Incompatible Data Types
- **Solution**: Compare database data types and map them properly using Java annotations.

### Pitfall 2: Missing Relationships
- **Solution**: Carefully replicate C# relationships in Java using annotations like `@OneToMany`, `@ManyToOne`.

### Pitfall 3: Complex LINQ Queries
- **Solution**: Break down LINQ queries into smaller JPQL or Criteria API queries for easier conversion.

---

## Testing Checklist

1. Verify schema compatibility.
2. Test CRUD operations in Java.
3. Validate queries functionality.
4. Ensure stored procedures work as expected.
5. Perform integration testing for data access layer.

---

## Validation Criteria

- All database tables and relationships are properly ported.
- Queries return correct and complete results.
- Services interact with the database without errors.

---

## Troubleshooting Guide

### Issue: "Schema mismatch errors"
- **Solution**: Check for missing or incorrect annotations in Java models.

### Issue: "Database connection issues"
- **Solution**: Verify JDBC driver installation and connection properties.

---

## Resources and References

- [Spring Data JPA Documentation](https://spring.io/projects/spring-data-jpa)
- [Hibernate ORM Documentation](https://hibernate.org/documentation/)
- [Java Persistence API (JPA) Guide](https://jakarta.ee/specifications/persistence/)

---

## Next Steps

Proceed to **Step 5: Business Logic Migration**, focusing on migrating service-layer logic from C# to Java.

Estimated Time: **8-12 hours**, depending on the complexity of the schema and queries.