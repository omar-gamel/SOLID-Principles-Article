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

<p>In this section we will look at some common mistakes that violate the Single Responsibility Principle. Then we will talk about some ways to fix them.</p>

```   
class Book {
	String name;
	String authorName;
	int year;
	int price;
	String isbn;

	public Book(String name, String authorName, int year, int price, String isbn) {
		this.name = name;
		this.authorName = authorName;
		this.year = year;
        this.price = price;
		this.isbn = isbn;
	}
}
```   


<p>Now let's create the invoice class which will contain the logic for creating the invoice and calculating the total price. For now, assume that our bookstore only sells books and nothing else.</p>

```  
public class Invoice {

	private Book book;
	private int quantity;
	private double discountRate;
	private double taxRate;
	private double total;

	public Invoice(Book book, int quantity, double discountRate, double taxRate) {
		this.book = book;
		this.quantity = quantity;
		this.discountRate = discountRate;
		this.taxRate = taxRate;
		this.total = this.calculateTotal();
	}

	public double calculateTotal() {
	        double price = ((book.price - book.price * discountRate) * this.quantity);

		double priceWithTaxes = price * (1 + taxRate);

		return priceWithTaxes;
	}

	public void printInvoice() {
            System.out.println(quantity + "x " + book.name + " " +          book.price + "$");
            System.out.println("Discount Rate: " + discountRate);
            System.out.println("Tax Rate: " + taxRate);
            System.out.println("Total: " + total);
	}

        public void saveToFile(String filename) {
	// Creates a file with given name and writes the invoice
	}

}
```  

<p>Here is our invoice class. It also contains some fields about invoicing and 3 methods:</p>

- <b>calculateTotal</b> method, which calculates the total price,
- <b>printInvoice</b> method, that should print the invoice to console, and
- <b>saveToFile</b> method, responsible for writing the invoice to a file.

<p>The first violation is the printInvoice method, which contains our printing logic. The SRP states that our class should only have a single reason to change, and that reason should be a change in the invoice calculation for our class.</p>

<p>But in this architecture, if we wanted to change the printing format, we would need to change the class. This is why we should not have printing logic mixed with business logic in the same class.</p>

<p>There is another method that violates the SRP in our class: the saveToFile method. It is also an extremely common mistake to mix persistence logic with business logic.</p>

<p>So how can we fix this print function, you may ask.</p>

<p>We can create new classes for our printing and persistence logic so we will no longer need to modify the invoice class for those purposes.</p>

<p>We create 2 classes, <b>InvoicePrinter</b> and <b>InvoicePersistence</b>, and move the methods.</p>

```
public class InvoicePrinter {
    private Invoice invoice;

    public InvoicePrinter(Invoice invoice) {
        this.invoice = invoice;
    }

    public void print() {
        System.out.println(invoice.quantity + "x " + invoice.book.name + " " + invoice.book.price + " $");
        System.out.println("Discount Rate: " + invoice.discountRate);
        System.out.println("Tax Rate: " + invoice.taxRate);
        System.out.println("Total: " + invoice.total + " $");
    }
}
```
```
public class InvoicePersistence {
    Invoice invoice;

    public InvoicePersistence(Invoice invoice) {
        this.invoice = invoice;
    }

    public void saveToFile(String filename) {
        // Creates a file with given name and writes the invoice
    }
}
```
<p>Now our class structure obeys the Single Responsibility Principle and every class is responsible for one aspect of our application.</p>

# Open-Closed Principle


