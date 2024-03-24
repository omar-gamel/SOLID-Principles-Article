![SOLID Princoles](https://github.com/omar-gamel/SOLID-principles-article/blob/main/solid-principles.PNG)
# SOLID Principles

 The SOLID Principles are five principles of Object-Oriented class design.<br> 
 They are a set of rules and best practices to follow while designing a class structure.<br>
 They all serve the same purpose: To create understandable, readable, and testable code that many developers can collaboratively work on.<br>
 
<h3>Background</h3>

<p>The SOLID principles were first introduced by the famous Computer Scientist Robert J. Martin (a.k.a Uncle Bob) in his paper in 2000. But the SOLID acronym was introduced later by Michael Feathers.</p>
 
<p>Uncle Bob is also the author of bestselling books Clean Code and Clean Architecture, and is one of the participants of the "Agile Alliance".</p>
 
Let's look at each principle one by one. Following the SOLID acronym, they are:
- The <b>S</b>ingle Responsibility Principle
- The <b>O</b>pen-Closed Principle
- The <b>L</b>iskov Substitution Principle
- The <b>I</b>nterface Segregation Principle
- The <b>D</b>ependency Inversion Principle

# The Single Responsibility Principle

The Single Responsibility Principle states that <b> a class should do one thing and therefore it should have only a single reason to change.</b>

<p>In this section we will look at some common mistakes that violate the Single Responsibility Principle.</p>

```  java
public class User {
    private String name;
    private String email;

    public User(String name, String email) {
        this.name = name;
        this.email = email;
    }

    public void saveToDatabase() {
        // Logic to save user details to a database
    }

    public void sendEmail(String content) {
        // Logic to send an email to the user
    }
}
```

<b>Refactored Example (SRP)</b>

We can refactor the above into three classes, each with a single responsibility:

1. <b>User</b> Manages user properties.
2. <b>UserDataBase</b> Handles database operations for users.
3. <b>UserEmailService</b> Manages email sending for users.


``` java  
public class User {
    private String name;
    private String email;

    public User(String name, String email) {
        this.name = name;
        this.email = email;
    }

    // Getters and setters for name and email
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}
```
``` java  
public class UserDatabase {
    public void saveToDatabase(User user) {
        // Logic to save user details to a database
    }
}
```
``` java  
public class UserEmailService {
    public void sendEmail(User user, String content) {
        // Logic to send an email to the user
    }
}
```

In this refactored example, each class has a single responsibility: <b>User</b> holds user data, <b>UserDatabase</b> handles database operations, and <b>UserEmailService</b> deals with sending emails. This structure adheres to the Single Responsibility Principle, making the code more modular, easier to understand, maintain, and test.

# Open-Closed Principle

The Open-Closed Principle requires that <b>classes should be open for extension and closed to modification.</b>

<p>Modification means changing the code of an existing class, and extension means adding new functionality.</p>

<p>So what this principle wants to say is: We should be able to add new functionality without touching the existing code for the class. This is because whenever we modify the existing code, we are taking the risk of creating potential bugs. So we should avoid touching the tested and reliable (mostly) production code if possible.<p>

<p>But how are we going to add new functionality without touching the class, you may ask. It is usually done with the help of interfaces and abstract classes.</p>	

<b>Original Classes (Violating OCP)</b>

``` java
public class User {
    private String name;
    private String email;
    // ... other fields, constructors, getters, setters

    public void saveToDatabase() {
        // Code to save user details directly to a database
    }
}
```
In this original setup, if we want to support saving to a different type of database or a different storage method (like a file system), we would need to modify the <b>User</b> class, which violates the OCP.

<b>Refactored Classes (Adhering to OCP)</b>

To adhere to OCP, we can introduce an interface for data persistence and then provide implementations for different persistence mechanisms:

``` java
public interface UserDataPersistence {
    void save(User user);
}
```
``` java
public class SqlDatabasePersistence implements UserDataPersistence {
    public void save(User user) {
        // SQL database saving logic
    }
}
```
``` java
public class FilePersistence implements UserDataPersistence {
    public void save(User user) {
        // File saving logic
    }
}
```
``` java
public class User {
    private String name;
    private String email;
    // ... other fields, constructors, getters, setters

    // User doesn't know about the saving mechanism
    private UserDataPersistence persistence;

    public User(UserDataPersistence persistence) {
        this.persistence = persistence;
    }

    public void saveToDatabase() {
        persistence.save(this);
    }
}
```
``` java
public class Main {
    public static void main(String[] args) {
        User user = new User(new SqlDatabasePersistence());
        user.saveToDatabase(); // Saves to SQL database

        User anotherUser = new User(new FilePersistence());
        anotherUser.saveToDatabase(); // Saves to a file
    }
}
```
In this example, the <b>User</b> class is closed for modifications as it doesn't need to change when we want to add new data persistence methods. Instead, we extend the functionality by implementing new classes that adhere to the <b>UserDataPersistence</b> interface.

By using the <b>UserDataPersistence</b> interface, we can easily add more ways to persist user data, like <b>NoSqlDatabasePersistence</b> or <b>CloudStoragePersistence</b>, without ever needing to touch the <b>User</b> class again. This is how we achieve being open for extension but closed for modification.

# Liskov Substitution Principle

<p>The Liskov Substitution Principle states that <b>subclasses should be substitutable for their base classes.</b></p>

Here's an example demonstrating LSP with a base class <b>Payment</b> and a subclass <b>CreditCardPayment</b>. The principle will guide us to ensure that <b>CreditCardPayment</b> can replace Payment without altering the desirable properties of the program.

```
 T = Parent;
 S = SubClass;

 t1 = new T
    = new S
```

<b>Example Before Applying LSP</b>

Suppose we have a <b>Payment</b> class and a <b>CreditCardPayment</b> class that overrides a method in a way that changes the original behavior:

``` java
class Payment {
    void processPayment(double amount) {
        // Process the payment through bank transfer
        System.out.println("Processing bank transfer payment of " + amount);
    }
}

class CreditCardPayment extends Payment {
    void processPayment(double amount) {
        if (amount > 1000) {
            throw new IllegalArgumentException("Amount exceeds credit card limit");
        }
        // Process the credit card payment
        System.out.println("Processing credit card payment of " + amount);
    }
}

class PaymentProcessor {
    void process(Payment payment, double amount) {
        payment.processPayment(amount);
    }
}
```

In this code, using a <b>CreditCardPayment</b> where a <b>Payment</b> is expected can lead to unexpected exceptions because of the amount limit, which violates LSP.

<b>Example After Applying LSP</b>

To adhere to LSP, we should ensure that <b>CreditCardPayment</b> can be used wherever <b>Payment</b> is expected without any surprises:

``` java
class Payment {
    void processPayment(double amount) {
        // Process the payment
        System.out.println("Processing payment of " + amount);
    }
}

class CreditCardPayment extends Payment {
    @Override
    void processPayment(double amount) {
        // Precondition not strengthened in the subclass
        // Process the credit card payment with no additional limitations
        System.out.println("Processing credit card payment of " + amount);
    }
}

class PaymentProcessor {
    void process(Payment payment, double amount) {
        payment.processPayment(amount);
    }
}
```

In this refactored example, the <b>CreditCardPayment</b> class can be substituted for <b>Payment</b> without altering the behavior expected by <b>PaymentProcessor</b>. We have removed the condition that throws an exception for amounts over 1000, thus adhering to LSP.


``` java
public class Main {
    public static void main(String[] args) {
        Payment payment = new Payment();
        CreditCardPayment creditCardPayment = new CreditCardPayment();
        PaymentProcessor processor = new PaymentProcessor();

        processor.process(payment, 500); // Works for Payment
```
``` java
        processor.process(creditCardPayment, 500); // Also works for CreditCardPayment
    }
}
```
With this refactoring, both <b>Payment</b> and <b>CreditCardPayment</b> can be used interchangeably in the <b>PaymentProcessor</b>. process method without any unexpected behavior, adhering to the Liskov Substitution Principle. If there need to be constraints or different behaviors for <b>CreditCardPayment</b>, these should be transparent to the clients of the <b>Payment</b> class and not violate the contract set by the superclass.

# Interface Segregation Principle

Segregation means keeping things separated, and the Interface Segregation Principle is about separating the interfaces.

The principle states that many client-specific interfaces are better than one general-purpose interface. Clients should not be forced to implement a function they do no need.

<b>Example Before Applying ISP</b>

Let's say we have a large interface for a <b>SmartDevice</b> that includes methods not relevant to all smart devices:

``` java
interface SmartDevice {
    void print();
    void fax();
    void scan();
    void call();
}
```

Not all smart devices have printing, faxing, scanning, or calling capabilities. A SmartCamera implementing this interface would have to implement methods that have nothing to do with a camera's functionality:

``` java
class SmartCamera implements SmartDevice {
    public void print() {
        // Not applicable for SmartCamera
    }

    public void fax() {
        // Not applicable for SmartCamera
    }

    public void scan() {
        // Not applicable for SmartCamera
    }

    public void call() {
        // SmartCamera can't make calls
    }

    // Camera specific functions...
}
```

<b>Example After Applying ISP</b>

We can refactor our design to follow ISP by breaking the SmartDevice interface into more granular and specific interfaces:

``` java
interface Printer {
    void print();
}

interface Fax {
    void fax();
}

interface Scanner {
    void scan();
}

interface Phone {
    void call();
}
```

Now, the <b>SmartCamera</b> class can implement only the interfaces that are relevant to its functionality:

``` java
class SmartCamera {
    // Camera specific functions...
    // It does not implement Printer, Fax, Scanner, or Phone
}
```

A device that can print and scan would implement <b>Printer</b> and <b>Scanner</b> interfaces, but not <b>Fax</b> or <b>Phone</b>. This way, each type of smart device only needs to know about the methods that are relevant to it, fulfilling the Interface Segregation Principle.

# Dependency Inversion Principle

The Dependency Inversion principle states that <b>our classes should depend upon interfaces or abstract classes instead of concrete classes and functions.</b>

1. High-level modules should not depend on low-level modules. Both should depend on abstractions.
2. Abstractions should not depend on details. Details should depend on abstractions.

<b>Note:</b> If the OCP states the goal of OO architecture, the DIP states the primary mechanism.

These two principles are indeed related and we have applied this pattern before while we were discussing the Open-Closed Principle.

We want our classes to be open to extension, so we have reorganized our dependencies to depend on interfaces instead of concrete classes. Our PersistenceManager class depends on InvoicePersistence instead of the classes that implement that interface.

<b>Example Before Applying DIP</b>

Consider a shopping cart system that directly depends on a concrete <b>MySQLDatabase</b> class for data persistence:

``` java
class ShoppingCart {
    private MySQLDatabase database;

    public ShoppingCart() {
        database = new MySQLDatabase();
    }

    public void checkout() {
        // perform checkout
        database.saveOrder(this);
    }
}

class MySQLDatabase {
    public void saveOrder(ShoppingCart cart) {
        // Save order to MySQL database
    }
}
```
In this example, changing the database would require changes to the <b>ShoppingCart</b> class, which violates DIP.

<b>Example After Applying DIP</b>

To follow DIP, we would introduce an abstraction that the <b>ShoppingCart</b> depends on, and invert the dependency so that the details of how an order is saved are decided by the implementation, not by the <b>ShoppingCart</b> class.

``` java
interface Database {
    void saveOrder(ShoppingCart cart);
}

class ShoppingCart {
    private Database database;

    public ShoppingCart(Database database) {
        this.database = database;
    }

    public void checkout() {
        // perform checkout
        database.saveOrder(this);
    }
}

class MySQLDatabase implements Database {
    public void saveOrder(ShoppingCart cart) {
        // Save order to MySQL database
    }
}

class MongoDBDatabase implements Database {
    public void saveOrder(ShoppingCart cart) {
        // Save order to MongoDB database
    }
}
```

With this refactored code, <b>ShoppingCart</b> now depends on the <b>Database</b> interface, an abstraction, rather than on a concrete database class. This way, <b>ShoppingCart</b> is not affected by changes in database implementations, and new types of databases can be added without modifying ShoppingCart code. This design is in line with DIP and increases the modularity and flexibility of the code.

