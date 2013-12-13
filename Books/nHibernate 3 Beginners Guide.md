#nHibernate 3

## 1. First Look

### What is new in NHibernate 3.0

* now based on .NET 3.5
* A totally rewritten LINQ provider
* QueryOver api
* ConfORM
* lazy load specific columns of a table
* Detached criteria with stateless sessions

### Where do I get it

* http://www.nhforge.org
* http://fluentnhibernate.org/downloads
* http://sourceforge.net/projects/nhcontrib/
* via nuget

### Is there documentation available

* NHibernate meta blog at http://nhforge.org/blogs/nhibernate.
* NHibernate FAQs at http://nhibernate.hibernatingrhinos.com/.
* Fabio Maulo's blog at http://fabiomaulo.blogspot.com. Fabio is the leader of the NHibernate project.
* Ayende Rahien's blog at http://ayende.com/Blog/default.aspx. Ayende is one of the main contributors to NHibernate. Search for NHibernate.
* James Gregory's blog at http://jagregory.com. James is the leader of the Fluent NHibernate project

#### books

* "Hibernate in Acton" by Christan Bauer and Gavin King, Manning Publicatons Co.: This is probably the frst book published about Hibernate and discusses the Java 
version of the framework
* "NHibernate in Acton" by Pierre Henri Kuaté, Tobin Harris, Christan Bauer, Gavin King, and Tobin Harris Manning Publicatons Co.
* "NHibernate 2 Beginner's Guide" by Aaron Cure, Packt Publishing
* "NHibernate 3.0 Cookbook" by Jason Dentler, Packt Publishing

### Can I get help using NHibernate

*  http://groups.google.com/group/nhusers

### Who uses it

## 2. A First Complete Sample

### Prepare our development environment

* NHibernate and Fluent NHibernate

### Defining a model

* Category
  * Description
  * Id
  * Name

* Product
  * Description
  * Discontinued
  * Id
  * Name
  * ReorderLevel
  * UnitPrice

Product -> Category

### Creating the product inventory model

```csharp
public class Category
{
	public virtual int Id { get; set; }
	public virtual string Name { get; set; }
	public virtual string Description { get; set; }
}

public class Product
{
    public virtual int Id { get; set; }
    public virtual string Name { get; set; }
    public virtual string Description { get; set; }
    public virtual Category Category { get; set; }
    public virtual decimal UnitPrice { get; set; }
    public virtual int ReorderLevel { get; set; }
    public virtual bool Discontinued { get; set; }
}

```

### Mapping our model

```csharp
public class CategoryMap : ClassMap<Category>
{
    public CategoryMap()
    {
      Id(x => x.Id);
      Map(x => x.Name);
      Map(x => x.Description);
    }
}


public class ProductMap : ClassMap<Product>
{
    public ProductMap()
    {
	  Id(x => x.Id);
      Map(x => x.Name);
      Map(x => x.Description);
      Map(x => x.UnitPrice);
      Map(x => x.ReorderLevel);
      Map(x => x.Discontinued);
      References(x => x.Category);
    }
}

```  

### Creating the database schema

```csharp

public partial class MainWindow
{
    const string connString = "server=.\\SQLExpress;" + 
      "database=NH3BeginnersGuide;" + 
      "Integrated Security=SSPI;";
    public MainWindow()
    {
      InitializeComponent();
    }
    private void btnCreateDatabase_Click(object sender, 
      RoutedEventArgs e)
    {
      Fluently.Configure() 
        .Database(MsSqlConfiguration 
          .MsSql2008 
          .ConnectionString(connString)) 
        .Mappings(m =>m.FluentMappings 
          .AddFromAssemblyOf<ProductMap>()) 
        .ExposeConfiguration(CreateSchema) 
        .BuildConfiguration();
    }
    private static void CreateSchema(Configuration cfg)
    {
      var schemaExport = new SchemaExport(cfg);
      schemaExport.Drop(false, true);
      schemaExport.Create(false, true);
    }
}

```  

### Creating a session factory

```csharp

private ISessionFactory CreateSessionFactory()
{
  return Fluently.Configure().Database(MsSqlConfiguration.MsSql2008.ConnectionString(connString)) 
    		.Mappings(m =>m.FluentMappings.AddFromAssemblyOf<ProductMap>())
    		.BuildSessionFactory();
}

```  

### Opening a session

```csharp

var factory = CreateSessionFactory();
using (var session = factory.OpenSession())
{
    // do something with the session
}


```  

### Persisting objects to the database

```csharp

var category = new Category  
{ 
    Name = "Beverages",  
    Description = "Some description" 
};

var id = session.Save(category);

var category = new Category { Name = "Beverages" };
var product = new Product { Name = "Milk", Category = category };
session.Save(category);
session.Save(product);

// To delete
session.Delete(category);


var factory = CreateSessionFactory();
using (var session = factory.OpenSession()) 
{ 
    var category = new Category 
    {
    	Name = txtCategoryName.Text, 
      	Description = txtCategoryDescription.Text 
    };
    session.Save(category);
}

```  

### Adding a new category to the database

```csharp
var factory = CreateSessionFactory();
using (var session = factory.OpenSession()) 
{ 
    var category = new Category 
    {
    	Name = txtCategoryName.Text, 
      	Description = txtCategoryDescription.Text 
    };
    session.Save(category);
}

```  

### Reading from the database

```csharp

var category = session.Get<Category>(1);
var categories = session.Query<Category>();
var products = session.Query<Product>() 
  .Where(p =>p.Discontinued) 
  .OrderBy(p =>p.Name);

```  

### Loading the list of all categories from the database

```csharp
var factory = CreateSessionFactory();
using (var session = factory.OpenSession())
{
    var categories = session.Query<Category>() 
      .OrderBy(c =>c.Name) 
      .ToList();
    lstCategories.ItemsSource = categories;
    lstCategories.DisplayMemberPath = "Name";
}  

```  


## 3. Creating a Model

### What is a model

A model is an atempt to describe reality. A model is equally understandable by us 
developers, as by business analysts, our customers, and their subject mater experts. Thus, 
a model is not a UML diagram or any other very developer-centric way of describing the 
domain.

### Model first versus data first

Data sitng in a data store is of no value as long as there are no processes defned about 
how to use and interpret this data. However, the defniton of those processes and usage 
scenarios are part of what we call the model of the corresponding domain. It is the domain 
model, which is really at the heart of an applicaton, that adds value to a business. The data 
and its structure follow.

### Elements of a model

#### Entity

An entty is an object that is uniquely identfable by a combinaton of its atributes and has 
a well-defned lifespan.

#### Value object

In a model, objects may exist with a life span of no interest and which do not need to be uniquely identfed by an ID or a key. These kinds of objects are called value objects.

Examples

* Name of, for example, a person entty. The Name value object consists of the surname, given name, and middle name of a person object.
* Geographical coordinate in a GIS applicaton. Such value objects consist of a value pair for lattude and longitude.
* Color in a colorimetry applicaton. A color object consists of values for red, green, blue, and alpha channels.
* Address in a customer relatonship management (CRM) applicaton as part of a customer entty. An address object might contain values for address line 1 and 2, zip code, and city

### Creating a Name value object

```csharp

public class Name
{
  public string LastName { get; private set; }
  public string FirstName { get; private set; }
  public string MiddleName { get; private set; }

  public Name(string firstName, string middleName, string lastName)
  {
    if(string.IsNullOrWhiteSpace(firstName)) throw new ArgumentException("First name must be defined.");
    if(string.IsNullOrWhiteSpace(lastName)) throw new ArgumentException("Last name must be defined.");
    FirstName = firstName;
    MiddleName = middleName;
    LastName = lastName;
  }

  public override int GetHashCode()
  {
    unchecked
    {
      var result = FirstName.GetHashCode();
      result = (result*397) ^ (MiddleName != null ? MiddleName.GetHashCode() : 0);
      result = (result*397) ^ LastName.GetHashCode();
      return result;
    }
  }

  public bool Equals(Name other)
  {
    if (other == null) return false;
    if (ReferenceEquals(this, other)) return true;
    return Equals(other.FirstName, FirstName) &&  
      Equals(other.MiddleName, MiddleName) &&  
      Equals(other.LastName, LastName);
  }

  public override bool Equals(object other)
  {
    return Equals(other as Name);
  }

}


```

### Creating a base entity

1. Add a new class and new auto properties to it
2. Override the Equals and provide the implementation of GetHashCode()
3. Override == and !=

```csharp

public abstract class Entity<T> where T : Entity<T>
{
  public Guid ID { get; private set; }

  public override bool Equals(object obj)
  {
    var other = obj as T;
    if (other == null) return false;
    var thisIsNew = Equals(ID, Guid.Empty);
    var otherIsNew = Equals(other.ID, Guid.Empty);
    if (thisIsNew && otherIsNew)
      return ReferenceEquals(this, other);
    return ID.Equals(other.ID);
  }

  private int? oldHashCode;
  public override int GetHashCode()
  {
    // once we have a hashcode we'll never change it
    if (oldHashCode.HasValue)
      return oldHashCode.Value;
    // when this instance is new we use the base hash code
    // and remember it, so an instance can NEVER change its
    // hash code.
    var thisIsNew = Equals(ID, Guid.Empty);
    if(thisIsNew)
    {
      oldHashCode = base.GetHashCode();
      return oldHashCode.Value;
    }
    return ID.GetHashCode();
  } 

  public static bool operator ==(Entity<T> lhs, Entity<T> rhs)
  {
    return Equals(lhs, rhs);
  }
  public static bool operator !=(Entity<T> lhs, Entity<T> rhs)
  {
    return !Equals(lhs, rhs);
  }

}

```

Enttes that have never been saved to a database are called *transient*. Enttes that have been saved to the database are called *persistent*.

### Creating a Customer entity

1. Add a new class and make it inherit the base entity class
2. Add auto-properties
3. Implement (a) method(s) that change(s) properties value

```csharp

public class Customer : Entity<Customer>
{ 
  public string CustomerIdentifier { get; private set; }
  public Name CustomerName { get; private set; }

  public void ChangeCustomerName(string firstName, string  
    middleName, string lastName)
  {
    CustomerName = new Name(firstName, middleName, lastName);
  }

}

  public string CustomerIdentifier { get; private set; }
  public Name CustomerName { get; private set; }

  public void ChangeCustomerName(string firstName, string  
    middleName, string lastName)
  {
    CustomerName = new Name(firstName, middleName, lastName);
  }

```

### Defining relations between entities

#### Owns or contains

Value objects can never exist alone. They only become a meaning in conjuncton with an entty. An entty can own or contain zero to many value objects.

Customer -- (CustomerName) --> Name

```csharp
public Name CustomerName { get; private set; }
```

#### One to many

Product -- (Category) --> Category
Category -- (Products) --> Product

```chsarp
// Product
public Category Category { get; private set; }

// Category
private List<Products> products;
public IEnumerable<Product> Products { get { return products; } }
```

#### One to one

Person ----> Student
Student ----> Person

Professor ----> HeadOfDepartment
HeadOfDepartment ----> Professor

#### Many to many

Product -- (Orders) --> Order
Order -- (Products) --> Product

Order --> (LineItems) --> LineItem -- (Product) --> Product
LineItem -- (Order) --> Order

### The order entry model



### Implementing an order entry model

```csharp
// Address

public bool Equals(Address other)
{
  if (other == null) return false;
  if (ReferenceEquals(this, other)) return true;
  return Equals(other.Line1, Line1) && 
    Equals(other.Line2, Line2) && 
    Equals(other.ZipCode, ZipCode) && 
    Equals(other.City, City) && 
    Equals(other.State, State);
}
public override bool Equals(object obj)
{
  return Equals(obj as Address);
}

public override int GetHashCode()
{
  unchecked
  {
    var result = Line1.GetHashCode();
    result = (result * 397) ^ (Line2 != null ?  
      Line2.GetHashCode() : 0);
    result = (result * 397) ^ ZipCode.GetHashCode();
    result = (result * 397) ^ City.GetHashCode();
    result = (result * 397) ^ State.GetHashCode();
    return result;
  }
}

// Customer

private readonly List<Order> orders;
public IEnumerable<Order> Orders { get { return orders; } }

public void PlaceOrder(LineInfo[] lineInfos,  
  IDictionary<int, Product> products)
{
  var order = new Order(this);
  foreach (var lineInfo in lineInfos)
  {
    var product = products[lineInfo.ProductId];
    order.AddProduct(this, product, lineInfo.Quantity);
  }
  orders.Add(order);
}

// Order

private readonly List<LineItem> lineItems;
public IEnumerable<LineItem> LineItems 
{ get { return lineItems; } }

public Order(Customer customer)
{
  lineItems = new List<LineItem>();
  Customer = customer;
  OrderDate = DateTime.Now;
}

public void AddProduct(Customer customer, Product product, int quantity)
{
  Customer = customer;
  var line = new LineItem(this, quantity, product);
  lineItems.Add(line);
}

// LineItem

public LineItem(Order order, int quantity, Product product)
{
  Order = order;
  Quantity = quantity;
  Product = product;
  UnitPrice = product.UnitPrice;
  if (quantity >= 10)
    Discount = 0.05m;
}

// LineInfo

public class LineInfo
{
  public int ProductId { get; set; }
  public int Quantity { get; set; }
}
```

## 4. Defining the Database Schema

### What is a database schema?

The database schema is a descripton of the structure of the database, and as such, describes how the data is organized inside the database and is divided into database tables

### Creating the Categories table

* Id: int, not null
* Name: nvarchar(50), not null,
* UnitPrice: money, null
* ReorderLevel: int, null
* Discontinued: bit, null
* Description: nvarchar(MAX), null

### Defining a script to create the Products table

```sql
create table Products ( 
  Id int not null, 
  Name nvarchar(50) not null, 
  UnitPrice money not null, 
  ReorderLevel int not null, 
  Discontinued bit not null, 
  Description nvarchar(MAX), 
  primary key (Id) 
  )

GO
```

### Table columns

| .NET     | SQL Server | Oracle | Comment |
| ----     | ---------- | ------ | ------- |
| int      | int        | NUMBER(p,0) | p is the precision and specifes the maximal number of decimal digits that can be stored. | 
| decimal  | money or decimal(p,s) | NUMBER(p,s) | s is the scale and specifes the maximal number of decimal digits that can be stored to the right of the decimal point. For example, NUMBER (7,2) has 5 digits before and 2 digits afer the decimal point. | 
| string   | nvarchar(50)  | VARCHAR2(50) | For short strings of up to 4000 characters. Stores Unicode characters. | 
| string   | nvarchar(MAX) | NCLOB | For strings of a length of up to 4 GB. Stores Unicode characters. | 
| bool     | bit | CHAR(1) | Oracle does not have a Boolean type. Ofen it is simulated by a char(1) column containing either Y or N. | 
| DateTime | datetme  | TIMESTAMP | Date and tme. | 
| DateTime | date | DATE  | Date only. | 
| byte[]   | varbinary(n)  | RAW | Variable length binary strings of up to 2000 bytes. n is the max length of the array. | 
| byte[]   | varbinary(MAX) | BLOB | Stores unstructured binary large objects of up to 4 GB. | 


### Relations, constraints, and indices
### Adding a constraint to the Product table
### Creating a script to add a check constraint
### Adding an index using the designer
### Creating a script to add an index
### Normal form
### Putting it all together
### Creating a schema for the order entry system
### Do not use database-generated IDs
### Views
### What about stored procedures and triggers?

## 5. Mapping the model to the database
## 6. Sessions and Transactions
## 7. Testing, Profiling, Monitoring, and Logging
## 8. Configuration
## 9. Writing Queries
## 10. Validating the Data to Persist
## 11. Common Pitfalls - Things to avoid