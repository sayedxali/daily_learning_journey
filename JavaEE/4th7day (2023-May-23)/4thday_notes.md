# <span style='color: olive'>Day 29/90</span>

Date : 10-May-2023



## <span style='color: grey'>JPQL - Java Persistence Query Language (continue)</span>

### <span style='color: navy'>Where Clause - In Operator</span>

📝 Note: "Where Clause - IN Operator"

The `WHERE` clause is often used to filter data in a SQL query. One useful operator that can be used in the `WHERE` clause is the `IN` operator. This operator allows you to specify a list of values to compare against a single column in your table.

For example, let's say we have a table called `fruits` with columns `id` and `name`. We want to select all the fruits whose names are either "banana", "apple", or "orange". We can use the `IN` operator like this:

```
SELECT * FROM fruits WHERE name IN ('banana', 'apple', 'orange');
```

This will return all the rows from the `fruits` table where the `name` column is either "banana", "apple", or "orange".

We can also use the `NOT IN` operator to exclude certain values. For example, let's say we want to select all fruits except for "banana" and "orange":

```java
SELECT * FROM fruits WHERE name NOT IN ('banana', 'orange');
```

In addition to using the `IN` operator directly in the `WHERE` clause, we can also use it in subqueries. For example, let's say we have another table called `orders`, which has a column called `fruit_id` that corresponds to the `id` column in the `fruits` table. We want to find all the orders that correspond to bananas, apples, or oranges. We can use a subquery like this:

```java
SELECT * FROM orders WHERE fruit_id IN (SELECT id FROM fruits WHERE name IN ('banana', 'apple', 'orange'));
```

Finally, if you're working with Java and using the Entity Manager to interact with your database, you can create a method to return the result list using the `IN` operator. Here's an example:

```java
public List<Fruit> getFruits(List<String> names) {
   Query query = entityManager.createQuery("SELECT f FROM Fruit f WHERE f.name IN :names");
   query.setParameter("names", names);
   return query.getResultList();
}
```

This method takes a list of fruit names as input and returns a list of `Fruit` objects from the database where the name is in the input list.

### <span style='color: navy'>Where Clause - IS EMPTY/IS NOT EMPTY Operator</span>

📝 Note: "Where Clause - IS EMPTY Operator"

The `WHERE` clause is an essential part of SQL queries used to filter data. Another useful operator in the `WHERE` clause is the `IS EMPTY` operator, which allows you to determine whether a specific field or relationship in your table is empty or not.

👨‍💼👩‍💼 Let's take an example of an `Employee` entity that has a self-referential Many-to-One relationship with itself, where one employee is the head of a team. In this case, the subordinate has a One-to-Many relationship with the `Employee` object. If the subordinates are empty, it means that the employee is the leader of the team.

🔍 To write a query that retrieves all leaders who don't have any subordinates, we can use the `IS EMPTY` operator like this:

```sql
SELECT e FROM Employee e WHERE e.subordinates IS EMPTY
```

👀 This query will return all employees who are the leaders of their respective teams and do not have any subordinates.

💻 In Java, we can create a method using the Entity Manager to retrieve this information. Here's an example of such a method:

```java
public List<Employee> getLeadersWithoutSubordinates() {
   Query query = entityManager.createQuery("SELECT e FROM Employee e WHERE e.subordinates IS EMPTY");
   return query.getResultList();
}
```

📈 This method returns a list of all employees who are leaders and do not have any subordinates. You can use this list to perform further operations on these entities, such as updating or deleting them.

### <span style='color: navy'>Where Clause - MEMBER OF Operator</span>

📝 Note: Where Clause with the "member of" Operator 🕵️‍♀️

The "member of" operator in SQL is a powerful tool that allows you to filter data based on whether a specific value is contained within an array or collection. In the following example, we're going to find all employees who are working on a particular project.

👩‍💻 To use this operator in your own code, you can write a WHERE clause like this:

```sql
SELECT * FROM employees
WHERE project_id member of projects;
```

🔎 This will return all employees that have the specified `project_id` included in their list of `projects`.

🏋️‍♂️ Here's an example method that utilizes the "member of" operator with the entity manager:

```java
public List<Employee> getEmployeesByProject(Project project) {
    return entityManager.createQuery(
            "SELECT e FROM Employee e WHERE :project member of e.projects",
            Employee.class)
            .setParameter("project", project)
            .getResultList();
}
```

👀 In this method, we're selecting all instances of the `Employee` class where the specified `project` is included in their `projects` list. We then set our query parameter using `.setParameter()` and return the result list.

🤓 With the "member of" operator, you can easily filter data based on specific values within collections.

### <span style='color: navy'>Where Clause - ANY, ALL Operator</span>

📝 Note: The "where clause" is a clause in SQL used to filter data based on specific conditions. It's used to select data from a table that meets certain criteria.

The "any", "all", and "some" operators are used in conjunction with the "where" clause to compare a single value with a set of values returned from a subquery.

- The "any" operator returns true if any one of the values returned by the subquery satisfies the condition.
- The "all" operator returns true if all of the values returned by the subquery satisfy the condition.
- The "some" operator is synonymous with the "any" operator.

💻 Here's an example based on the given code:

Suppose we have an Employee table with columns id, name, basicSalary, and subordinates. The subordinates column contains information about who reports to whom, as shown below:

| id   | name  | basicSalary | subordinates |
| ---- | ----- | ----------- | ------------ |
| 1    | John  | 2500        | Jane, Mark   |
| 2    | Jane  | 2500        | NULL         |
| 3    | Mark  | 3500        | NULL         |
| 4    | Alice | 4000        | Bob, Cindy   |
| 5    | Bob   | 4500        | NULL         |
| 6    | Cindy | 5000        | NULL         |

Now suppose we want to retrieve all lower-paid managers using the provided code.

```java
public List<Employee> getAllLowerPaidManagers() {
    EntityManager entityManager = getEntityManager();
    
    String query = "select e from Employee e where e.subordinates is not empty and e.basicSalary < all (select s.basicSalary from e.subordinates s)";
    
    TypedQuery<Employee> typedQuery = entityManager.createQuery(query, Employee.class);
    
    List<Employee> resultList = typedQuery.getResultList();
    
    return resultList;
}
```

This code uses the "where" clause to filter data from the Employee table. Specifically, it selects employees who have subordinates and whose basic salary is less than all of their subordinates' basic salaries.

So in our example, only John satisfies these conditions, as his basic salary of 2500 is less than all of his subordinates' basic salaries of 2500 and 3500. Therefore, the method would return a list containing only John.

### <span style='color: navy'>Order By Clause</span>

The `ORDER BY` clause is used in SQL to sort the query results in a particular order. It is often used with the `SELECT` statement and can be used to sort the results by one or more columns.

💡 For example, let's say we have a table of employee salaries and we want to give a bonus to our top performing employees. We can use the `ORDER BY` clause to sort the employees by their bonus amount.

Here's an example query:

```sql
SELECT employee_name, basic_salary * 1.5 AS bonus
FROM employee_table
ORDER BY bonus DESC;
```

This query will multiply the basic salary of each employee by 1.5 to get their bonus amount and then sort the results in descending order based on the bonus value.

👨‍💻 To use this in code, we can create a method that returns a sorted list of employees based on their bonus. Here's an example using Entity Manager in Java:

```java
public List<Employee> getEmployeesByBonus(EntityManager em) {
    String queryString = "SELECT e FROM Employee e ORDER BY e.basicSalary * 1.5 DESC";
    TypedQuery<Employee> query = em.createQuery(queryString, Employee.class);
    return query.getResultList();
}
```

> or :
>
> ```java
> "SELECT e, e.basicSalary * 1.5 as bonus FROM Employee e ORDER BY bonus DESC"
> ```
>
> 

This method will return a list of employees sorted by their bonus amount (which is calculated as 1.5 times their basic salary).

### <span style='color: navy'>JPQL - Aggregate Functions</span>

(ﾉ◕ヮ◕)ﾉ*:･ﾟ✧ In JPQL, we can use aggregate functions to perform operations on groups of data. These functions include:

- COUNT 🧮 - counts the number of rows in a group
- SUM 📈 - calculates the sum of values in a group
- AVG 📊 - calculates the average of values in a group
- MAX 👑 - returns the maximum value in a group
- MIN 👑 - returns the minimum value in a group

Aggregate functions are useful when we want to get a better understanding of our data as a whole, rather than just looking at individual rows.

COUNT 🧮 - counts the number of rows in a group

```sql
SELECT COUNT(e) FROM Employee e WHERE e.salary > 50000
```

This query will return the number of employees who earn a salary greater than $50,000.

SUM 📈 - calculates the sum of values in a group

```sql
SELECT SUM(o.totalAmount) FROM Order o WHERE o.orderDate BETWEEN :startDate AND :endDate
```

This query will return the total amount of all orders placed between the specified start and end dates.

AVG 📊 - calculates the average of values in a group

```sql
SELECT AVG(p.price) FROM Product p WHERE p.category = :category
```

This query will return the average price of products in the specified category.

MAX 👑 - returns the maximum value in a group

```sql
SELECT MAX(c.creditLimit) FROM Customer c
```

This query will return the highest credit limit among all customers.

MIN 👑 - returns the minimum value in a group

```sql
SELECT MIN(e.hireDate) FROM Employee e WHERE e.department = :department
```

This query will return the earliest hire date for employees in the specified department.

## <span style='color: #8b0000'>Done</span>

- [x] Day 29/90 ===> [JavaEE: Day 29/90 - JPQL (Java Persistence Query Language)](https://www.linkedin.com/pulse/javaee-day-2990-jpql-java-persistence-query-language-seyed-ali)



# <span style='color: olive'>Day 30/90</span>

Date : 11-May-2023



## <span style='color: grey'>JPA - Java Persistence API (continue)</span>

### <span style='color: navy'>Validation - Bean Validation</span>

📝 Note on JPA Validation using Bean Validation 🌟

JPA validation is a powerful tool that helps ensure the data stored in your application's database is valid. It uses Bean Validation - a specification for validating Java beans - to check if certain fields meet specific criteria.

👉 Here are some commonly used annotations in JPA validation:

- `@NotEmpty` : Ensures that a field is not null and has a non-zero length. 

  Example: 

  ```java
  @NotEmpty String username;
  ```

- `@NotNull` : Ensures that a field is not null. 

  Example: 

  ```java
  @NotNull Integer age;
  ```

- `@Size` : Ensures that a string or collection has a specific number of elements. 

  Example: 

  ```java
  @Size(min = 2, max = 10) String title;
  ```

- `@DecimalMin` and `@DecimalMax` : Ensure that a numeric value is within a specific range. 

  Example: 

  ```java
  @DecimalMin("0.00") @DecimalMax("999.99") BigDecimal price;
  ```

- `@Past` : Ensures that the provided date is in the past.

  Example:

  ```java
  @Past LocalDate dateOfBirth;
  ```

- `@Pattern` :  The `@Pattern` annotation is a feature in Java that allows developers to validate input fields based on a specified regular expression. It ensures that input values match the pattern defined by the regular expression. This annotation is commonly used in Java classes that handle input validation and data binding.

  Here's an example of how to use the `@Pattern` annotation in Java:

  ```java
  public class User {
      @Pattern(regexp = "^[a-zA-Z0-9!@#$%^&*()_+\\-=\\[\\]{};':\"\\\\|,.<>\\/?]{8,}$", message = "Password should contain at least 8 characters with a mix of uppercase and lowercase letters, digits, and symbols")
      private String password;
      
      @Pattern(regexp = "^([A-Z]*)\\d{3}[A-Z]*_(\\d{3})_[A-Z]*$", message = "Department name should be in this form: FIN001MAIN - departmentNameInCapital_3digitnumber_branch")
      private String departmentName;
      
      // getters and setters
  }
  ```

  In the above example, the first "@Pattern" annotation is used to validate the password field. The regular expression used in the annotation ensures that the password contains at least 8 characters with a mix of uppercase and lowercase letters, digits, and symbols. The message attribute provides a custom error message if the validation fails.

  The second "@Pattern" annotation is used to validate the departmentName field. The regular expression used in the annotation ensures that the department name follows the format "FIN001MAIN - departmentNameInCapital_3digitnumber_branch". In this format, the departmentNameInCapital should only consist of uppercase letters and the branch should only consist of numbers. Again, the message attribute provides a custom error message if the validation fails.

  code explanation :

  🔑For the `password` field, the regular expression used is:

  ```java
  ^[a-zA-Z0-9!@#$%^&*()_+\\-=\\[\\]{};':\"\\\\|,.<>\\/?]{8,}$
  ```

  This regular expression ensures that the password contains at least 8 characters with a mix of uppercase and lowercase letters, digits, and symbols. Let's break down this regular expression:

  - `^` - matches the beginning of the input string
  - `[a-zA-Z0-9!@#$%^&*()_+\\-=\\[\\]{};':\"\\\\|,.<>\\/?]` - matches any character from the set of characters specified inside the square brackets. This includes uppercase and lowercase letters, digits, and commonly used symbols.
  - `{8,}` - ensures that the previous character set occurs at least 8 times
  - `$` - matches the end of the input string

  

  🏬For the `departmentName` field, the regular expression used is:

  ```java
  ^([A-Z]*)\\d{3}[A-Z]*_(\\d{3})_[A-Z]*$
  ```

  This regular expression ensures that the department name follows the format "FIN001MAIN - departmentNameInCapital_3digitnumber_branch". In this format, the departmentNameInCapital should only consist of uppercase letters and the branch should only consist of numbers. Let's break down this regular expression:

  - `^` - matches the beginning of the input string
  - `([A-Z]*)` - matches any number of uppercase letters at the start of the string (the department code)
  - `\\d{3}` - matches exactly 3 digits (the three-digit number after the department code)
  - `[A-Z]*` - matches any number of uppercase letters (the department name in capital)
  - `_` - matches an underscore character
  - `(\\d{3})` - matches exactly 3 digits (the branch number)
  - `_` - matches another underscore character
  - `[A-Z]*$` - matches any number of uppercase letters at the end of the string (which should be nothing in this case)

💡 These annotations are just a few examples of how JPA validation can help make your code more robust!

### <span style='color: navy'>Validation - Creating Custom Validation Annotation</span>

⚆_⚆ Prerequsite - Java RegExp. It is important to have basic knowledge of it.

📝 JPA Custom Validation Annotations 👨‍💻

When building Java applications using the JPA (Java Persistence API), we often need to validate user input before persisting data in a database. While JPA provides several built-in validation annotations, sometimes we may require custom validation rules that are not covered by these built-in annotations. In such cases, we can create our own custom validation annotations.

✅ Example 1: Creating Annotation for Validating Email Let's say we want to ensure that the email entered by the user is in the correct format. We can achieve this by creating a custom annotation for email validation as follows:

```java
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = EmailValidator.class)
public @interface EmailValidation {

    String message() default "Invalid email format";

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};
}
```

In this example, we have created an annotation named `@EmailValidation`. The annotation specifies that it should be applied to fields and that it should be validated at runtime using the `EmailValidator` class.

`EmailValidator` Implementation:

```java
public class EmailValidator implements ConstraintValidator<EmailValidation, String> {

    @Override
    public void initialize(EmailValidation annotation) {
    }

    @Override
    public boolean isValid(String email, ConstraintValidatorContext context) {
        if (email == null) {
            return false;
        }
        // Regular expression to validate email format
        String emailPattern = "^[A-Za-z0-9+_.-]+@[A-Za-z0-9.-]+$";
        return email.matches(emailPattern);
    }
}
```

This implementation of `EmailValidator` checks if the email address provided in the annotated field is valid using a regular expression pattern. If it is not valid, it returns `false`.

🧑🏻‍💻EmailValidator: `^[A-Za-z0-9+_.-]+@[A-Za-z0-9.-]+$`

- `^` - The caret symbol at the beginning of the regex indicates that it should match the starting position of the string.
- `[A-Za-z0-9+_.-]` - This character class matches any letter (upper or lowercase), digit, or one of the special characters `+, _, .,` or `-`.
- `+` - The plus sign means to match one or more instances of the preceding character or character class.
- `@` - This matches the literal '@' symbol that separates the local and domain parts of an email address.
- `[A-Za-z0-9.-]` - Another character class matching any letter (upper or lowercase), digit, or the special characters `.` or `-`.
- `$` - The dollar sign at the end of the regex indicates that it should match the ending position of the string.



✅ Example 2: Creating Annotation for Validating Password Now let's say we want to ensure that the password entered by the user meets certain criteria, such as containing at least one uppercase letter, one lowercase letter, one digit, and one special character. We can create a custom annotation for password validation as follows:

```java
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = PasswordValidator.class)
public @interface PasswordValidation {

    String message() default "Invalid password format";

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};
}
```

In this example, we have created an annotation named `@PasswordValidation`. The annotation specifies that it should be applied to fields and that it should be validated at runtime using the `PasswordValidator` class.

`PasswordValidator` Implementation:

```java
public class PasswordValidator implements ConstraintValidator<PasswordValidation, String> {

    @Override
    public void initialize(PasswordValidation annotation) {
    }

    @Override
    public boolean isValid(String password, ConstraintValidatorContext context) {
        if (password == null) {
            return false;
        }
        // Regular expression to validate password format
        String passwordPattern = "^(?=.*[a-z])(?=.*[A-Z])(?=.*\\d)(?=.*[@#$%^&+=!]).{8,}$";
        return password.matches(passwordPattern);
    }
}
```

This implementation of `PasswordValidator` checks if the password provided in the annotated field meets the required criteria using a regular expression pattern. If it does not meet the criteria, it returns `false`.

🧑🏻‍💻PasswordValidator: `^(?=.*[a-z])(?=.*[A-Z])(?=.*\\d)(?=.*[@#$%^&+=!]).{8,}$`

- `^` - The caret symbol at the beginning of the regex indicates that it should match the starting position of the string.
- `(?=.*[a-z])` - This is a positive lookahead assertion, which checks whether the string contains at least one lowercase letter.
- `(?=.*[A-Z])` - Another positive lookahead assertion, this time checking for at least one uppercase letter.
- `(?=.*\d)` - A third positive lookahead assertion, checking for at least one digit.
- `(?=.*[@#$%^&+=!])` - The final positive lookahead assertion, checking for at least one of the special characters @, #, $, %, ^, &, +, =, or !.
- `.{8,}` - This matches any character (except a newline) for a minimum length of eight characters.
- `$` - The dollar sign at the end of the regex indicates that it should match the ending position of the string.

> An example : `P@ssw0rd!`



✅ Example 3: Creating Annotation for Validating Department Name Let's consider the example of validating departmentName in the format "FIN001MAIN - departmentNameInCapital_3digitnumber_branch". We can create a custom annotation for this validation as follows:

```java
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = DepartmentNameValidator.class)
public @interface DepartmentNameValidation {

    String message() default "Invalid department name format";

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};
}
```

In this example, we have created an annotation named `@DepartmentNameValidation`. The annotation specifies that it should be applied to fields and that it should be validated at runtime using the `DepartmentNameValidator` class.

DepartmentNameValidator Implementation:

```java
public class DepartmentNameValidator implements ConstraintValidator<DepartmentNameValidation, String> {

    @Override
    public void initialize(DepartmentNameValidation annotation) {
    }

    @Override
    public boolean isValid(String departmentName, ConstraintValidatorContext context) {
        if (departmentName == null) {
            return false;
        }
        // Regular expression to validate department name format
        String departmentNamePattern = "^[A-Z]*\\d{3}[A-Z]*_(\\d{3})_[A-Z]*$";
        return departmentName.matches(departmentNamePattern);
    }
}
```

This implementation of `DepartmentNameValidator` checks if the department name provided in the annotated field is in the correct format using a regular expression pattern. If it is not in the correct format, it returns `false`.

🧑🏻‍💻DepartmentNameValidator: `"^[A-Z]*\\d{3}[A-Z]*_(\\d{3})_[A-Z]*$`

- `^` - This symbol indicates the start of the string.
- `[A-Z]*` - This character class matches zero or more uppercase letters.
- `\d{3}` - This matches three digits.
- `[A-Z]*` - This character class matches zero or more uppercase letters.
- `_` - This matches an underscore character.
- `(\d{3})` - This is a capture group that matches three digits. The parentheses indicate that this portion of the pattern should be captured and remembered for later use.
- `_` - This matches another underscore character.
- `[A-Z]*` - This character class matches zero or more uppercase letters.
- `$` - This symbol indicates the end of the string.

> Some examples of strings that would match this regex are "ABC123_456_DEF" and "GHI789_012_JKL".



🤓 Steps in Summary :

1. Identify the validation requirement that cannot be achieved using the built-in validation annotations.
2. Create a custom annotation with the `@Constraint` annotation to specify the validator class.
3. Implement the validator class to define the validation logic based on the requirements.

### <span style='color: navy'>Entity Lifecycle Callback Methods</span>

Entity lifecycle callback methods are methods that are invoked when the entity state changes, such as when it is persisted, updated, removed or loaded. These methods are useful for validating entity fields and updating transient state that is not usually persisted with the entity. Entity lifecycle callback methods can be defined on the entity class itself or on a separate entity listener class.

There are 7️⃣ optional lifecycle events that are supported by JPA :

- @`PrePersist` : This annotation is used to mark a method that will be called before persisting the entity. Persisting means saving the entity data to the database for the first time. For example, we might use this method to validate the data or set some default values. 🆕
- `@PostPersist` : This annotation is used to mark a method that will be called after persisting the entity. This means that the entity data has been successfully inserted into the database. For example, we might use this method to send a confirmation email or generate a report. 🎉
- `@PreRemove` : This annotation is used to mark a method that will be called before removing the entity. Removing means deleting the entity data from the database. For example, we might use this method to backup the data or check some conditions. 🗑️
- `@PostRemove` : This annotation is used to mark a method that will be called after removing the entity. This means that the entity data has been successfully deleted from the database. For example, we might use this method to notify other users or clean up some resources. 💥
- `@PreUpdate` : This annotation is used to mark a method that will be called before updating the entity. Updating means modifying the entity data in the database. For example, we might use this method to audit the changes or encrypt some fields. 🔄
- `@PostUpdate` : This annotation is used to mark a method that will be called after updating the entity. This means that the entity data has been successfully updated in the database. For example, we might use this method to refresh the entity state or log the results. ✅
- `@PostLoad` : This annotation is used to mark a method that will be called after loading the entity. Loading means fetching the entity data from the database. For example, we might use this method to format the data or initialize some variables. 🔎

To use these annotations, we need to have a void method with no arguments in the entity class or with one argument of the entity type in the listener class. We can also use both approaches at the same time.

Here is an example of an entity class with some callback methods:

```java
@Entity
public class MyEntity {

  @Id
  @GeneratedValue
  private int id;
  private String msg;
  private int age;

  public MyEntity() {}

  public MyEntity(String msg) {
    this.msg = msg;
  }

  //This method will be called before persisting the entity 🆕
  @PrePersist
  private void validate() {
    //This will check if the message is not null or empty
    if (msg == null || msg.isEmpty()) {
      throw new IllegalArgumentException("Message cannot be null or empty");
    }
    //This will check if the age is positive
    if (age < 0) {
      throw new IllegalArgumentException("Age cannot be negative");
    }
    
    //This will calculate the age from the date of birth and set it to the age field 🎂
    this.age = Period.between(dateOfBirth, LocalDate.now()).getYears();
  }

  //This method will be called after persisting the entity 🎉
  @PostPersist
  public void notify() {
    //This will send an email notification to the admin
    EmailService.sendEmail("admin@example.com", "New entity created", "A new entity with id " + id + " and message " + msg + " has been created.");
  }

  //This method will be called before updating the entity 🔄
  @PreUpdate
  public void audit() {
    //This will log the changes to the entity
    AuditService.logChange("Entity with id " + id + " has been updated. New message: " + msg + ", new age: " + age);
  }

  //This method will be called after updating the entity ✅
  @PostUpdate
  public void refresh() {
    //This will refresh the entity state from the database
    EntityManager em = EntityManagerFactory.createEntityManager();
    em.refresh(this);
    em.close();
  }

  //This method will be called before removing the entity 🗑️
  @PreRemove
  public void backup() {
    //This will backup the entity data to a file
    BackupService.backupEntity(this);
  }

  //This method will be called after removing the entity 💥
  @PostRemove
  public void delete() {
    //This will delete the associated files of the entity
    FileService.deleteFiles(id);
  }

  //This method will be called after loading the entity 🔎
  @PostLoad
  public void format() {
    //This will format the message to uppercase
    msg = msg.toUpperCase();
  }

  //Getters and setters omitted for brevity
}
```

### <span style='color: navy'>Entity Listeneres</span>

JavaEE entity listeners are classes that can listen to events that happen to entities in a persistence context. Entities are objects that represent data stored in a database. A persistence context is a set of entities that are managed by an entity manager. 🗂

Entity listeners can be used to perform actions before or after an entity is created, updated, deleted or loaded from the database. For example, you can use an entity listener to validate data, generate timestamps, audit changes, send notifications or encrypt/decrypt data. 🔎

In summary and in a very simple term we can define it as an abstract class, that we put our lifecycle callback methods there, so that we can have a clean and more readable classes. 😋(～￣▽￣)～

To create an entity listener, you need to:

- Define a class that implements one or more callback methods. A callback method is a method that is annotated with one of the following annotations: `@PrePersist`, `@PostPersist`, `@PreUpdate`, `@PostUpdate`, `@PreRemove`, `@PostRemove`, `@PostLoad`. Each annotation specifies when the method will be invoked. 📝
- Register the entity listener class with one or more entity classes. You can do this by using the `@EntityListeners` annotation on the entity class and specifying the entity listener class as its value. Alternatively, you can use the `<entity-listeners>` element in the `orm.xml` file. 📌

Here is an example of an entity listener class that generates a creation and update timestamp for an entity:

```java
// EntityListener.java
import java.util.Date;
import javax.persistence.PrePersist;
import javax.persistence.PreUpdate;

public class EntityListener {

  // This method will be called before an entity is persisted (inserted) into the database
  @PrePersist
  public void prePersist(Object obj) {
    if (obj instanceof Timestamped) { // Check if the entity implements the Timestamped interface
      Timestamped entity = (Timestamped) obj; // Cast the object to the Timestamped type
      Date now = new Date(); // Get the current date and time
      entity.setCreatedAt(now); // Set the creation timestamp
      entity.setUpdatedAt(now); // Set the update timestamp
    }
  }

  // This method will be called before an entity is updated in the database
  @PreUpdate
  public void preUpdate(Object obj) {
    if (obj instanceof Timestamped) { // Check if the entity implements the Timestamped interface
      Timestamped entity = (Timestamped) obj; // Cast the object to the Timestamped type
      Date now = new Date(); // Get the current date and time
      entity.setUpdatedAt(now); // Set the update timestamp
    }
  }
}
```

Here is an example of an entity class that uses the entity listener class:

```java
// User.java
import java.util.Date;
import javax.persistence.Entity;
import javax.persistence.EntityListeners;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
@EntityListeners(EntityListener.class) // This annotation registers the EntityListener class as an entity listener for this class
public class User implements Timestamped { // This class implements the Timestamped interface

  @Id
  @GeneratedValue
  private Long id;
  private String name;
  private String email;
  private Date createdAt;
  private Date updatedAt;
    
  // Getters and setters for all fields
}
```

Here is an example of an interface that defines the methods for setting and getting timestamps:

```java
// Timestamped.java
import java.util.Date;

public interface Timestamped {

  // Set the creation timestamp
  void setCreatedAt(Date date);

  // Get the creation timestamp
  Date getCreatedAt();

  // Set the update timestamp
  void setUpdatedAt(Date date);

  // Get the update timestamp
  Date getUpdatedAt();
}
```

This example shows how you can use an entity listener to automatically generate timestamps for your entities without having to write any code in your entity classes. This can be useful for tracking changes and auditing purposes. 🕒

### <span style='color: navy'>Native Queries</span>

JPA Native Queries are queries that use the native SQL syntax of the database that you are using. JPA stands for Java Persistence API, which is a standard for accessing and managing data in Java applications. 📚

JPA Native Queries can be used when you need to perform complex or vendor-specific queries that are not supported by JPQL (Java Persistence Query Language), which is the default query language of JPA. For example, you can use native queries to call stored procedures, use database functions, join tables without relationships, or optimize performance. 🚀

In summary and in a very simple term; it is the SQL native queries, like `SELECT * FROM Employee`.

### <span style='color: navy'>Overriding hashCode and equals methods</span>

📝 **JavaEE - JPA Overriding equals and hashcode**

In JavaEE, the Java Persistence API (JPA) is used to manage relational data in applications. When using JPA, it’s important to override the `equals` and `hashCode` methods for your entity classes. This is because JPA uses these methods to determine if two instances of an entity are equal.

🤔 **Why override equals and hashCode?**

Overriding `equals` and `hashCode` is important because it allows JPA to correctly identify if two instances of an entity are equal. This is useful when you’re working with collections of entities, such as when you’re using a `Set` to store a collection of entities.

👨‍💻 **How to override equals and hashCode?**

To override `equals` and `hashCode`, you need to provide your own implementation of these methods in your entity class. Here’s an example of how you might do this for an entity class called `Person`:

```java
@Entity
public class Person {
    @Id
    private Long id;
    private String name;
    private int age;
    private String departmentName;

    // getters and setters

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Department department = (Department) o;
        return Objects.equals(getDepartmentName().toUpperCase(), department.getDepartmentName().toUpperCase());
    }

    @Override
    public int hashCode() {
        return Objects.hash(getDepartmentName().toUpperCase());
    }
}
```

In this example, we’ve overridden the `equals` method to compare the `id`, `name`, and `age` fields of two `Person` objects. We’ve also overridden the `hashCode` method to generate a hash code based on these same fields.

🌎 **Real-world example**

Here’s a real-world example that shows how overriding `equals` and `hashCode` can be useful. Let’s say we have a `Set<Person>` that stores a collection of `Person` objects. If we don’t override `equals` and `hashCode`, then adding two `Person` objects with the same field values to the set will result in two separate entries in the set:

```java
Set<Person> people = new HashSet<>();
Person person1 = new Person(1L, "Alice", 25);
Person person2 = new Person(1L, "Alice", 25);
people.add(person1);
people.add(person2);
System.out.println(people.size()); // prints 2
```

However, if we do override `equals` and `hashCode`, then adding two `Person` objects with the same field values to the set will result in only one entry in the set:

```java
Set<Person> people = new HashSet<>();
Person person1 = new Person(1L, "Alice", 25);
Person person2 = new Person(1L, "Alice", 25);
people.add(person1);
people.add(person2);
System.out.println(people.size()); // prints 1
```



## <span style='color: #8b0000'>Done</span>

- [x] Day 25/90 ===> [JavaEE: Day 25/90 - JPA (Java Persistence API) (continue)](https://www.linkedin.com/pulse/javaee-day-2590-jpa-java-persistence-api-continue-seyed-ali)



# <span style='color: olive'>Day 31/90</span>

Date : 12-May-2023



## <span style='color: grey'>JAX-RS : Java API for RESTful Web Services</span>

📝 **JAX-RS**

JAX-RS stands for **Java API for RESTful Web Services**. It’s a Java programming language API that provides support for creating web services according to the Representational State Transfer (REST) architectural pattern. JAX-RS uses annotations to simplify the development and deployment of web services.

🤔 **What does JAX-RS do?**

JAX-RS allows you to easily create RESTful web services in Java. With JAX-RS, you can define the endpoints of your web service using annotations, making it easy to map HTTP requests to Java methods. JAX-RS also provides support for common tasks such as request and response handling, content negotiation, and exception handling.

👍 **Advantages of JAX-RS**

Some advantages of using JAX-RS include:

- It’s easy to use: JAX-RS uses annotations to simplify the development of web services.
- It’s flexible: JAX-RS allows you to create web services that can handle a wide range of data formats and HTTP methods.
- It’s portable: JAX-RS is part of the Java EE standard, so you can use it in any Java EE application server.

🌟 **JAX-RS vs Spring Boot**

Spring Boot is another popular framework for creating web services in Java. Like JAX-RS, Spring Boot makes it easy to create RESTful web services using annotations. However, there are some differences between the two:

- **Architecture**: Spring Boot is a standalone framework that can be used independently of other technologies. JAX-RS, on the other hand, is part of the Java EE standard and is designed to be used within a Java EE application server. This means that Spring Boot may be easier to set up and use in a standalone environment, while JAX-RS may be better suited for use within a larger Java EE application.
- **Features**: Spring Boot provides more features out-of-the-box than JAX-RS. For example, Spring Boot includes built-in support for data access, security, and testing. With JAX-RS, you may need to use additional libraries or frameworks to achieve the same level of functionality. This means that Spring Boot may be easier to use for developers who want a comprehensive solution with minimal setup.
- **Community**: Spring Boot has a larger and more active community than JAX-RS. This means that there are more resources available for learning and troubleshooting Spring Boot, and it may be easier to find help when you run into problems. Additionally, the larger community may result in more frequent updates and improvements to the framework.

Ultimately, the choice between JAX-RS and Spring Boot depends on your specific needs and preferences. Both frameworks are powerful tools for creating web services in Java.

### <span style='color: navy'>REST Architecture Constraints</span>

📝**REST architecture constraints**

REST stands for**Representational State Transfer**. It’s an architectural style for building web services that is based on a set of constraints. These constraints are designed to ensure that web services built using REST are scalable, reliable, and easy to use.

Here are the main constraints of the REST architecture:

- **Client-server**: This constraint means that the web service is divided into two parts: a client and a server. The client is responsible for sending requests to the server, and the server is responsible for processing those requests and returning responses. This separation of concerns allows the client and server to evolve independently of each other. An example of client is the chrome browser; or any browser that you are making a request from. 🖥️📱 

  A real-world example of the client-server constraint is a web application that uses a RESTful API. The web application (the client) sends requests to the API (the server) to retrieve or update data. The server processes the requests and returns responses to the client. This separation of concerns allows the web application and the API to evolve independently of each other. 🖥️📱

- **Stateless**: This constraint means that the server does not store any information about the client’s state. Instead, all the information needed to process a request is included in the request itself. This makes it easier to scale the web service because the server doesn’t need to keep track of client state. 📈

  A real-world example of the stateless constraint is an e-commerce website that uses a RESTful API. When a user adds an item to their cart, the website sends a request to the API that includes all the information needed to update the cart, such as the user’s ID and the ID of the item being added. The server processes the request and returns a response without storing any information about the user’s state. This makes it easier to scale the web service because the server doesn’t need to keep track of client state. 📈

- **Cacheable**: This constraint means that responses from the server can be cached by the client or intermediate proxies. This can improve performance by reducing the number of requests that need to be sent to the server. ⏱️

  A real-world example of the cacheable constraint is a news website that uses a RESTful API. When a user requests the latest news stories, the website sends a request to the API. The server returns a response that includes the news stories and a cache control header that indicates how long the response can be cached. The website can then cache the response and use it to fulfill future requests for the same information without sending another request to the server. This can improve performance by reducing the number of requests that need to be sent to the server. ⏱️

- **Uniform interface**: This constraint means that all resources exposed by the web service are accessed using a common set of methods and formats. This makes it easier for clients to interact with the web service because they don’t need to learn different interfaces for different resources. 🧩

  A real-world example of the uniform interface constraint is a social media website that uses a RESTful API. The website exposes resources such as users, posts, and comments using a common set of methods and formats. For example, to retrieve information about a user, a client can send a GET request to `/users/{id}`. To create a new post, a client can send a POST request to `/posts` with the post data in the request body. This uniform interface makes it easy for clients to interact with different resources using a common set of methods and formats. 🧩

- **Layered system**: This constraint means that the web service can be composed of multiple layers, such as load balancers, caches, and proxies. Each layer can perform specific tasks, such as routing requests or caching responses. This can improve scalability and reliability by allowing different layers to handle different aspects of the web service. 🧅

  A real-world example of the layered system constraint is an online gaming platform that uses a RESTful API. The platform may use multiple layers such as load balancers, caches, and proxies to handle different aspects of its web service. For example, when a user sends a request to join a game, their request may first pass through a load balancer that routes it to an available server. The server may then check its cache for information about available games before processing the request and returning a response. This layered architecture can improve scalability and reliability by allowing different layers to handle different aspects of the web service. 🧅

### <span style='color: navy'>Root Resource - Application Class</span>

📝 **Root resource in JAX-RS: Application class**

In JAX-RS, a root resource is a class that defines the endpoints of your web service. The root resource class is annotated with `@Path` to specify the base URI for the resource.

The `Application` class is a special class in JAX-RS that acts as the entry point for your web service. The `Application` class extends `javax.ws.rs.core.Application` and is used to configure your web service. You can use the `Application` class to specify the root resource classes and providers that your web service uses.

Here’s an example of how you might create an `Application` class for a simple web service:

```java
import javax.ws.rs.ApplicationPath;
import javax.ws.rs.core.Application;
import java.util.HashSet;
import java.util.Set;

@ApplicationPath("/api/v1")
public class MyApplication extends Application {
    @Override
    public Set<Class<?>> getClasses() {
        Set<Class<?>> classes = new HashSet<>();
        classes.add(MyResource.class);
        return classes;
    }
}
```

In this example, we’ve created an `Application` class called `MyApplication` that specifies the base URI for our web service as `/api`. We’ve also overridden the `getClasses` method to specify that our web service uses a root resource class called `MyResource`.

Here are the steps to create an `Application` class for your JAX-RS web service:

1. Create a new class that extends `javax.ws.rs.core.Application`.

2. Annotate your class with `@ApplicationPath` and specify the base URI for your web service.

3. Override the `getClasses` method to specify the root resource classes and providers that your web service uses (This is for when you want to have another root resource; like when you upgrade your version `/api/v2`).

4. Register your `Application` class with your Java EE application server.

   

   **Note 📝: **The `/api/v1` or `/api/v2` in a URL path typically indicates the version of the API being used. This is a common practice in API design to allow for changes and updates to the API without breaking existing clients.

   When an API is updated with new features or changes to existing functionality, it’s often released as a new version. By including the version number in the URL path, clients can specify which version of the API they want to use. This allows clients to continue using an older version of the API while they update their code to support the new version.

   For example, let’s say an API has two versions: `v1` and `v2`. A client that wants to use the `v1` version of the API would send requests to `/api/v1`, while a client that wants to use the `v2` version would send requests to `/api/v2`. This allows both versions of the API to coexist and be used by different clients.

   Using versioning in the URL path is just one way to manage changes and updates to an API. Other approaches include using request headers or query parameters to specify the API version.

### <span style='color: navy'>Simple Hello World Example</span>

Let's do a simple hello world example with JAX-RS.

HelloResource class

```java
@Path("/hello")
public class HelloResource {
    @Path("{name}")
    @GET
    public Response sayHello(`@PathParam`("name") String name) {
        String greeting = "Hello " + name;
        return Response.ok(greeting).build();
    }
}
```

📝 **Code explanation**

This code defines a JAX-RS resource class called `HelloResource`. The class has a single method called `sayHello` that is annotated with `@Path` and `@GET`.

The `@Path` annotation on the class specifies that the base URI for all methods in the class is `/hello`. The `@Path` annotation on the `sayHello` method specifies that the URI for this specific method is `/hello/{name}`.

The `sayHello` method takes a single parameter called `name` that is annotated with ``@PathParam``. This annotation indicates that the value of the `name` parameter should be taken from the `{name}` path parameter in the URI.

When a GET request is sent to `/hello/{name}`, it will be handled by the `sayHello` method. The method will extract the value of the `{name}` path parameter from the URI and use it to create a greeting. The greeting is then returned in the response using the `Response.ok` method.

For example, if a GET request is sent to `/hello/Bob`, it will be handled by the `sayHello` method with a `name` parameter value of `"Bob"`. The method will create a greeting of `"Hello Bob"` and return it in the response.

### <span style='color: navy'>`@Path`</span>

📝 **@Path in JAX-RS**

In JAX-RS, the `@Path` annotation is used to specify the URI path for a resource. The `@Path` annotation can be applied to both classes and methods.

When applied to a class, the `@Path` annotation specifies the base URI for all the methods in the class. When applied to a method, the `@Path` annotation specifies the URI for that specific method.

Here’s an example of how you might use the `@Path` annotation in a JAX-RS resource class:

```java
@Path("/users")
public class UserResource {
    @GET
    @Path("/{id}")
    public Response getUser(`@PathParam`("id") int id) {
        // retrieve user with the specified id
        User user = ...;
        return Response.ok(user).build();
    }

    @POST
    public Response createUser(User user) {
        // create a new user
        ...
        return Response.created(...).build();
    }
}
```

In this example, we’ve applied the `@Path` annotation to the `UserResource` class to specify that all methods in the class have a base URI of `/users`. We’ve also applied the `@Path` annotation to the `getUser` method to specify that it has a URI of `/users/{id}`. This means that a GET request to `/users/123` would be handled by the `getUser` method with an `id` parameter value of `123`.

### <span style='color: navy'>HTTP Requests</span>

📝 **HTTP requests and methods in JAX-RS**

In JAX-RS, you can use annotations to map HTTP requests and their methods (GET, POST, PUT, DELETE) to Java methods. This allows you to define the behavior of your web service when it receives different types of HTTP requests.

Here’s an example of how you might use these annotations in a JAX-RS resource class:

```java
@Path("/users")
public class UserResource {
    @GET
    public Response getUsers() {
        // retrieve all users
        List<User> users = ...;
        return Response.ok(users).build();
    }

    @POST
    public Response createUser(User user) {
        // create a new user
        ...
        return Response.created(...).build();
    }

    @Path("/{id}")
    @PUT
    public Response updateUser(`@PathParam`("id") int id, User user) {
        // update the user with the specified id
        ...
        return Response.ok().build();
    }

    @Path("/{id}")
    @DELETE
    public Response deleteUser(`@PathParam`("id") int id) {
        // delete the user with the specified id
        ...
        return Response.noContent().build();
    }
}
```

In this example, we’ve created a `UserResource` class that defines four methods: `getUsers`, `createUser`, `updateUser`, and `deleteUser`. Each method is annotated with one of the HTTP method annotations (`@GET`, `@POST`, `@PUT`, `@DELETE`) to specify which type of HTTP request it should handle.

The `getUsers` method is annotated with `@GET` and will be invoked when the web service receives a GET request to `/users`. The method retrieves a list of all users and returns it in the response.

The `createUser` method is annotated with `@POST` and will be invoked when the web service receives a POST request to `/users`. The method creates a new user using the data provided in the request body and returns a response indicating that the user was created.

The `updateUser` method is annotated with `@PUT` and `@Path("/{id}")` and will be invoked when the web service receives a PUT request to `/users/{id}`. The method updates the user with the specified `id` using the data provided in the request body and returns a response indicating that the update was successful.

The `deleteUser` method is annotated with `@DELETE` and `@Path("/{id}")` and will be invoked when the web service receives a DELETE request to `/users/{id}`. The method deletes the user with the specified `id` and returns a response indicating that the deletion was successful.

#### provide an example here!

# <span style='color: crimson'>JpaRepository Equivalent in JavaEE</span>

You know there is `JpaRepository` in spring boot right? It makes it easy to perform the basic CRUD operations right out of the box. It is predefined there. 

Unfortunately, JavaEE doesn't have that predefined out of the box `JpaRepository`. we have to implement each and every CRUD method everytime we make a new project. Though you shall not have fear, because, there are external libraries out there that help us with that. One of them is `DeltaSpike`!

You basically include the dependency in you `pom.xml` file, then create an interface that extends `EntityRepository`, provide the information (since it is a generic type), and viola~ You have something just like `JpaRepository`! 

Step 1️⃣: Dependency 

```xml
<dependency>
	<groupId>org.apache.deltaspike.core</groupId>
    <artifactId>deltaspike-core-api</artifactId>
    <version>1.9.5</version>
</dependency>
<dependency>
	<groupId>org.apache.deltaspike.core</groupId>
    <artifactId>deltaspike-core-impl</artifactId>
    <version>1.9.5</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>org.apache.deltaspike.modules</groupId>
    <artifactId>deltaspike-data-module-api</artifactId>
    <version>1.9.5</version>
</dependency>
<dependency>
    <groupId>org.apache.deltaspike.modules</groupId>
    <artifactId>deltaspike-data-module-impl</artifactId>
    <version>1.9.5</version>
    <scope>runtime</scope>
</dependency>
```

Step 2️⃣: Create the interface and extend `EntityRepository`

```java
public interface EmployeeRepository extends EntityRepository<Employee, Long> { }
```

​			I named it Just like Spring boot naming convention 😝😊

Step 3️⃣: Inject it in your service layer and done! You are good to go.

## <span style='color: #8b0000'>Done</span>

- [x] Day 31/90 ===> [JavaEE: Day 31/90 - JAX-RS (Java API for RESTful Web Services)](https://www.linkedin.com/pulse/javaee-day-3190-jax-rs-java-api-restful-web-services-seyed-ali)



# <span style='color: olive'>Day 32/90</span>

Date : 13-May-2023



## <span style='color: grey'>JAX-RS : Java API for RESTful Web Services (continue)</span>

### <span style='color: navy'>Content Type</span>

📝 In JavaEE JAX-RS, the content type is a crucial concept that determines how data is represented and transmitted over HTTP. 😎

🤔 So, what exactly is the content type? 🤔 The content type is essentially a header in an HTTP request or response that specifies the format of the data being sent or received. It tells the recipient how to handle the incoming data.

👨‍💻 In JavaEE JAX-RS, you can use the @Produces and @Consumes annotations to specify the content type for a RESTful web service. 🌐

🚀 The @Produces annotation specifies the MIME media type(s) that a resource method can produce and send back to the client.

📥 On the other hand, the @Consumes annotation specifies the MIME media type(s) that the resource method can accept as input from the client.

Some examples of content type is : `application/json`,  `text/plain`, `text/html`, `application/xml`, `image/png`.

It's important to note that the choice of content type depends on the requirements of your application. For instance, if you're building a web service that returns an image, you might use `image/png` as the content type. Alternatively, if you're building a web service that returns a list of items in XML format, you might use `application/xml` as the content type.

### <span style='color: navy'>Declaring Content Type - `@Produces`</span>

📝 In JavaEE JAX-RS, the @Produces annotation is used to declare the content type(s) that a resource method can produce and send back to the client. 🌐

👨‍💻 To use the @Produces annotation, simply add it to the method signature of your resource class or method, followed by the MIME media type(s) that the method can produce.

🚀 For example, let's say you have a RESTful web service that returns a list of customers in JSON format. You can specify the content type using the @Produces annotation like this:

```java
@GET
@Path("/customers")
@Produces("application/json") // <-- this is both method-level and class-level
public List<Customer> getCustomers() {
    // retrieve list of customers from database
    List<Customer> customers = customerService.getCustomers();
    return customers;
}
```

In this example, the @Produces annotation is used to specify that the method produces a response in `application/json` format.

🔑 Declaring the content type using the @Produces annotation ensures that the client knows how to handle the data being sent back by the server. It also helps to ensure that the response is properly formatted and compatible with different clients and platforms.

A note to make is that `@Produces` is both class and method level annotation. So you can easily annotate your class with it.

### <span style='color: navy'>Declaring Content Type - `@Consumes`</span>

When you’re building a RESTful web service using Java EE JAX-RS, you might need to consume data from an HTTP request body. The **@Consumes** annotation is used to declare the content type of the data that you’re consuming. This is important because it tells the server how to parse the data that’s being sent in the request body.

For example, let’s say that you’re building a web service that allows users to create new accounts. When a user submits the account creation form, the data is sent to the server in the request body. You can use the **@Consumes** annotation to specify that the data is in JSON format:

```java
@POST
@Path("/accounts")
@Consumes(MediaType.APPLICATION_JSON)
public Response createAccount(Account account) {
    // your code here
}
```

In this example, the **createAccount()** method consumes JSON data and maps it to an **Account** object. The server knows how to parse the JSON data because of the **@Consumes** annotation.

You might also use the **@Consumes** annotation when you’re building a web service that accepts file uploads. In this case, you would use the **MediaType.MULTIPART_FORM_DATA** media type:

```java
@POST
@Path("/upload")
@Consumes(MediaType.MULTIPART_FORM_DATA)
public Response uploadFile(@FormDataParam("file") InputStream fileInputStream,
                           @FormDataParam("file") FormDataContentDisposition fileMetaData) {
    // your code here
}
```

In this example, the **uploadFile()** method consumes multipart form data and maps it to an **InputStream** object and a **FormDataContentDisposition** object.

🌎 Real world example of `@Consumes` in JAX-RS 🌎

- Suppose you want to create a RESTful web service that can handle different types of requests with different media types, such as XML, JSON, plain text, etc. 🌐
- You can use the `@Consumes` annotation to specify the media type of the request body that your web service method can accept 📥
- For example, if you have a method that can create a new user from a JSON request, you can annotate it with `@Consumes(MediaType.APPLICATION_JSON) `🗂️
- This way, your web service will only accept requests with JSON data and reject other types of requests with 406 Not Acceptable status code 🚫
- You can also use multiple values for @Consumes, such as `@Consumes({MediaType.APPLICATION_XML, MediaType.TEXT_PLAIN})` to accept both XML and plain text requests 📄
- This way, your web service will be more flexible and interoperable with different clients and formats 🙌

Here is a sample:

```java
@Path("/user")
public class UserResource {

  // Create a new user from a JSON request
  @POST
  @Consumes(MediaType.APPLICATION_JSON)
  public Response createUser(User user) {
    // Do something with the user object
    return Response.ok().build();
  }

  // Get a user by id as an XML response
  @GET
  @Path("{id}")
  @Produces(MediaType.APPLICATION_XML)
  public User getUser(`@PathParam`("id") int id) {
    // Find the user by id
    return user;
  }

  // Update a user by id from a plain text request
  @PUT
  @Path("{id}")
  @Consumes(MediaType.TEXT_PLAIN)
  public Response updateUser(`@PathParam`("id") int id, String name) {
    // Update the user name by id
    return Response.ok().build();
  }
}
```

Generally, `@Consumes` is used on `@POST` and `@PUT` methods.

### <span style='color: navy'>Path Params with ``@PathParam``</span>

Path parameters are a way of passing dynamic values in the URI of a RESTful web service. They are useful when you want to identify a specific resource or a subset of resources from a collection.👍

For example, if you have a web service that manages users, you can use path parameters to get the information of a particular user by their username or id.👥

To use path parameters in JAX-RS, you need to do the following steps:

- Annotate your class with **@Path** and specify a base URI for your web service. For example, **@Path(“/users”)**.🏷️
- Annotate your method with **@Path** and specify a template that contains one or more variables enclosed in curly braces. For example, **@Path(“/{username}”)**.🔖
- Annotate your method parameter with **`@PathParam`** and specify the name of the variable that matches the one in the template. For example, **`@PathParam`(“username”) String username**.📝
- Use the parameter value in your method logic as needed. For example, return a response with the user information based on the username.📤

Here is a code example that shows how to use path parameters in JAX-RS:👇

```java
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.PathParam;
import javax.ws.rs.core.Response;

@Path("/users")
public class UserRestService {

  @GET
  @Path("/{username}")
  public Response getUserInfo(`@PathParam`("username") String username) {
    // get user information from database or other source
    User user = findUserByUsername(username);
    // return a response with user information
    return Response.ok(user).build();
  }
}
```

In this example, the web service can handle requests like **/users/mkyong** or **/users/javacodegeeks** and return the corresponding user information.😎

#### <span style='color: navy'>Restricting User Input In PathParam with RegEx</span>

Sometimes, you may want to restrict the path parameter values to match a certain pattern or format. For example, you may want to accept only numbers or only letters or only specific words.👌

To do that, you can use regular expressions (regex) to specify the constraints on the path parameter values. Regex are a way of describing patterns of characters using special symbols and rules.👍

To use regex with path parameters in JAX-RS, you need to do the following steps:

- Annotate your class with **@Path** and specify a base URI for your web service. For example, **@Path(“/users”)**.🏷️
- Annotate your method with **@Path** and specify a template that contains one or more variables enclosed in curly braces. For example, **@Path(“/{username}”)**.🔖
- Inside the curly braces, after the variable name, add a colon (:) and then write the regex that defines the valid values for the variable. For example, **@Path(“/{username: [a-z]+}”)**.📝
- Annotate your method parameter with **`@PathParam`** and specify the name of the variable that matches the one in the template. For example, **`@PathParam`(“username”) String username**.📝
- Use the parameter value in your method logic as needed. For example, return a response with the user information based on the username.📤

Here is a code example that shows how to use regex with path parameters in JAX-RS:👇

```java
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.PathParam;
import javax.ws.rs.core.Response;

@Path("/users")
public class UserRestService {

  @GET
  @Path("/{username: [a-z]+}") // <-- provide ur custom regex
  public Response getUserInfo(`@PathParam`("username") String username) {
    // get user information from database or other source
    User user = findUserByUsername(username);
    // return a response with user information
    return Response.ok(user).build();
  }
}
```

In this example, the web service can handle requests like **/users/mkyong** or **/users/javacodegeeks**, but not **/users/1234** or **/users/MKYONG**, because the regex **[a-z]+** means one or more lowercase letters.😎

You can use different regex to match different patterns of values. For example, you can use **[0-9]+** to match one or more digits, or **(red|green|blue)** to match one of these three words.😊

#### <span style='color: navy'>Setting Default Value for Path Params</span>

Setting default value for path params means that you can specify a value that the path parameter will assume if no other value is provided in the request URI. This can be useful when you want to have a fallback option or a common case for the path parameter.👍

For example, if you have a web service that returns the weather information for a given city, you can set a default value for the city parameter to be your current location. This way, if the user does not specify any city in the request URI, they will get the weather information for their current location.👌

To set default value for path params in JAX-RS, you need to do the following steps:

- Annotate your class with **@Path** and specify a base URI for your web service. For example, **@Path(“/weather”)**.🏷️
- Annotate your method with **@Path** and specify a template that contains one or more variables enclosed in curly braces. For example, **@Path(“/{city}”)**.🔖
- Annotate your method parameter with **@PathParam** and specify the name of the variable that matches the one in the template. For example, **@PathParam(“city”) String city**.📝
- Annotate your method parameter with **@DefaultValue** and specify the value that you want to use as the default for the path parameter. For example, **@DefaultValue(“Redmond”) @PathParam(“city”) String city**.📝
- Use the parameter value in your method logic as needed. For example, return a response with the weather information based on the city.📤

Here is a code example that shows how to set default value for path params in JAX-RS:👇

```java
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.PathParam;
import javax.ws.rs.DefaultValue;
import javax.ws.rs.core.Response;

@Path("/weather")
public class WeatherRestService {

  @GET
  @Path("/{city}")
  public Response getWeatherInfo(@DefaultValue("Redmond") @PathParam("city") String city) { // <-- default value set to Redmond
    // get weather information from some source
    Weather weather = findWeatherByCity(city);
    // return a response with weather information
    return Response.ok(weather).build();
  }
}
```

In this example, the web service can handle requests like **/weather/Seattle** or **/weather/New York**, but also **/weather/**, which will use the default value of Redmond for the city parameter.😎

🥬🍃 To set default value for path params in Spring Boot framework, you need to do similar steps:

- Annotate your class with **@RestController** and optionally specify a base URI for your web service using **@RequestMapping**. For example, **@RestController @RequestMapping(“/weather”)**.🏷️
- Annotate your method with **@GetMapping** and specify a template that contains one or more variables enclosed in curly braces. For example, **@GetMapping(“/{city}”)**.🔖
- Annotate your method parameter with **@PathVariable** and specify the name of the variable that matches the one in the template. For example, **@PathVariable(“city”) String city**.📝
- Assign a default value to your method parameter using an assignment operator (=). For example, **@PathVariable(“city”) String city = “Redmond”**.📝
- Use the parameter value in your method logic as needed. For example, return a response with the weather information based on the city.📤

Here is a code example that shows how to set default value for path params in Spring Boot framework:👇

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

@RestController
@RequestMapping("/weather")
public class WeatherRestController {

  @GetMapping("/{city}")
  public Weather getWeatherInfo(@PathVariable("city") String city = "Redmond") { // <-- default value set to Redmond!
    // get weather information from some source
    Weather weather = findWeatherByCity(city);
    // return a response with weather information
    return weather;
  }
}
```

In this example, the web service can handle requests like **/weather/Seattle** or **/weather/New**.

#### <span style='color: navy'>`@PathParam` in JavaEE vs `@PathVariable` in Spring-Boot</span>

`@PathParam` and `@PathVariable` are both annotations that allow you to extract values from the URI of a web service. However, they belong to different frameworks and have some differences in usage and features.👍

`@PathParam` is part of the JAX-RS specification, which is a standard for building RESTful web services in Java. JAX-RS provides a set of annotations and interfaces that can be used with different implementations, such as Jersey, RESTEasy, or Apache CXF.👌

`@PathVariable` is part of the Spring framework, which is a comprehensive platform for building Java applications. Spring provides a module called Spring MVC, which supports building web applications using the Model-View-Controller pattern. Spring MVC also supports building RESTful web services using annotations and conventions.👌

Some of the differences between `@PathParam` and `@PathVariable` are:

- `@PathParam` can be used with any HTTP method (GET, POST, PUT, DELETE, etc.), while `@PathVariable` can only be used with GET and DELETE methods by default. To use `@PathVariable` with other methods, you need to enable the hiddenHttpMethodFilter in Spring Boot.👉
- `@PathParam` can use regular expressions to restrict the values of the path parameters. For example, **`@Path(“/{id: [0-9]+}”)`** means that the id parameter must be a number. `@PathVariable` can also use regular expressions, but you need to specify them in the @RequestMapping annotation. For example, **`@RequestMapping(value = “/{id: [0-9]+}”)`**.👉
- `@PathParam` can be optional by using a regular expression that allows an empty value or by using a default value annotation. For example, **`@Path("/{id: (\d\*)?}")`** or **`@DefaultValue(“0”) @PathParam(“id”)`**. `@PathVariable` can be optional by using the required attribute and setting it to false. For example, **`@PathVariable(required = false) String id`**.👉
- `@PathParam` can be used with other JAX-RS annotations, such as `@QueryParam`, `@FormParam`, or `@HeaderParam`, to access different types of parameters from the request. `@PathVariable` can be used with other Spring annotations, such as `@RequestParam`, `@RequestBody`, or `@RequestHeader`, to access different types of parameters from the request.👉
- `@PathParam` can be used with different JAX-RS implementations and runtimes, such as Tomcat, Jetty, or WildFly. `@PathVariable` can be used with Spring Boot and its embedded servers, such as Tomcat, Jetty, or Undertow.👉

### <span style='color: navy'>Query Params With `@QueryParam`</span>

Query params are a way of passing dynamic values in the query string of a RESTful web service. They are useful when you want to **filter, sort, or paginate** a collection of resources or when you want to provide optional parameters for a request.👍

For example, if you have a web service that returns a list of books, you can use query params to get only the books that match a certain genre, author, or title. You can also use query params to sort the books by price or rating or to limit the number of books per page.👌

To use query params in JAX-RS, you need to do the following steps:

- Annotate your class with **`@Path`** and specify a base URI for your web service. For example, **`@Path(“/books”)`**.🏷️
- Annotate your method with **`@GET`** or any other HTTP method that supports query params. For example, **`@GET`**.🔖
- Annotate your method parameters with **`@QueryParam`** and specify the name of the query param that matches the one in the request URI. For example, **`@QueryParam(“genre”) String genre`**.📝
- Use the parameter value in your method logic as needed. For example, return a response with the list of books that match the genre query param.📤

Here is a code example that shows how to use query params in JAX-RS:👇

```java
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.QueryParam;
import javax.ws.rs.core.Response;

@Path("/books")
public class BookRestService {

  @GET
  public Response getBooks(@QueryParam("genre") String genre) { // <-- we used queryparam here!
    // get books from database or other source
    List<Book> books = findBooksByGenre(genre);
    // return a response with books
    return Response.ok(books).build();
  }
}
```

In this example, the web service can handle requests like **/books?genre=fiction** or **/books?genre=romance** and return the corresponding list of books.😎

You can also use multiple query params in your method by adding more parameters annotated with **@QueryParam**. For example, you can add **@QueryParam(“author”) String author** and **@QueryParam(“title”) String title** to filter the books by author and title as well.👍

### <span style='color: navy'>`@QueryParam` in JavaEE vs `@RequestParam` in Spring-Boot</span>

🥬🍃 To use query params in Spring Boot framework, you need to do similar steps:

- Annotate your class with **@RestController** and optionally specify a base URI for your web service using **@RequestMapping**. For example, **@RestController @RequestMapping(“/books”)**.🏷️
- Annotate your method with **@GetMapping** or any other HTTP method that supports query params. For example, **@GetMapping**.🔖
- Annotate your method parameters with **@RequestParam** and specify the name of the query param that matches the one in the request URI. For example, **@RequestParam(“genre”) String genre**.📝
- Use the parameter value in your method logic as needed. For example, return a response with the list of books that match the genre query param.📤

Here is a code example that shows how to use query params in Spring Boot framework:👇

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;

@RestController
@RequestMapping("/books")
public class BookRestController {

  @GetMapping
  public List<Book> getBooks(@RequestParam("genre") String genre) { // <-- alternate of queryparam of JavaEE in Spring-Boot!
    // get books from database or other source
    List<Book> books = findBooksByGenre(genre);
    // return a response with books
    return books;
  }
}
```

In this example, the web service can handle requests like **/books?genre=fiction** or **/books?genre=romance** and return the corresponding list of books.😎

You can also use multiple query params in your method by adding more parameters annotated with **@RequestParam**. For example, you can add **@RequestParam(“author”) String author** and **@RequestParam(“title”) String title** to filter the books by author and title as well.

### <span style='color: navy'>Response Object</span>

<span style='font-size: 25px'>What is a Response Object?</span>

A response object is an instance of the **javax.ws.rs.core.Response** class that defines the contract between a returned instance and the runtime when an application needs to provide metadata to the runtime. An application class should not extend this class directly. Response class is reserved for an extension by a JAX-RS implementation providers.👍

A response object contains information such as:

- The status code of the response, which indicates the outcome of the request. For example, 200 OK, 404 Not Found, 500 Internal Server Error, etc.👌
- The entity of the response, which is the message body that contains the representation of the requested resource or an error message. For example, a JSON object, an XML document, a plain text, etc.👌
- The headers of the response, which are key-value pairs that provide additional information about the response. For example, Content-Type, Content-Length, Location, ETag, etc.👌
- The cookies of the response, which are small pieces of data that can be sent to the client and stored by the browser. For example, session ID, user preferences, etc.👌

<span style='font-size: 25px'>Why to Use a Response Object?</span>

A response object is useful when you want to have more control over the content and format of the response that you send back to the client. For example, you may want to:

- Set a custom status code or message for the response. For example, 201 Created, 204 No Content, 400 Bad Request, etc.👉
- Set or modify headers for the response. For example, Content-Type, Content-Language, Cache-Control, etc.👉
- Set cookies for the response. For example, session ID, user preferences, etc.👉
- Return different types of entities for the response based on the request parameters or headers. For example, JSON or XML based on Accept header.👉
- Return no entity for the response when it is not needed or appropriate. For example, 204 No Content or 254 Not Modified.👉

<span style='font-size: 25px'>How to Use a Response Object?</span>

To use a response object in JAX-RS, you need to use one of the static methods of the Response class to create a **Response.ResponseBuilder** instance and then use the methods of the builder to set the status code, entity, headers, and cookies of the response. Finally, you need to call the **build()** method of the builder to obtain a Response instance and return it from your resource method. For example:

```java
// Return a custom status code and message
@GET
@Path("/custom")
public Response getCustomResponse() {
  String message = "This is a custom response";
  return Response
    .status(202) // 202 Accepted | or use `Response.Status.ACCEPTED`
    .entity(message)
    .build();
}

// Return a different type of entity based on Accept header
@GET
@Path("/books/{id}")
public Response getBook(@PathParam("id") int id, @HeaderParam("Accept") String accept) {
  // get book from database or other source
  Book book = findBookById(id);
  // check the accept header and return different type of entity
  if (accept.equals(MediaType.APPLICATION_JSON)) {
    // convert book to JSON string
    String json = convertBookToJson(book);
    // return JSON string as entity
    return Response
      .ok(json, MediaType.APPLICATION_JSON)
      .build();
  } else if (accept.equals(MediaType.APPLICATION_XML)) {
    // convert book to XML string
    String xml = convertBookToXml(book);
    // return XML string as entity
    return Response
      .ok(xml, MediaType.APPLICATION_XML)
      .build();
  } else {
    // return plain text as entity
    return Response
      .ok(book.toString(), MediaType.TEXT_PLAIN)
      .build();
  }
}

// Return no entity and set a Location header
@POST
@Path("/books")
public Response createBook(Book book) {
  // create book in database or other source and get its id
  int id = createBook(book);
  // create a URI for the created book
  URI uri = UriBuilder.fromUri("/books/{id}").build(id);
  // return no entity and set Location header
  return Response
    .created(uri) // 201 Created
    .build();
}

// Return an entity and set a cookie
@GET
@Path("/books")
public Response getBooks(@CookieParam("user") String user) {
  // get books from database or other source based on user preference
  List<Book> books = findBooksByUser(user);
  // create a new cookie with user preference
  NewCookie cookie = new NewCookie("user", user);
  // return books as entity and set cookie
  return Response
    .ok(books)
    .cookie(cookie)
    .build();
}
```

🥬🍃 To use a response object in Spring Boot framework, you need to use one of the constructors of the **org.springframework.http.ResponseEntity** class to create a ResponseEntity instance and then use the methods of the instance to set the status code, entity, headers, and cookies of the response. Finally, you need to return it from your resource method. For example:

```java
// Return a custom status code and message
@GetMapping("/custom")
public ResponseEntity<String> getCustomResponse() {
  String message = "This is a custom response";
  return new ResponseEntity<>(message, HttpStatus.ACCEPTED); // 202 Accepted
}

// Return a different type of entity based on Accept header
@GetMapping("/books/{id}")
public ResponseEntity<String> getBook(@PathVariable("id") int id,
                                      @RequestHeader("Accept") String accept) {
  // get book from database or other source
  Book book = findBookById(id);
  // check the accept header and return different type of entity
  if (accept.equals(MediaType.APPLICATION_JSON_VALUE)) {
    // convert book to JSON string
    String json = convertBookToJson(book);
    // return JSON string as entity with content type header
    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);
    return new ResponseEntity<>(json, headers, HttpStatus.OK); // 200 OK
  } else if (accept.equals(MediaType.APPLICATION_XML_VALUE)) {
    // convert book to XML string
    String xml = convertBookToXml(book);
    // return XML string as entity with content type header
    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_XML);
    return new ResponseEntity<>(xml, headers, HttpStatus.OK); // 200 OK
  } else {
    // return plain text as entity with content type header
    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.TEXT_PLAIN);
    return new ResponseEntity<>(book.toString(), headers, HttpStatus.OK); // 200 OK
  }
}

// Return no entity and set a Location header
@PostMapping("/books")
public ResponseEntity<Void> createBook(@RequestBody Book book) {
  // create book in database or other source and get its id
  int id = createBook(book);
  // create a URI for the created book
  URI uri = ServletUriComponentsBuilder.fromCurrentRequest()
            .path("/{id}")
            .buildAndExpand(id)
            .toUri();
  // return no entity and set Location header
  return ResponseEntity.created(uri).build(); // 201 Created
}

// Return an entity and set a cookie
@GetMapping("/books")
public ResponseEntity<List<Book>> getBooks(@CookieValue("user") String user) {
  // get books from database or other source based on user preference
  List<Book> books = findBooksByUser(user);
  // create a new cookie with user preference
  ResponseCookie cookie = ResponseCookie.from("user", user)
            .maxAge(3600)
            .httpOnly(true)
            .build();
  // return books as entity and set cookie
  HttpHeaders headers = new HttpHeaders();
  headers.add(HttpHeaders.SET_COOKIE, cookie.toString());
  return new ResponseEntity<>(books, headers, HttpStatus.OK); // 200 OK
}
```

<span style='font-size: 25px'>Comparison with Spring Boot Framework 🥬🍃</span>

Both JAX-RS and Spring Boot framework provide ways to use response objects to customize the HTTP responses from resource methods. However, there are some differences between them:

- JAX-RS uses the **javax.ws.rs.core.Response** class and its nested builder interfaces (**Response.ResponseBuilder**, **Response.HeadersBuilder**, **Response.BodyBuilder**) to create and manipulate response objects. Spring Boot framework uses the **org.springframework.http.ResponseEntity** class and its constructors to create response objects and its methods to manipulate them .👉
- JAX-RS uses annotations such as **@Path**, **@GET**, **@POST**, **@PathParam**, **@QueryParam**, **@HeaderParam**, **@CookieParam**, etc. to map resource methods to URIs and access request parameters and headers. Spring Boot framework uses annotations such as **@RestController**, **@RequestMapping**, **@GetMapping**, **@PostMapping**, **@PathVariable**, **@RequestParam**, **@RequestHeader**, **@CookieValue**, etc. to map resource methods to URIs and access request parameters and headers .👉
- JAX-RS uses the **javax.ws.rs.core.UriBuilder** class and its methods to create URIs for resources. Spring Boot framework uses the **org.springframework.web.servlet.support.ServletUriComponentsBuilder** class and its methods to create URIs for resources .👉
- JAX-RS uses the **javax.ws.rs.core.NewCookie** class and its constructors to create cookies for the response. Spring Boot framework uses the **org.springframework.http.ResponseCookie** class and its builder methods to create cookies for the response .👉

### <span style='color: navy'>POST Request with `URIInfo` interface</span>

<span style='font-size: 25px'>What is UriInfo and getAbsolutePathBuilder?</span>

UriInfo is an injectable interface that provides access to application and request URI information in JAX-RS. We can use the **@Context** annotation to inject an instance of UriInfo into our resource class or method.👍

UriInfo has many methods that allow us to get information about the URI components of the current request, such as:

- **getBaseUri()**: Get the base URI of the application.
- **getAbsolutePath()**: Get the absolute path of the request.
- **getRequestUri()**: Get the absolute request URI including any query parameters.
- **getPath()**: Get the path of the current request relative to the base URI as a string.
- **getPathSegments()**: Get the path of the current request relative to the base URI as a list of PathSegment.
- **getQueryParameters()**: Get the URI query parameters of the current request.
- **getPathParameters()**: Get the values of any embedded URI template parameters.
- **getMatchedResources()**: Get a read-only list of the currently matched resource class instances.
- **getMatchedURIs()**: Get a read-only list of URIs for matched resources.👌

One of these methods is **getAbsolutePathBuilder()**, which returns an instance of UriBuilder initialized with the absolute path of the request. UriBuilder is a class that provides methods to build and manipulate URIs in a fluent and convenient way.👍

Using UriBuilder, we can:

- Append or replace parts of the URI, such as scheme, host, port, path, query, fragment, etc.
- Encode or decode parts of the URI according to RFC 3986 rules.
- Expand URI template variables with values.
- Build a URI instance from the current state of the builder.👌

<span style='font-size: 25px'>Why Do We Need It?</span>

We need UriInfo and getAbsolutePathBuilder when we want to create a new resource using a POST request and return its location in the response. According to RESTful best practices, when we create a new resource using a POST request, we should:

- Return a status code of 201 Created to indicate that the resource was successfully created.
- Return a Location header with the URI of the new resource so that the client can access it later.
- Optionally return a representation of the new resource in the response body.👉

To achieve this, we can use UriInfo and getAbsolutePathBuilder to construct the URI of the new resource using the id of the resource and the base URI of the application. getAbsolutePathBuilder() helps us to get the base URI of the application and append the path of the new resource to it.👍

For example, if we have a resource class that handles POST requests to create users, we can use UriInfo and getAbsolutePathBuilder like this:

```java
@Stateless
@Path("users")
public class UserResource {

  @Context // <-- these 2 lines are important! ⚟
  private UriInfo uriInfo; 

  @POST
  @Consumes(MediaType.APPLICATION_JSON)
  public Response createUser(User user) {
    // create user in database or other source and get its id
    int id = createUser(user);
    // use UriInfo and getAbsolutePathBuilder to construct the URI of the new user
    URI uri = uriInfo.getAbsolutePathBuilder().path(String.valueOf(id)).build();
    // return 201 Created with Location header and optionally user entity
    return Response.created(uri).entity(user).build();
  }
}
```

<span style='font-size: 25px'>When Do We Need It?</span>

We need UriInfo and getAbsolutePathBuilder when we want to follow the RESTful best practices for creating a new resource using a POST request. As we mentioned before, when we create a new resource using a POST request, we should:

- Return a status code of 201 Created to indicate that the resource was successfully created.
- Return a Location header with the URI of the new resource so that the client can access it later.
- Optionally return a representation of the new resource in the response body.👉

UriInfo and getAbsolutePathBuilder help us to achieve these goals by providing us with an easy and convenient way to construct the URI of the new resource based on the id of the resource and the base URI of the application.👌

<span style='font-size: 25px'>How to Use It?</span>

To use UriInfo and getAbsolutePathBuilder in JAX-RS, we need to follow these steps:

- Inject an instance of UriInfo into our resource class or method using the **@Context** annotation.
- Use the **getAbsolutePathBuilder()** method of UriInfo to get an instance of UriBuilder initialized with the absolute path of the request.
- Use the **path()** method of UriBuilder to append the path of the new resource to the base URI. The path can be a string or a template with variables.
- Use the **build()** method of UriBuilder to build a URI instance from the current state of the builder. Optionally, we can pass values for template variables if any.
- Use the **Response.created()** method to create a Response instance with status code 201 Created and Location header set to the URI instance. Optionally, we can pass an entity for the response body.
- Return the Response instance from our resource method.👌

For example, if we have a resource class that handles POST requests to create books, we can use UriInfo and getAbsolutePathBuilder like this:

```java
@Stateless
@Path("books")
public class BookResource {

  @Context
  private UriInfo uriInfo;

  @POST
  @Consumes(MediaType.APPLICATION_JSON)
  public Response createBook(Book book) {
    // create book in database or other source and get its id
    int id = createBook(book);
    // use UriInfo and getAbsolutePathBuilder to construct the URI of the new book
    URI uri = uriInfo.getAbsolutePathBuilder().path("{id}").build(id);
    // return 201 Created with Location header and optionally book entity
    return Response.created(uri).entity(book).build();
  }
}
```

<span style='font-size: 25px'>Comparison with Spring Boot Framework</span>

Both JAX-RS and Spring Boot framework provide ways to use UriInfo and getAbsolutePathBuilder to construct URIs for new resources created by POST requests. However, there are some differences between them:

- JAX-RS uses the **javax.ws.rs.core.UriInfo** interface and its **getAbsolutePathBuilder()** method to get an instance of **javax.ws.rs.core.UriBuilder** initialized with the absolute path of the request. Spring Boot framework uses the **org.springframework.web.servlet.support.ServletUriComponentsBuilder** class and its **fromCurrentRequest()** method to get an instance of **org.springframework.web.util.UriComponentsBuilder** initialized with components of current request .👉
- JAX-RS uses annotations such as **@Path**, **@POST**, **@PathParam**, etc. to map resource methods to URIs and access request parameters. Spring Boot framework uses annotations such as **@RestController**, **@RequestMapping**, **@PostMapping**, **@PathVariable**, etc to map resource methods to URIs and access request parameters.👉
- JAX-RS uses the **Response.created()** method to create a Response instance with status code 201 Created and Location header set to the URI instance. Spring Boot framework uses the **ResponseEntity.created()** method to create a ResponseEntity instance with status code 201 Created and Location header set to the URI instance.👉

## <span style='color: #8b0000'>Done</span>

- [x] Day 32/90 ===> [JavaEE: Day 32/90 - JAX-RS (Java API for RESTful Web Services)](https://www.linkedin.com/pulse/javaee-day-3290-jax-rs-java-api-restful-web-services-seyed-ali)



# <span style='color: olive'>Day 33/90</span>

Date : 14-May-2023



## <span style='color: grey'>JAX-RS : Java API for RESTful Web Services (continue)</span>

Today was coding day <3 finally! 💪🏻 got tired of all the theory concepts 😐

- [x] Day 33/90 ===> [JavaEE: Day 33/90 - JAX-RS (Java API for RESTful Web Services)](https://www.linkedin.com/pulse/javaee-day-3390-jax-rs-java-api-restful-web-services-seyed-ali)



# <span style='color: olive'>Day 34/90</span>

Date : 15-May-2023



## <span style='color: grey'>JAX-RS : Java API for RESTful Web Services (continue)</span>

### <span style='color: navy'>`@Provider`</span>

<span style='font-size: 25px'>What is @Provider?</span>

`@Provider` is an annotation in the JavaEE JAX-RS specification that is used to register components or services in a JAX-RS runtime environment. It is used to annotate classes that provide one of several types of JAX-RS service: message bodies, exception mappers, context providers, and entity filters. ✨ 🌟 ✨

So, imagine you have a RESTful web application built using JAX-RS. You may want to customize how certain parts of the application work, for example, you may want to handle exceptions in a specific way or provide a custom message converter for a particular media type. To do this, you can create a class that implements one of these services and annotate it with `@Provider`.

<span style='font-size: 25px'>Why use @Provider?</span>

You would use `@Provider` when you want to extend or customize the behavior of a JAX-RS runtime environment.

It allows you to add your own functionality to the JAX-RS runtime without modifying the existing implementation. This means you can modify the behavior of your application without having to change the underlying framework. 🚀💥🚀

<span style='font-size: 25px'>When to use @Provider?</span>

You should use `@Provider` whenever you need to customize the behavior of a JAX-RS runtime environment. For example, if you are building an application that uses JSON as its primary data format, you might want to create a custom JSON message converter to handle serialization and deserialization of objects to and from JSON. 🤖🧠🤖

<span style='font-size: 25px'>How to use @Provider?</span>

To use `@Provider`, simply annotate any class that implements one of the JAX-RS service interfaces with `@Provider`. The JAX-RS runtime will automatically discover and register the class, allowing it to be used within your application.

Here's an example of how to create a custom message body reader that handles a specific media type:

```java
@Provider
@Consumes("application/vnd.mycompany.myformat")
public class MyFormatMessageBodyReader implements MessageBodyReader<MyObject> {

  public boolean isReadable(Class<?> type, Type genericType, Annotation[] annotations, MediaType mediaType) {
    return type == MyObject.class;
  }

  public MyObject readFrom(Class<MyObject> type, Type genericType, Annotation[] annotations, MediaType mediaType, 
                           MultivaluedMap<String, String> httpHeaders, InputStream entityStream) throws IOException {
    //read the input stream and create a MyObject instance
  }
}
```

This `MyFormatMessageBodyReader` class can now be used by any JAX-RS resource method in your application that consumes the `application/vnd.mycompany.myformat` media type. 👏🎉👏

<span style='font-size: 25px'>Comparison with Spring Boot</span>

In Spring Boot, the equivalent of `@Provider` is `@Component`.

Both `@Provider` and `@Component` are used to register components or services within a runtime environment. However, Spring Boot uses a different approach to dependency injection than JavaEE, so the implementation details of these two annotations differ. 💻🌟💻

### <span style='color: navy'>ExceptionMapper aka ControllerAdvice in SB</span>

<span style='font-size: 25px'>What is it? 🤔</span>

Exception Mappers in JavaEE JAX-RS are a way to handle exceptions that occur during RESTful web service requests. When an exception is thrown, an Exception Mapper intercepts the exception and maps it to a response that can be returned to the client.

<span style='font-size: 25px'>Why to use? 🤷‍♀️</span>

Using Exception Mappers allows for better error handling and more user-friendly responses. Instead of returning a generic error message to the client, you can return a custom error message that provides more information about what went wrong.

<span style='font-size: 25px'>When to use? 📅</span>

You should use Exception Mappers whenever you want to handle exceptions in a customized way. For example, if you have a RESTful web service that deals with financial transactions, you might want to provide specific error messages for different types of errors such as insufficient funds or invalid account numbers.

<span style='font-size: 25px'>How to use? 🛠️</span>

To use Exception Mappers in JavaEE JAX-RS, you need to create a class that implements the `javax.ws.rs.ext.ExceptionMapper` interface. This class should have a method that takes in the exception object that was thrown and returns a Response object. Within this method, you can customize the response by setting the status code, headers, and entity body.

Here's an example:

```java
@Provider
public class CustomExceptionMapper implements ExceptionMapper<CustomException> {
 
    @Override
    public Response toResponse(CustomException ex) {
        ErrorMessage errorMessage = new ErrorMessage(ex.getMessage(), 400);
        return Response.status(Response.Status.BAD_REQUEST)
                .entity(errorMessage)
                .build();
    }
}
```

In this example, we're creating a custom exception mapper for a `CustomException`. When this exception is thrown, the `toResponse` method will be called, and we're returning a custom error message with a status code of 400.

Here's another example:

```java
@Provider
public class ConstraintViolationExceptionMapper implements ExceptionMapper<ConstraintViolationException> { // <-- we can provide our own custom Exception too!
    @Override
    public Response toResponse(ConstraintViolationException e) {
        Map<String, String> map = new HashMap<>();
        for (ConstraintViolation cv : e.getConstraintViolations()) {
            String path = cv.getPropertyPath().toString();
            map.put(path, cv.getMessage());
            // map.put(path, e.getMessage());
        }
        return Response.status(Response.Status.PRECONDITION_FAILED).entity(map).build();
    }
}
```

<span style='font-size: 25px'>One Improvement 🔝</span>

What is happening right now? In the previous examples, the persistence layer is responsible for Constraint Violations. Persistence layer goes all the way to the method setter and in the implemenation, and will throw the exception from there. 

We can improve that. We can check for violation at Resource layer. We just need to put `@Valid` annotation right before an entity or the request body that is supposed to be entered by user, in the parameter of a resource method. like so:

```java
@Path("/employees")
@Produces(MediaType.APPLICATION_JSON)
public class EmployeeResource {
    @Inject
    private EmployeeService employeeService;

    @Context
    private UriInfo uriInfo;

   	// other methods are hidden for brevity

    @POST // create a new employee - /api/v1/employees/createEmployee
    public Response saveEmployee(@Valid Employee employee) { // <-- right this annotation over here!
        employeeService.saveEmployee(employee);
        URI uri = uriInfo.getAbsolutePathBuilder().path(employee.getId().toString()).build();
        return Response
                .created(uri)
                .status(Response.Status.CREATED)
                .build();
    }
}
```

In this way, the checking of violation will occur in the resource layer and it won't go any deeper into the setters or any method. This is good.

**📝 Note :** we need to make a change from our previous example because we'll get the path of the resource => argument0 => the_field_that_is_being_violated.

```java
put the changes
```

<span style='font-size: 25px'>Comparison with Spring-Boot Framework 🤝</span>

In Spring-Boot, the equivalent of Exception Mappers are `@ControllerAdvice` and `@ExceptionHandler` annotations. These annotations allow you to handle exceptions globally across all controllers or for a specific controller.

For example:

```java
@ControllerAdvice
public class CustomExceptionHandler {
 
    @ExceptionHandler(CustomException.class)
    public ResponseEntity<ErrorMessage> handleCustomException(CustomException ex) {
        ErrorMessage errorMessage = new ErrorMessage(ex.getMessage(), 400);
        return new ResponseEntity<>(errorMessage, HttpStatus.BAD_REQUEST);
    }
}
```

In this example, we're using the `@ExceptionHandler` annotation to handle the `CustomException`. The response entity is returned with a status code of 400.

### <span style='color: navy'>POST Using Fields with `@FormParam`</span>

<span style='font-size: 25px'>What is it? 🤔</span>

`@FormParam` is an annotation that you can use to access individual entries posted by an HTML form document. It is part of the JAX-RS API, which is a standard for building RESTful web services in Java. [JAX-RS stands for Java API for RESTful Web Services.

<span style='font-size: 25px'>Why to use it? 🙌</span>

You can use `@FormParam` to inject the values of HTML form parameters into your Java method parameters. This way, you can easily process the user input and perform some actions on the server side. For example, you can use `@FormParam` to create a new customer, update a product, or send an email.

<span style='font-size: 25px'>When to use it? 🕒</span>

You can use `@FormParam` when you have an HTML form that submits data using the `POST` method and the `application/x-www-form-urlencoded` content type. This is the default content type for HTML forms. You can also use `@FormParam` with other content types, such as `application/json`, but you need to specify the `@Consumes` annotation on your method.

<span style='font-size: 25px'>How to use it? 🚀</span>

To use `@FormParam`, you need to do the following steps:

1. Create an HTML form that has some input fields with names and values. For example:

```html
<FORM action="http://example.com/customers" method="post">
  <P>
    First name: <INPUT type="text" name="firstname"><BR>
    Last name: <INPUT type="text" name="lastname"><BR>
    <INPUT type="submit" value="Send">
  </P>
</FORM>
```

2. Create a JAX-RS resource class that has a method annotated with `@POST` and `@Path`. The method should have parameters annotated with `@FormParam` that match the names of the input fields in the HTML form. For example:

```java
@Path("/customers")
public class CustomerResource {

  @POST
  public void createCustomer(@FormParam("firstname") String first,
                             @FormParam("lastname") String last) {
    // do something with first and last
  }
}
```

3. Deploy your JAX-RS resource class on a web server that supports JAX-RS, such as GlassFish, WildFly, or Tomcat.

4. Test your HTML form and JAX-RS resource by submitting some data and checking the output on the server side.

<span style='font-size: 25px'>Comparison with Spring Boot Framework 🌸</span>

Spring Boot is another popular framework for building web applications in Java. It also supports RESTful web services and HTML forms. However, there are some differences between Spring Boot and JAX-RS when it comes to using `@FormParam`.

- In Spring Boot, you don’t need to use `@FormParam` explicitly. You can use `@RequestParam` instead, which works for both query parameters and form parameters. For example:

```java
@PostMapping("/customers")
public void createCustomer(@RequestParam("firstname") String first,
                           @RequestParam("lastname") String last) {
  // do something with first and last
}
```

### <span style='color: navy'>POST Using Fields with `@BeanParam`</span>

<span style='font-size: 25px'>What is it? 🤔</span>

`@BeanParam` is an annotation that you can use to inject a Java bean class whose properties or fields are annotated with any of the injection parameters supported by JAX-RS. It is part of the JAX-RS 2.0 specification, which is a standard for building RESTful web services in Java. JAX-RS stands for Java API for RESTful Web Services[1](https://stackoverflow.com/questions/53428129/jax-rs-mapping-json-input-to-a-bean-using-beanparam-doesnt-work).

<span style='font-size: 25px'>Why to use it? 🙌</span>

You can use `@BeanParam` to simplify your web service method parameters by grouping them into a single bean class. This way, you can avoid repeating the same annotations for each parameter and make your code more readable and maintainable. For example, you can use `@BeanParam` to group `@FormParam`, `@PathParam`, `@QueryParam`, and `@HeaderParam` annotations into one bean class.

<span style='font-size: 25px'>When to use it? 🕒</span>

You can use `@BeanParam` when you have multiple injection parameters that belong to the same logical entity or concept. For example, you can use `@BeanParam` to represent a customer, a product, or an order. You can also use `@BeanParam` when you want to perform validation on your injection parameters using `@Valid` or `@Validated` annotations.

<span style='font-size: 25px'>How to use it? 🚀</span>

To use `@BeanParam`, you need to do the following steps:

1. Create a Java bean class that has properties or fields annotated with any of the injection parameters supported by JAX-RS. For example:

```java
public class Customer {
  @FormParam("firstname")
  private String firstname;

  @FormParam("lastname")
  private String lastname;

  @PathParam("id")
  private Long id;

  @HeaderParam("User-Agent")
  private String userAgent;

  // getters and setters
}
```

2. Create a JAX-RS resource class that has a method annotated with `@POST` and `@Path`. The method should have a parameter annotated with `@BeanParam` that matches the type of your bean class. For example:

```java
@Path("/customers")
public class CustomerResource {

  @POST
  public void createCustomer(@Valid @BeanParam Customer customer) {
    // do something with customer
  }
}
```

3. Deploy your JAX-RS resource class on a web server that supports JAX-RS, such as GlassFish, WildFly, or Tomcat.

4. Test your web service method by sending a POST request with the appropriate form parameters, path parameters, and header parameters.

<span style='font-size: 25px'>Comparison with Spring Boot Framework 🌸</span>

Spring Boot is another popular framework for building web applications in Java. It also supports RESTful web services and injection parameters. However, there are some differences between Spring Boot and JAX-RS when it comes to using `@BeanParam`.

- In Spring Boot, you don’t need to use `@BeanParam` explicitly. You can use `@ModelAttribute` instead, which works for both form parameters and request bodies[2](https://stackoverflow.com/questions/37121529/how-to-post-a-list-of-object-using-jax-rs-to-perform-bean-validation). For example:

```java
public class Customer {
  private String firstname;
  private String lastname;
  // getters and setters
}

@PostMapping("/customers")
public void createCustomer(@ModelAttribute Customer customer) {
  // do something with customer
}
```

### <span style='color: navy'>`@Context` in JAX-RS</span>

<span style='font-size: 25px'>What is it? 🤔</span>

`@Context` is an annotation that allows you to inject various objects related to the context of the current HTTP request into your JAX-RS resource classes or providers. It behaves similarly to `@Inject` or `@Autowired` annotations in Java EE or Spring frameworks, but it is specific to JAX-RS.

<span style='font-size: 25px'>Why to use it? 🙌</span>

You may want to use `@Context` when you need some information or functionality from the JAX-RS runtime, such as the security context, the request headers, the URI details, the application configuration, etc. These objects can help you to implement your business logic, validate the request, customize the response, or access other resources.

<span style='font-size: 25px'>When to use it? 🕒</span>

You can use `@Context` whenever you have a JAX-RS resource class or provider that needs to access the context objects. You can annotate a field, a property, a constructor parameter, or a method parameter with `@Context` and specify the type of the object you want to inject. For example:

```java
@Path("/")
public class TestResource {

  // Annotate a field with @Context and specify the type
  @Context
  private UriInfo uriInfo;

  // Annotate a property with @Context and specify the type
  private SecurityContext securityContext;

  @Context
  public void setSecurityContext(SecurityContext securityContext) {
    this.securityContext = securityContext;
  }

  // Annotate a constructor parameter with @Context and specify the type
  public TestResource(@Context HttpHeaders httpHeaders) {
    // Do something with httpHeaders
  }

  // Annotate a method parameter with @Context and specify the type
  @GET
  @Path("/test")
  public Response test(@Context Request request) {
    // Do something with request
    return Response.ok().build();
  }
}
```

<span style='font-size: 25px'>How to use it? 🚀</span>

To use `@Context`, you need to follow some rules:

- The type of the annotated element must be one of the following:
  - `SecurityContext` – Security context instance for the current HTTP request
  - `Request` – Used for setting precondition request processing
  - `Application`, `Configuration`, and `Providers` – Provide access to the JAX-RS application, configuration, and providers instances
  - `ResourceContext` – Resource context class instances
  - `ServletConfig` – The ServletConfig instance instance
  - `ServletContext` – The ServletContext instance
  - `HttpServletRequest` – The HttpServletRequest instance for the current request
  - `HttpServletResponse` – The HttpServletResponse instance for the current request
  - `HttpHeaders` – Maintains the HTTP header keys and values
  - `UriInfo` – Query parameters and path variables from the URI called
- You can use `@Context` on any valid resource class field or property. [Access to these fields or properties is not thread-safe.
- You can use `@Context` on resource class constructor parameters or method parameters. These parameters are thread-safe.
- You cannot use `@Context` on resource class constructors or methods.

<span style='font-size: 25px'>A real world example 😎</span>

Let’s say you have a RESTful web service that allows users to get some information about their account. You want to check if the user is authenticated and authorized before returning the information. You also want to return a link to their profile page in the response. You can use `@Context` to inject the `SecurityContext` and `UriInfo` objects into your resource class. For example:

```java
@Path("/account")
public class AccountResource {

  // Inject SecurityContext into a field
  @Context
  private SecurityContext securityContext;

  // Inject UriInfo into a method parameter
  @GET
  @Produces(MediaType.APPLICATION_JSON)
  public Response getAccountInfo(@Context UriInfo uriInfo) {
    // Check if the user is authenticated
    if (securityContext.getUserPrincipal() == null) {
      // Return an unauthorized response with an error message
      return Response.status(Response.Status.UNAUTHORIZED)
                     .entity("You need to log in first.")
                     .build();
    }

    // Check if the user has a valid role
    if (!securityContext.isUserInRole("USER")) {
      // Return a forbidden
        
      // Check if the user has a valid role
    if (!securityContext.isUserInRole("USER")) {
      // Return a forbidden response with an error message
      return Response.status(Response.Status.FORBIDDEN)
                     .entity("You are not authorized to access this resource.")
                     .build();
    }

    // Otherwise, get the user name from the security context
    String userName = securityContext.getUserPrincipal().getName();

    // Get some information about the user from the storage
    // ...
    String email = ...;
    String phone = ...;

    // Create a link to the user profile page using the uri info
    URI profileUri = uriInfo.getBaseUriBuilder()
                            .path("/profile")
                            .path(userName)
                            .build();

    // Create a JSON object to return the information and the link
    JsonObject json = Json.createObjectBuilder()
                          .add("userName", userName)
                          .add("email", email)
                          .add("phone", phone)
                          .add("profile", profileUri.toString())
                          .build();

    // Return a success response with the JSON object
    return Response.ok(json).build();
  }
}
```

<span style='font-size: 25px'>Comparing it with its alternative in Spring Boot framework 🤓</span>

In Spring Boot framework, you can use the `@Autowired` annotation to inject various objects related to the context of the current HTTP request into your controller classes. It is similar to `@Context` in JAX-RS, but it has some differences:

- The type of the annotated element can be any type that is supported by the Spring dependency injection mechanism. This includes most built-in types such as `HttpServletRequest`, `HttpServletResponse`, `HttpSession`, etc., as well as custom types that have a registered bean.
- You can use `@Autowired` on any valid controller class field, property, constructor, or method. Access to these elements is thread-safe.
- You can use the `required` attribute to specify whether the dependency is required or not. If it is required and the dependency is not available, a `NoSuchBeanDefinitionException` will be thrown. You can also use the `@Qualifier` annotation to specify a qualifier for the dependency if there are multiple candidates.

For example:

```java
@RestController
@RequestMapping("/account")
public class AccountController {

  // Inject HttpServletRequest into a field
  @Autowired
  private HttpServletRequest request;

  // Inject HttpServletResponse into a constructor parameter
  private HttpServletResponse response;

  public AccountController(@Autowired HttpServletResponse response) {
    this.response = response;
  }

  // Inject HttpSession into a method parameter
  @GetMapping(produces = MediaType.APPLICATION_JSON_VALUE)
  public ResponseEntity<JsonObject> getAccountInfo(@Autowired HttpSession session) {
    // Do something with request, response, and session
    // ...

    // Return a success response with a JSON object
    return ResponseEntity.ok(json);
  }
}
```

### <span style='color:navy'>`@HeaderParam`</span>

<span style='font-size: 25px'>What is it? 🤔</span>

`@HeaderParam` is an annotation that binds the value(s) of a HTTP header to a resource method parameter, resource class field, or resource class bean property. It allows you to access the request headers and use them in your code.

<span style='font-size: 25px'>Why to use it? 🙌</span>

You may want to use `@HeaderParam` when you need some information from the request headers, such as the content type, the host name, the user agent, the authorization token, etc. These information can be useful for validating the request, customizing the response, or performing some logic based on the header values.

<span style='font-size: 25px'>When to use it? 🕒</span>

You can use `@HeaderParam` whenever you have a JAX-RS resource class that needs to access the request headers. You can annotate a method parameter, a field, or a bean property with `@HeaderParam` and specify the name of the header you want to bind. For example:

```java
@Path("/")
public class TestResource {

  // Annotate a method parameter
  @GET
  @Path("/test1")
  public String test1(@HeaderParam(HttpHeaders.HOST) String host) {
    return "The request 'host' header value = " + host;
  }

  // Annotate a field
  @HeaderParam("user-agent")
  private String userAgent;

  @GET
  @Path("/test2")
  public String test2() {
    return "The request 'user-agent' header value = " + userAgent;
  }

  // Annotate a bean property
  private String contentType;

  @HeaderParam("content-type")
  public void setContentType(String contentType) {
    this.contentType = contentType;
  }

  @GET
  @Path("/test3")
  public String test3() {
    return "The request 'content-type' header value = " + contentType;
  }
}
```

<span style='font-size: 25px'>How to use it? 🚀</span>

To use `@HeaderParam`, you need to follow some rules:

- The type of the annotated parameter, field or property must either:
  - Be a primitive type
  - Have a constructor that accepts a single String argument
  - Have a static method named `valueOf` or `fromString` that accepts a single String argument (see, for example, `Integer.valueOf(String)`)
  - Have a registered implementation of `ParamConverterProvider` JAX-RS extension SPI that returns a `ParamConverter` instance capable of a “from string” conversion for the type.
  - Be `List<T>`, `Set<T>` or `SortedSet<T>`, where T satisfies one of the above conditions. The resulting collection is read-only.
- If the type is not one of the collection types listed above and the header parameter is represented by multiple values then the first value (lexically) of the parameter is used.
- Because injection occurs at object creation time, use of this annotation on resource class fields and bean properties is only supported for the default per-request resource class lifecycle. Resource classes using other lifecycles should only use this annotation on resource method parameters.

<span style='font-size: 25px'>A real world example 😎</span>

Let’s say you have a RESTful web service that allows users to upload files. You want to check the size of the file before uploading it to avoid wasting bandwidth and storage. You can use `@HeaderParam` to get the value of the `Content-Length` header and compare it with a maximum limit. For example:

```java
@Path("/upload")
public class UploadResource {

  // Define a maximum file size limit
  private static final long MAX_FILE_SIZE = 10 * 1024 * 1024; // 10 MB

  // Annotate a method parameter with @HeaderParam and specify the header name
  @POST
  @Consumes(MediaType.APPLICATION_OCTET_STREAM)
  public Response uploadFile(@HeaderParam("Content-Length") long fileSize,
                             InputStream fileInputStream) {
    // Check if the file size exceeds the limit
    if (fileSize > MAX_FILE_SIZE) {
      // Return a bad request response with an error message
      return Response.status(Response.Status.BAD_REQUEST)
                     .entity("File size too large. Maximum allowed is " + MAX_FILE_SIZE + " bytes.")
                     .build();
    }

    // Otherwise, proceed with uploading the file
    // ...
      
    // Return a success response with a message
    return Response.status(Response.Status.OK)
                   .entity("File uploaded successfully.")
                   .build();
  }
}
```

<span style='font-size: 25px'>Comparing it with its alternative in Spring Boot framework 🤓</span>

In Spring Boot framework, you can use the `@RequestHeader` annotation to bind the value(s) of a HTTP header to a method parameter. It is similar to `@HeaderParam` in JAX-RS, but it has some differences:

- The type of the annotated parameter can be any type that is supported by the `HttpMessageConverter` mechanism. This includes most built-in types such as `String`, `int`, `boolean`, etc., as well as custom types that have a registered converter.
- If the type is not one of the collection types and the header parameter is represented by multiple values then all values are concatenated together with a comma as the delimiter.
- You can use the `required` attribute to specify whether the header is required or not. If it is required and the header is missing, a `MissingRequestHeaderException` will be thrown. You can also use the `defaultValue` attribute to specify a default value for the header if it is not present or empty.
- You can use the `@RequestHeader` annotation on controller class fields and bean properties as well, regardless of the resource class lifecycle. However, this is not recommended as it introduces hidden dependencies and makes testing more difficult.

For example:

```java
@RestController
@RequestMapping("/upload")
public class UploadController {

  // Define a maximum file size limit
  private static final long MAX_FILE_SIZE = 10 * 1024 * 1024; // 10 MB

  // Annotate a method parameter with @RequestHeader and specify the header name
  @PostMapping(consumes = MediaType.APPLICATION_OCTET_STREAM_VALUE)
  public ResponseEntity<String> uploadFile(@RequestHeader("Content-Length") long fileSize,
                                           InputStream fileInputStream) {
    // Check if the file size exceeds the limit
    if (fileSize > MAX_FILE_SIZE) {
      // Return a bad request response with an error message
      return ResponseEntity.badRequest()
                           .body("File size too large. Maximum allowed is " + MAX_FILE_SIZE + " bytes.");
    }

    // Otherwise, proceed with uploading the file
    // ...

    // Return a success response with a message
    return ResponseEntity.ok()
                         .body("File uploaded successfully.");
  }
}
```

## <span style='color: #8b0000'>Done</span>

- [x] Day 34/90 ===> [JavaEE: Day 34/90 - JAX-RS (Java API for RESTful Web Services)](https://www.linkedin.com/pulse/javaee-day-3490-jax-rs-java-api-restful-web-services-seyed-ali)

# <span style='color: olive'>Day 35/90</span>

Date : 16-May-2023



## <span style='color: grey'>JAX-RS : Java API for RESTful Web Services (continue)</span>

### <span style='color:navy'>Caching</span>

Caching is a technique that allows you to store and reuse data that does not change frequently or that is expensive to generate or fetch. Caching can improve the performance, scalability and reliability of your web services by reducing the load on your server and network. 🙌

In this note, I will explain what caching is, why you should use it, when you should use it and how you can use it with JavaEE JAX-RS. I will also give you a real world example and compare it with an alternative approach in Spring Boot framework. 📝

<span style='font-size: 25px'>What is it? 🤔</span>

Caching is the process of storing data in a temporary storage area called a cache. A cache can be located on the client side (such as a browser or an app), on the server side (such as a web server or an application server) or on an intermediate layer (such as a proxy server or a content delivery network). 🌐

The main purpose of caching is to avoid unnecessary or redundant requests to the original data source, which can be slow, costly or unavailable. By using a cache, you can serve data faster, save bandwidth and resources, and enhance the user experience. 😊

There are different types of caching, such as:

- **Time-based caching**: This type of caching uses a predefined expiration time to determine how long the data can be stored in the cache. After the expiration time, the cache is considered stale and needs to be refreshed from the original source. ⏰
- **Validation-based caching**: This type of caching uses a unique identifier (such as an ETag) to check if the data in the cache is still valid or has changed since the last request. If the data has not changed, the cache can be reused without fetching the data again. If the data has changed, the cache needs to be updated with the new data. 🔖
- **Invalidation-based caching**: This type of caching uses a mechanism (such as an event or a message) to notify the cache when the data has changed and needs to be invalidated. The cache then deletes or updates the data accordingly. 📢

<span style='font-size: 25px'>Why to use it? 🙌</span>

You may want to use caching when you have a web service that returns data or responses that are:

- Static or rarely changed
- Not sensitive or confidential
- Not dependent on user-specific parameters
- Not affected by side effects

Caching can improve the performance and scalability of your web service by:

- Avoiding unnecessary computations or database queries
- Reducing the bandwidth consumption and network latency
- Increasing the availability and reliability of your web service

Caching can provide many benefits for your web services, such as:

- **Performance**: Caching can reduce the response time and latency of your web services by serving data from a nearby or local cache instead of making a round trip to the original source. This can make your web services faster and more responsive for your users. ⚡
- **Scalability**: Caching can reduce the load and traffic on your server and network by avoiding unnecessary or redundant requests to the original source. This can help you handle more concurrent users and requests without degrading the quality of service. 📈
- **Reliability**: Caching can improve the availability and resilience of your web services by serving data from a cache even if the original source is down or unreachable. This can prevent errors and failures and ensure continuity of service for your users. 💯

<span style='font-size: 25px'>When to use it? 🕒</span>

You can use caching whenever you have a JAX-RS resource method that returns data or responses that meet the criteria mentioned above. You can use the `CacheControl` class and the `Response` class to set the cache headers and control the cache behavior of your web service. For example:

```java
@Path("/books")
public class BookResource {

  // A method that returns a list of books
  @GET
  @Produces(MediaType.APPLICATION_JSON)
  public Response getBooks() {
    // Get the books from the storage
    // ...
    List<Book> books = ...;

    // Create a CacheControl instance
    CacheControl cc = new CacheControl();

    // Set the max-age directive to 60 seconds
    // This means the response can be cached for 60 seconds
    cc.setMaxAge(60);

    // Set the must-revalidate directive to true
    // This means the cached response must be revalidated by the server before using it
    cc.setMustRevalidate(true);

    // Create a Response instance with the books and the cache control
    Response response = Response.ok(books)
                                .cacheControl(cc)
                                .build();

    // Return the response
    return response;
  }
}
```

<span style='font-size: 25px'>How to use it? 🚀</span>

To use caching in JAX-RS, you need to follow some rules:

<span style='font-style: italic'>CacheControl 💾</span>

CacheControl is a class that represents the `Cache-Control` HTTP header, which specifies the caching directives for the client and/or the proxy caches. You can use this class to set various properties of the cache, such as:

- `maxAge`: The maximum number of seconds that the cache can store the data.
- `sMaxAge`: The maximum number of seconds that the shared (proxy) cache can store the data.
- `noCache`: A flag that indicates that the cache must not store the data.
- `noStore`: A flag that indicates that the cache must not store any part of the request or response.
- `noTransform`: A flag that indicates that the cache must not modify the data in any way.
- `mustRevalidate`: A flag that indicates that the cache must revalidate the data with the original source before using it.
- `proxyRevalidate`: A flag that indicates that the shared (proxy) cache must revalidate the data with the original source before using it.
- `private`: A flag that indicates that the cache is private and only for the use of a single user.
- `public`: A flag that indicates that the cache is public and can be shared by multiple users.

You can create an instance of CacheControl and set its properties using its methods or its constructor. For example:

```java
// Create a CacheControl instance with a maxAge of 60 seconds
CacheControl cc = new CacheControl();
cc.setMaxAge(60);

// Create a CacheControl instance with a noCache directive
CacheControl cc = new CacheControl("no-cache");
```

You can use the CacheControl instance to set the `Cache-Control` header of your response using the `cacheControl()` method of the ResponseBuilder class. For example:

```java
// Create a ResponseBuilder instance
ResponseBuilder builder = Response.ok(data);

// Set the Cache-Control header using the CacheControl instance
builder.cacheControl(cc);

// Build and return the response
return builder.build();
```

<span style='font-size: 25px'>ETag 🏷️</span>

ETag is a class that represents the `ETag` HTTP header, which specifies a unique identifier for a version of a resource. You can use this class to implement validation-based caching, where you check if the data in the cache is still valid or has changed since the last request.🧐

You can create an instance of ETag using its constructor, which takes a string value as an argument. The string value should be a unique and representation-specific identifier for your resource, such as a hash code or a version number. For example:

```java
// Create an ETag instance with a hash code value
ETag etag = new ETag(Integer.toString(data.hashCode()));

// Create an ETag instance with a version number value
ETag etag = new ETag(data.getVersion());

// Create an ETag instance with a UUID
ETag etag = new ETag(UUID.randomUUID().toString());
```

You can use the ETag instance to set the `ETag` header of your response using the `tag()` method of the ResponseBuilder class. For example:

```java
// Create a ResponseBuilder instance
ResponseBuilder builder = Response.ok(data);

// Set the ETag header using the ETag instance
builder.tag(etag);

// Build and return the response
return builder.build();
```

You can also use the ETag instance to check if your resource has changed or not by comparing it with the `If-None-Match` header of your request. You can use the `evaluatePreconditions()` method of the Request class, which takes an ETag argument and returns a ResponseBuilder instance. If the ETag matches with the `If-None-Match` header, it means that your resource has not changed and you can return a 304 Not Modified response without sending any data. If the ETag does not match, it means that your resource has changed and you need to send your data with a 200 OK response. For example:

```java
// Get the Request instance from your context
Request request = context.getRequest();

// Evaluate the preconditions using the ETag instance 
ResponseBuilder builder = request.evaluatePreconditions(etag);

// If the builder is not null, it means that the ETag matches and the resource has not changed 
if (builder != null) { 
  // Return a 304 Not Modified response without any data return 
    builder.build()
}
// If the builder is null, it means that the ETag does not match and the resource has change
else { 
    // Return a 200 OK response with your data return 
    Response.ok(data).tag(etag).build(); 
}
```

<span style='font-size: 25px'>A real world example 1😎</span>

Let’s say you have a RESTful web service that allows users to get some information about a book by its ISBN. You want to cache the book information for one hour, unless it has been modified in the storage. You also want to support conditional requests based on ETags. You can use caching in JAX-RS to implement this scenario. For example:

```java
@Path("/books/{isbn}")
public class BookResource {

  // A method that returns a book by its ISBN
  @GET
  @Produces(MediaType.APPLICATION_JSON)
  public Response getBook(@PathParam("isbn") String isbn, @Context Request request) {
    // Get the book from the storage by its ISBN
    // ...
    Book book = ...;

    // Check if the book exists
    if (book == null) {
      // Return a not found response with an error message
      return Response.status(Response.Status.NOT_FOUND)
                     .entity("Book not found.")
                     .build();
    }

    // Create an ETag based on the book's hash code
      EntityTag etag = new EntityTag(Integer.toString(book.hashCode()));

    // Create a CacheControl instance
    CacheControl cc = new CacheControl();

    // Set the max-age directive to 3600 seconds (one hour)
    // This means the response can be cached for one hour
    cc.setMaxAge(3600);

    // Evaluate the preconditions based on the ETag
    ResponseBuilder builder = request.evaluatePreconditions(etag);

    // If the preconditions are met, return a not modified response
    if (builder != null) {
      return builder.cacheControl(cc).build();
    }

    // Otherwise, return an ok response with the book and the cache control
    return Response.ok(book)
                   .cacheControl(cc)
                   .tag(etag)
                   .build();
  }
}
```

Let's see another one:

<span style='font-size: 25px'>A real world example 2🌎</span>

Let's say you have a web service that provides information about books, such as title, author, genre, price, etc. You want to use caching to improve the performance and scalability of your web service, as well as to enhance the user experience. You also want to use validation-based caching to ensure that your data is always fresh and consistent. 📚

Here is how you can implement caching with CacheControl and ETag in JavaEE JAX-RS:

Book

```java
public class Book {

    private String id; // A unique identifier for the book
    private String title; // The title of the book
    private String author; // The author of the book
    private String genre; // The genre of the book
    private double price; // The price of the book

    // Getters and setters for the fields

    // A method that returns a hash code for the book based on its fields
    public int getHashCode() {
        return Objects.hash(id, title, author, genre, price);
    }
}
```

BookService

```java
// A class that represents a web service for books
@Path("/books")
public class BookService {

    // A method that returns a list of all books
    @GET
    @Produces(MediaType.APPLICATION_JSON)
    public Response getAllBooks(@Context Request request) {

        // Get all books from a database or another source
        List<Book> books = getBooksFromDB();

        // Create a CacheControl instance with a maxAge of one hour
        CacheControl cc = new CacheControl();
        cc.setMaxAge(3600);

        // Create an ETag instance with a hash code value based on the list of books
        ETag etag = new ETag(Integer.toString(books.hashCode()));

        // Evaluate the preconditions using the ETag instance
        ResponseBuilder builder = request.evaluatePreconditions(etag);

        // If the builder is not null, it means that the ETag matches and the list of books has not changed
        if (builder != null) {
            // Return a 304 Not Modified response without any data
            return builder.build();
        }

        // If the builder is null, it means that the ETag does not match and the list of books has changed
        else {
            // Return a 200 OK response with the list of books
            return Response.ok(books).cacheControl(cc).tag(etag).build();
        }
    }

    // A method that returns a book by its id
    @GET
    @Path("/{id}")
    @Produces(MediaType.APPLICATION_JSON)
    public Response getBookById(@PathParam("id") String id, @Context Request request) {

        // Get a book by its id from a database or another source
        Book book = getBookFromDB(id);

        // Check if the book exists or not
        if (book == null) {
            // Return a 404 Not Found response if the book does not exist
            return Response.status(Status.NOT_FOUND).build();
        } else {
            // Create a CacheControl instance with a maxAge of one hour
            CacheControl cc = new CacheControl();
            cc.setMaxAge(3600);

            // Create an ETag instance with a hash code value based on the book fields
            ETag etag = new ETag(Integer.toString(book.getHashCode()));

            // Evaluate the preconditions using the ETag instance
            ResponseBuilder builder = request.evaluatePreconditions(etag);

            // If the builder is not null, it means that the ETag matches and the book has not changed
            if (builder != null) {
                // Return a 304 Not Modified response without any data
                return builder.build();
            }

            // If the builder is null, it means that the ETag does not match and the book has changed
            else {
                // Return a 200 OK response with the book
                return Response.ok(book).cacheControl(cc).tag(etag).build();
            }
        }
    }

    // A method that returns a book by its title
    @GET
    @Path(“/title/{title}”)
    @Produces(MediaType.APPLICATION_JSON)
    public Response getBookByTitle(@PathParam(“title”) String title, @Context Request request) {
        // Get a book by its title from a database or another source
        Book book = getBookFromDBByTitle(title);

// Check if the book exists or not
        if (book == null) {
            // Return a 404 Not Found response if the book does not exist
            return Response.status(Status.NOT_FOUND).build();
        } else {
            // Create a CacheControl instance with a maxAge of one hour
            CacheControl cc = new CacheControl();
            cc.setMaxAge(3600);

            // Create an ETag instance with a hash code value based on the book fields
            ETag etag = new ETag(Integer.toString(book.getHashCode()));

            // Evaluate the preconditions using the ETag instance
            ResponseBuilder builder = request.evaluatePreconditions(etag);

            // If the builder is not null, it means that the ETag matches and the book has not changed
            if (builder != null) {
                // Return a 304 Not Modified response without any data
                return builder.build();
            }

            // If the builder is null, it means that the ETag does not match and the book has changed
            else {
                // Return a 200 OK response with the book
                return Response.ok(book).cacheControl(cc).tag(etag).build();
            }
        }
    }
}
```

<span style='font-size: 25px'>A real world example 3🗺️</span>

In this example, I have written a Java method that implements a REST endpoint for retrieving an employee by their ID. The endpoint is mapped to the URL path `/api/v1/getEmployeeById/the_id`, where `the_id` is the ID of the employee that is being requested.

To handle HTTP GET requests, I used the `@GET` annotation. I also used the `@Path` annotation to specify the endpoint's path, with curly braces indicating a path parameter named `id` that should be a positive integer.

To retrieve the employee by their ID, I called the `findById` method of an `employeeService` object. This object is an instance of a class that provides business logic for managing employees.

I then created an `EntityTag` object with a unique identifier generated by `UUID.randomUUID()`. This `EntityTag` is used to implement cache validation, which allows the client to check if the response has changed since it was last retrieved and avoid unnecessary network traffic.

To specify that the response can be cached for a maximum of 60 seconds, I set the `CacheControl` header. I then used `request.evaluatePreconditions(entityTag)` to check if the client's cached response is still valid. If the cached response is valid, I constructed a new response with the cached response headers and returned it.

If the cached response is not valid, I constructed a new response with the employee object as the entity, set the `CacheControl` and `EntityTag` headers, and returned it.

It's important to note that if I were to remove the `Response.ok(employee)` line, a `NullPointerException` would be thrown when the `entity` method is called because the entity would be null.

```java
@GET // find employee by its id - /api/v1/getEmployeeById/the_id
    @Path("/getEmployeeById/{id : [0-9]+}")
    public Response findEmployeeByIdPathParam(@PathParam("id") @DefaultValue("0") Long id, @Context Request request) {
        Employee employee = employeeService.findById(id);

        EntityTag entityTag = new EntityTag(UUID.randomUUID().toString());

        CacheControl cacheControl = new CacheControl();
        cacheControl.setMaxAge(60); //60 seconds

        Response.ResponseBuilder responseBuilder = request.evaluatePreconditions(entityTag);

        if (responseBuilder != null) {
            responseBuilder.cacheControl(cacheControl);
            return responseBuilder.build();
        }
        responseBuilder = Response.ok(employee); // <-- this line is important, otherwise we'll be getting a NullPointerException
        return responseBuilder
                .entity(employee)
                .tag(entityTag)
                .cacheControl(cacheControl)
                .build();
    }
```

<span style='font-size: 25px'>Comparing it with its alternative in Spring Boot framework 🤓</span>

Spring Boot supports HTTP caching by providing annotations and classes to work with HTTP cache headers, such as `@Cacheable`, `@CacheEvict`, `@CachePut`, `CacheManager`, `Cache`, etc. 🌟

<span style='font-style: italic'>@Cacheable 💾</span>

`@Cacheable` is an annotation that marks a method as cacheable, meaning that its return value will be stored in a cache and reused for subsequent invocations with the same arguments. You can use this annotation to implement time-based caching for your web services. ⏰

You can specify various attributes for this annotation, such as:

- `value`: The name of the cache where the return value will be stored.
- `key`: The key under which the return value will be stored in the cache.
- `condition`: A SpEL expression that determines whether the method is cacheable or not.
- `unless`: A SpEL expression that determines whether to prevent caching or not.
- `sync`: A flag that indicates whether to lock the method execution for concurrent

invocations with the same arguments.

You can use the `@Cacheable` annotation on your web service methods to cache their return values in a cache. 

For example:

```java
// A class that represents a web service for books
@RestController
@RequestMapping("/books")
public class BookService {

  // A method that returns a list of all books
  @GetMapping
  @Cacheable(value = "books")
  public List<Book> getAllBooks() {

    // Get all books from a database or another source
    List<Book> books = getBooksFromDB();

    // Return the list of books
    return books;
  }

  // A method that returns a book by its id
  @GetMapping("/{id}")
  @Cacheable(value = "books", key = "#id")
  public Book getBookById(@PathVariable String id) {

    // Get a book by its id from a database or another source
    Book book = getBookFromDB(id);

    // Check if the book exists or not
    if (book == null) {
      // Throw an exception if the book does not exist
      throw new BookNotFoundException(id);
    }
    else {
      // Return the book
      return book;
    }
  }

  // A method that returns a book by its title
  @GetMapping("/title/{title}")
  @Cacheable(value = "books", key = "#title")
  public Book getBookByTitle(@PathVariable String title) {

    // Get a book by its title from a database or another source
    Book book = getBookFromDBByTitle(title);

    // Check if the book exists or not
    if (book == null) {
      // Throw an exception if the book does not exist
      throw new BookNotFoundException(title);
    }
    else {
      // Return the book
      return book;
    }
  }
}
```

<span style='font-style: italic'>@CacheEvict 🗑️</span>

`@CacheEvict` is an annotation that marks a method as cache evicting, meaning that its execution will trigger the removal of one or more entries from the cache. You can use this annotation to implement invalidation-based caching for your web services. 📢

You can specify various attributes for this annotation, such as:

- `value`: The name of the cache where the entries will be removed.
- `key`: The key of the entry that will be removed from the cache.
- `condition`: A SpEL expression that determines whether the method is cache evicting or not.
- `allEntries`: A flag that indicates whether to remove all entries from the cache or not.
- `beforeInvocation`: A flag that indicates whether to remove the entries before or after the method execution.

You can use the `@CacheEvict` annotation on your web service methods to remove entries from the cache when they create, update or delete data. For example:

```java
// A class that represents a web service for books
@RestController
@RequestMapping("/books")
public class BookService {

  // A method that creates or updates a book
  @PostMapping
  @CacheEvict(value = "books", allEntries = true)
  public ResponseEntity<?> createOrUpdateBook(@RequestBody Book book) {

    // Create or update a book in a database or another source
    boolean created = createOrUpdateBookInDB(book);

    // Check if the book was created or updated successfully or not
    if (created) {
      // Return a 201 Created response if the book was created successfully
      return ResponseEntity.status(HttpStatus.CREATED).build();
    }
    else {
      // Return a 200 OK response if the book was updated successfully
      return ResponseEntity.status(HttpStatus.OK).build();
    }
  }

  // A method that deletes a book by its id
  @DeleteMapping("/{id}")
  @CacheEvict(value = "books", key = "#id")
  public ResponseEntity<?> deleteBookById(@PathVariable String id) {

    // Delete a book by its id from a database or another source
    boolean deleted = deleteBookFromDB(id);

    // Check if the book was deleted successfully or not
    if (deleted) {
      // Return a 204 No Content response if the book was deleted successfully
      return ResponseEntity.status(HttpStatus.NO_CONTENT).build();
    }
    else {
      // Throw an exception if the book does not exist
      throw new BookNotFoundException(id);
    }
  }
}
```

<span style='font-style: italic'>@CachePut 🔄</span>

`@CachePut` is an annotation that marks a method as cache updating, meaning that its execution will update the cache with the new or modified value. You can use this annotation to implement validation-based caching for your web services. 🔖

You can specify various attributes for this annotation, such as:

- `value`: The name of the cache where the value will be updated.
- `key`: The key under which the value will be updated in the cache.
- `condition`: A SpEL expression that determines whether the method is cache updating or not.
- `unless`: A SpEL expression that determines whether to prevent caching or not.

You can use the `@CachePut` annotation on your web service methods to update the cache with the new or modified value when they create, update or delete data. For example:

```java
// A class that represents a web service for books
@RestController
@RequestMapping("/books")
public class BookService {

  // A method that creates or updates a book
  @PostMapping
  @CachePut(value = "books", key = "#book.id")
  public Book createOrUpdateBook(@RequestBody Book book) {

    // Create or update a book in a database or another source
    boolean created = createOrUpdateBookInDB(book);

    // Check if the book was created or updated successfully or not
    if (created) {
      // Return the book with a 201 Created status
      return ResponseEntity.status(HttpStatus.CREATED).body(book);
    }
    else {
      // Return the book with a 200 OK status
      return ResponseEntity.status(HttpStatus.OK).body(book);
    }
  }

  // A method that deletes a book by its id
  @DeleteMapping("/{id}")
  @CacheEvict(value = "books", key = "#id")
  public ResponseEntity<?> deleteBookById(@PathVariable String id) {

    // Delete a book by its id from a database or another source
    boolean deleted = deleteBookFromDB(id);

    // Check if the book was deleted successfully or not
    if (deleted) {
      // Return a 204 No Content response if the book was deleted successfully
      return ResponseEntity.status(HttpStatus.NO_CONTENT).build();
    }
    else {
      // Throw an exception if the book does not exist
      throw new BookNotFoundException(id);
    }
  }
}
```

<span style='font-size: 25px'>Summary 📝</span>

In this note, I have explained what caching is, why you should use it, when you should use it and how you can use it with JavaEE JAX-RS. I have also given you a real world example and compared it with an alternative approach in Spring Boot framework. 🙌

Here are some key points to remember:

- Caching is a technique that allows you to store and reuse data that does not change frequently or that is expensive to generate or fetch.
- Caching can improve the performance, scalability and reliability of your web services by reducing the load on your server and network.
- Caching involves a trade-off between performance and freshness. You need to determine how fresh your data needs to be for your web services and your users.
- There are different types of caching, such as time-based caching, validation-based caching and invalidation-based caching.
- JavaEE JAX-RS supports HTTP caching by providing classes and methods to work with HTTP cache headers, such as CacheControl and ETag.
- Spring Boot supports HTTP caching by providing annotations and classes to work with HTTP cache headers, such as @Cacheable, @CacheEvict, @CachePut, CacheManager, Cache, etc.

50

### <span style='color: navy'>File Upload</span>

<span style='font-size: 25px'>📁 What is File Upload in JavaEE JAX-RS?</span>

File upload in JavaEE JAX-RS is a feature that allows users to upload files to a server using the JavaEE JAX-RS framework. JAX-RS (Java API for RESTful Web Services) is a set of APIs used to create RESTful web services in the Java programming language.

<span style='font-size: 25px'>🤔 Why to Use File Upload in JavaEE JAX-RS?</span>

File upload in JavaEE JAX-RS is useful when you want to allow users to upload files to your server through a RESTful web service. This can be helpful in a variety of scenarios, such as uploading images 📷, videos 🎥, PDFs 📄, or other types of files.

<span style='font-size: 25px'>⏰ When to Use File Upload in JavaEE JAX-RS?</span>

File upload in JavaEE JAX-RS can be used whenever you need to allow users to upload files to your server through a RESTful web service. Some common scenarios where you might need to use file upload include:

- Uploading profile pictures in a social media app 👥
- Uploading product images in an e-commerce app 🛍️
- Uploading documents in an online file storage app 🗂️

<span style='font-size: 25px'>🔧 How to Use File Upload in JavaEE JAX-RS?</span>

To use file upload in JavaEE JAX-RS, you'll need to follow these steps:

1. Create a JAX-RS resource class to handle the file upload request.
2. Use the `@FormDataParam` annotation to specify the file parameter in the resource method.
3. Use the `FormDataMultiPart` class to extract the file content from the request.
4. Save the file to a desired location on the server.

Here's an example of how to implement file upload in JavaEE JAX-RS:

```java
@POST
@Path("/upload")
@Consumes({MediaType.APPLICATION_OCTET_STREAM, "image/png", "image/jpeg", "image/jpg"})
@Produces(MediaType.TEXT_PLAIN)
public Response uploadPicture(File picture, @QueryParam("id") @NotNull Long id) {
    Employee employee = employeeService.findById(id);

    try (Reader reader = new FileReader(picture)) {
        // Set the picture data on the employee object
        employee.setPicture(Files.readAllBytes(Paths.get(picture.toURI())));
        employeeService.saveEmployee(employee);

        // Calculate the total size of the uploaded picture
        int totalsize = 0;
        int count = 0;
        final char[] buffer = new char[256];
        while ((count = reader.read(buffer)) != -1)
            totalsize += count;

        // Return a response with the total size of the uploaded picture
        return Response.ok(totalsize).build();
    } catch (IOException e) {
        // Return an error response if something goes wrong
        e.printStackTrace();
        return Response.serverError().build();
    }
}
```

In this example, we've created a JAX-RS resource method called `uploadPicture` that handles the picture upload request. We've used the `@POST` annotation to specify that this method should handle POST requests to the `/upload` endpoint. We've also used the `@Consumes` annotation to specify the accepted media types for the request, which in this case include `APPLICATION_OCTET_STREAM`, `image/png`, `image/jpeg`, and `image/jpg`.

In the method parameters, we've specified the `File` object for the picture and the `id` query parameter using the `@QueryParam` annotation. We've then loaded the employee object for the specified ID, set the picture data on the employee object using the `setPicture` method, and saved the employee object using the `saveEmployee` method.

Finally, we've calculated the total size of the uploaded picture using a `Reader` object and returned a response with the total size using the `Response` class.

<span style='font-size: 25px'>🤝 Comparison with Spring-Boot</span>

Spring Boot also provides support for file upload through its `MultipartFile` interface. The main difference between the two approaches is the implementation details. While JavaEE JAX-RS requires you to manually extract the file content from the request, Spring Boot handles this automatically through the `MultipartFile` interface. This can make file upload easier to implement in Spring Boot, but it also means that you have less control over the file upload process. Ultimately, the choice between the two approaches will depend on your specific requirements and preferences.

Here's an example of how to implement file upload in Spring Boot using `MultipartFile`:

```java
@RestController
public class FileUploadController {

    @PostMapping("/upload")
    public ResponseEntity<String> uploadFile(@RequestParam("file") MultipartFile file) {
        try {
            // Get the file name
            String fileName = file.getOriginalFilename();
            // Get the file content
            byte[] fileContent = file.getBytes();
            // Save the file to a desired location on the server
            String filePath = "/path/to/save/" + fileName;
            Files.write(Paths.get(filePath), fileContent);
            // Return a success response
            return ResponseEntity.status(HttpStatus.OK).body("File uploaded successfully");
        } catch (IOException e) {
            // Return an error response if something goes wrong
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("Failed to upload file");
        }
    }
}
```

In this example, we've created a controller class called `FileUploadController` with a method called `uploadFile` that handles the file upload request. We've used the `@PostMapping` annotation to specify that this method should handle POST requests to the `/upload` endpoint. We've also used the `@RequestParam` annotation to specify the file parameter in the method, and we've extracted the file content using the `getBytes` method of the `MultipartFile` interface. Finally, we've saved the file to a desired location on the server using the `Files.write` method.

I hope this helps! Let me know if you have any other questions.

### <span style='color: navy'>File Download</span>

Sure, I'm happy to help! Here's an updated version of the explanation with more emojis and additional details on the code snippets:

<span style='font-size: 25px'>What is Downloading a File in JavaEE JAX-RS?</span>

📥 Downloading a file in JavaEE JAX-RS is the process of transferring a file from a server to a client application over HTTP. This is useful when you want to allow users to download a file, such as a document or an image, from your web application.

<span style='font-size: 25px'>Why and When to Use Downloading a File in JavaEE JAX-RS?</span>

🤔 You would use downloading a file in JavaEE JAX-RS whenever you want to allow users to download files from your web application. For example, if you have a web application that stores user-generated images, you might want to allow users to download their images to their local machine.

<span style='font-size: 25px'>How to Download a File in JavaEE JAX-RS?</span>

👉 To download a file in JavaEE JAX-RS, you can follow these steps:

1. Define a new resource method in your JAX-RS resource class that will handle the file download request. In your example code, the resource method is called `downloadPicture`.

2. Use the `@Path` annotation to specify the URL path for the resource method. In your example code, the path is `/download`.

3. Use the `@Produces` annotation to specify the MIME media types that the resource method can produce. In your example code, the media types are `application/octet-stream`, `image/png`, `image/jpeg`, and `image/jpg`.

4. Within the resource method, retrieve the file you want to download. In your example code, the file is retrieved from the `employee` object.

   📝 Note: You can retrieve the file from a variety of sources such as a database or a file system.

5. Create a `Response` object that contains the file to be downloaded. In your example code, the file is written to a temporary file using the `Files.write` method, and then the file is added to the `Response` object as its entity.

   📝 Note: The `Response` object represents the response that will be sent back to the client.

6. Return the `Response` object from the resource method.

   📝 Note: The `Response` object will be sent back to the client as the HTTP response.

Here is an example code snippet that demonstrates how to download a picture of an employee in JavaEE JAX-RS:

```java
@POST
@Path("/download")
@Produces({MediaType.APPLICATION_OCTET_STREAM, "image/png", "image/jpeg", "image/jpg"})
public Response downloadPicture(@QueryParam("id") @NotNull Long id) throws IOException {
    Employee employee = employeeService.findById(id);
    if (employee != null) {
        byte[] picture = employee.getPicture();
        Path tempFile = Files.write(Paths.get("pic.png"), picture);
        File file = tempFile.toFile();
        Response.ResponseBuilder response = Response.ok((Object) file);
        response.header("Content-Disposition", "attachment; filename=\"pic.png\"");
        return response.build();
    }
    return Response.noContent().build();
}
```

In this example, the `downloadPicture` method accepts an employee ID as a query parameter and retrieves the corresponding employee from the service. If the employee exists, the method creates a `Response` object with a file entity that contains the picture of the employee. The `Response` object is then returned to the client.

```sql
📝 Note: In this code, we first retrieve the picture byte array from the `employee` object. Then, we write the byte array to a temporary file using the `Files.write` method. We create a `File` object from the temporary file and use it as the entity for the `Response` object. Finally, we set the `Content-Disposition` header to "attachment; filename=\"pic.png\"" to indicate that the file should be downloaded as an attachment.
```

<span style='font-size: 25px'>Comparison with Spring Boot</span>

🤝 In Spring Boot, you can also download files from a server to a client application using the `ResponseEntity` class. The main difference between Spring Boot and JavaEE JAX-RS is in the annotations used to specify the URL path and media types.

In Spring Boot, you would use the `@GetMapping` or `@PostMapping` annotations to specify the URL path, and the `produces` attribute of the `@GetMapping` or `@PostMapping` annotation to specify the media types.

Here's an example of how to download a file in Spring Boot:

```java
@GetMapping("/download")
public ResponseEntity<Resource> downloadFile() {
    Resource file = new FileSystemResource("/path/to/file");
    return ResponseEntity.ok()
            .header(HttpHeaders.CONTENT_DISPOSITION, "attachment; filename=\"" + file.getFilename() + "\"")
            .body(file);
}
```

```sql
📝 Note: In this code, we create a `ResponseEntity` object that contains a `Resource` object representing the file to be downloaded. We set the `Content-Disposition` header to "attachment; filename=\"" + file.getFilename() + "\"" to indicate that the file should be downloaded as an attachment. Finally, we return the `ResponseEntity` object to the client.
```

As you can see, the code is quite similar to the JavaEE JAX-RS example, but with different annotations. Ultimately, the choice of whether to use JavaEE JAX-RS or Spring Boot depends on your personal preference and the specific needs of your application.

I hope this updated explanation helps you understand how to download a file in JavaEE JAX-RS better! Let me know if you have any further questions. 😊

### <span style='color: navy'>Filters</span>

<span style='font-size: 25px'>What are Filters in JavaEE JAX-RS?</span>

🧹 Filters in JavaEE JAX-RS are components that intercept requests and responses to a web application. Filters can be used to perform a wide range of tasks, such as logging, authentication, authorization, input validation, output formatting, and more. 

🕵️‍♀️ Filters are similar to middleware in other web frameworks and can be thought of as a chain of interceptors that can modify the request and response objects before they are processed by the application.

<span style='font-size: 25px'>Why and When to Use Filters in JavaEE JAX-RS?</span>

🤔 You would use filters in JavaEE JAX-RS whenever you need to modify or inspect incoming requests or outgoing responses to a web application. For example, you might use a filter to log incoming requests to your application, or to authenticate users before allowing them to access certain resources.

📅 Filters can be used at various stages in the request/response lifecycle, such as before the request is processed by the application, after the response is generated by the application, or both.

<span style='font-size: 25px'>How to Use Filters in JavaEE JAX-RS?</span>

👉 To use filters in JavaEE JAX-RS, you can follow these steps:

1. Define a new class that implements the `ContainerRequestFilter` or `ContainerResponseFilter` interface, depending on whether you want to intercept incoming requests or outgoing responses.

2. Implement the `filter` method in your class. This method takes a `ContainerRequestContext` or `ContainerResponseContext` object as its argument, which represents the incoming request or outgoing response.

3. Modify or inspect the request or response object as needed in your `filter` method. For example, you might add headers to the request or response, modify the request URL, or validate input data.

4. Register your filter class with the application using the appropriate JAX-RS annotation, such as `@Provider` or `@NameBinding`.

Here's an example of a filter that logs incoming requests to a web application:

```java
@Provider
public class LoggingFilter implements ContainerRequestFilter {

    @Override
    public void filter(ContainerRequestContext requestContext) throws IOException {
        String method = requestContext.getMethod();
        String path = requestContext.getUriInfo().getPath();
        String address = requestContext.getUriInfo().getRequestUri().toString();
        String message = String.format("%s %s from %s", method, path, address);
        System.out.println(message);
    }
}
```

In this example, the `LoggingFilter` class implements the `ContainerRequestFilter` interface and overrides the `filter` method. The method extracts information from the `ContainerRequestContext` object, such as the HTTP method, request path, and request address, and logs it to the console.

The `@Provider` annotation is used to register the filter with the application.

Don't worry. I'll explain it in more detail in the upcoming sections!

<span style='font-size: 25px'>Comparison with Spring Boot</span>

🤝 In Spring Boot, you can use interceptors to achieve similar functionality to filters in JavaEE JAX-RS. Interceptors in Spring Boot are used to intercept incoming requests and outgoing responses to a web application and can be used for tasks such as logging, security, and caching.

📚 However, the implementation of interceptors in Spring Boot is different from filters in JavaEE JAX-RS. Interceptors in Spring Boot are implemented using the `HandlerInterceptor` interface and are registered with the application using the `WebMvcConfigurer` interface.

💡 Overall, both filters in JavaEE JAX-RS and interceptors in Spring Boot provide powerful ways to intercept and modify incoming requests and outgoing responses in web applications. The choice between the two will depend on the specific requirements of your application and your familiarity with the respective frameworks.

Here's an example of how to use interceptors in Spring Boot:

```java
@Component
public class LoggingInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String method = request.getMethod();
        String path = request.getRequestURI();
        String message = String.format("%s %s", method, path);
        System.out.println(message);
        return true;
    }
}
```

In this example, the `LoggingInterceptor` class is a Spring component that implements the `HandlerInterceptor` interface. The `HandlerInterceptor` interface defines three methods that can be used to intercept incoming requests and outgoing responses:

- `preHandle`: This method is called before the request is handled by the controller. It returns a boolean value indicating whether the request should be processed further or not.

- `postHandle`: This method is called after the request is handled by the controller but before the response is sent to the client.

- `afterCompletion`: This method is called after the response has been sent to the client.

In this example, we're overriding the `preHandle` method to log incoming requests to the console. The `HttpServletRequest` argument provides access to information about the incoming request, such as the HTTP method and request URI.

To register the interceptor with the application, we need to add it to the `WebMvcConfigurer` configuration:

```java
@Configuration
public class AppConfig implements WebMvcConfigurer {

    @Autowired
    private LoggingInterceptor loggingInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(loggingInterceptor);
    }
}
```

In this configuration class, we're implementing the `WebMvcConfigurer` interface and overriding the `addInterceptors` method. We're adding our `LoggingInterceptor` instance to the `InterceptorRegistry`, which registers it with the application.

That's it! Now, every incoming request to your Spring Boot application will be intercepted by the `LoggingInterceptor` and logged to the console.

I hope that helps! Let me know if you have any other questions.



- [x] Day 35/90 ===> [JavaEE: Day 35/90 - JAX-RS (Java API for RESTful Web Services)](https://www.linkedin.com/pulse/javaee-day-3590-jax-rs-java-api-restful-web-services-seyed-ali)
