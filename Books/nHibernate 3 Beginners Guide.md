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

#### Constraint 

* Not Null
* Data Type
* Check Constraint: limit the value
* Foreign key

### Creating a script to add a check constraint

```sql
IF EXISTS (SELECT * FROM sys.check_constraints  
  WHERE object_id = OBJECT_ID('CK_Products_ReorderLevel')  
  AND parent_object_id = OBJECT_ID('Products'))
ALTER TABLE Product DROP CONSTRAINT CK_Products_ReorderLevel

GO

ALTER TABLE Products ADD CONSTRAINT CK_Products_ReorderLevel CHECK (ReorderLevel>=0)

GO
```
### Creating a script to add an index

```sql
IF  EXISTS (SELECT * FROM sys.indexes  
  WHERE object_id = OBJECT_ID('Product')  
  AND name = 'IX_Products_Name')
DROP INDEX IX_Products_Name ON Product
GO

CREATE UNIQUE INDEX IX_Products_Name ON Product (Name ASC)
GO

```

### Normal form

* 1NF: A table is free of repeatng groups.
* 2NF: Non-key atributes must depend on the whole key.
* 3NF: Non-key atributes are dependent on "nothing but the key".

### Do not use database-generated IDs

Use NHibernate POID (Persistent Object ID): High/Low, GUID

### Views

```sql
create view CustomerOrderTotals
as select c.Id, 
  c.FirstName, 
  c.MiddleName, 
  c.LastName, 
  o.OrderDate, 
  o.OrderTotal
from Customers c
join Orders o on c.Id = o.CustomerId 
  where o.OrderTotal >= 1000;
```

### What about stored procedures and triggers?

Avoid as much as possible

## 5. Mapping the model to the database

### What is mapping?

Mapping defnes how the data that lives in the model inside the objects and their propertes fnds its way into database tables and their felds, defned in a relatonal database schema.

### Types of mapping

* XML-based mapping
* Attribute-based mapping
* Fluent mapping
* Convention-based mapping (Auto-mapping)

#### XML-based mapping

Only add the type definition if it is absolutely necessary

```xml
<property name="Name" not-null="true" length="50"/>
```

Product.hbm.xml

```xml
<?xml version="1.0" encoding="utf-8" ?>
<hibernate-mapping xmlns="urn:nhibernate-mapping-2.2" 
  assembly="Sample1" 
  namespace="Sample1.Domain">
  <class name="Product">
    <id name="Id">
      <generator class="native"/>
    </id>
    <property name="Name" not-null="true" length="50"/>
    <property name="UnitPrice" not-null="true" />
    <property name="ReorderLevel" not-null="true" />
    <property name="Discontinued" not-null="true" />
  </class>
</hibernate-mapping>
```

#### Attribute-based mapping

* NHibernate contribution
* Castle Active Records

```csharp
[Class(Name = "Products")]
public class Product
{
  [Id(Column = "ProductID")]
  [Generator(Class = "native")]
  public int Id { get; set; }
  [Property(Column = "ProductName", Length = 50, NotNull = true)]
  public string Name { get; set; }
  [Property(NotNull = true)]
  public decimal UnitPrice { get; set; }
  [Property(NotNull = true)]
  public int ReorderLevel { get; set; }
  [Property(NotNull = true)]
  public bool Discontinued { get; set; }
}
```

#### Fluent mapping

```csharp
public class ProductMap : ClassMap<Product>
{
  public ProductMap()
  {
    Id(x =>x.Id);
    Map(x =>x.Name).Length(50).Not.Nullable();
    Map(x =>x.UnitPrice).Not.Nullable();
    Map(x =>x.ReorderLevel).Not.Nullable();
    Map(x =>x.Discontinued).Not.Nullable();
  }
}

```

The advantages

* The code used to defne the mapping is very readable and concise.
* The mapping is kept separate from the entty, and thus does not pollute the entty.
* The mapping is type-safe. We use no magic strings.
* Possible selectons or setngs for each element are very discoverable as the fuent API is fully supported by IntelliSense.

#### Mapping by convention


### A word about lazy loading

#### Loading data on demand

Data is only to be loaded when it is really needed.

#### Proxies

NHibernate uses proxies. A *proxy* is a class that is wrapped around another class and acts on its behalf.
When NHibernate loads data from the database and creates an entty, say a product, it does not return an instance of type product to us, but rather a proxy to the product. To us, this proxy seems to be a product as it behaves like a product. This is possible due to the fact that the proxy is inherited from the Product class and just overrides all its virtual members

### Creating database schema creation scripts

```csharp
// Generate sql script

var configuration = ...
var sb = new StringBuilder();
var writer = new StringWriter(sb);
var exporter = new SchemaExport(configuration);
exporter.Execute(true, false,false, null, writer);
Console.WriteLine(sb);

var configuration = ...
var sb = new StringBuilder();
var exporter = new SchemaExport(configuration);
exporter.Create(s =>sb.AppendLine(s), false);
Console.WriteLine(sb);

var connString = "server=.\\SQLEXPRESS;database=OrderingSystem;" + 
  "integrated security=SSPI;";
var configuration = Fluently.Configure() 
  .Database(MsSqlConfiguration.MsSql2008 
  .ConnectionString(connString) 
  )
  .Mappings(m =>m.FluentMappings 
  .AddFromAssemblyOf<Product>())
  .BuildConfiguration();
```

### Fluent mapping

#### Expression trees

when the compiler parses code, it converts this code into tree-like structures, which can be beter analyzed and optmized. 
This analysis of the expression tree is also called statc refecton, whereas the standard refecton using the Type type of 
.NET is called dynamic refecton.

```csharp
// Instead of 
Map("Some Property");

// We use
Map(x => x.SomeProperty);

public Property Part<T> Map(Expression<Func<T, object>> expression)
{…}
```

#### Classes

```csharp
public class ProductMap : ClassMap<Product>
{
  public ProductMap()
  {
  // here we define the mapping details
  }
}
```

#### Entity level settings

```csharp
Table("tbl_Product");

// Chagen schema
Schema("OrderingSystem");

// Override the default loading behaviour
Not.LazyLoad();
```

#### ID Columns

```csharp
Id(x =>x.Id) 
  .GeneratedBy.HiLo("1000");
```

we are instructng NHibernate that the property Id of the Product entty shall be mapped as the Primary Key and that new IDs are to be automatcally generated by the HiLo generator of NHibernate.

If you want to use GUIDs as your IDs, then you can use the GuidComb generator and not the Guid generator, as it is optmized for database use. 

If the name is different

```csharp
Id(x =>x.ID, "PRODUCT_ID") 
  .GeneratedBy.HiLo("1000");

// or 
Id(x =>x.ID)
  .Column("PRODUCT_ID")
  .GeneratedBy.HiLo("1000")
```
When the object hasn't been saved

```csharp
Id(x =>x.ID, "PRODUCT_ID") 
  .GeneratedBy.HiLo("1000") 
  .UnsavedValue(-1);
```

#### Properties

```csharp
Map(x =>x.Name);

Map(x =>x.Name) 
  .Length(50) 
  .Not.Nullable();

Map(x =>x.Name, "PRODUCT_NAME") 
  .Length(50) 
  .Not.Nullable();

// mapping to an enum type
public enum ProductTypes
{
  ProductTypeA,
  ProductTypeB,
  ...
}

Map(x =>x.ProductType) 
  .CustomType<ProductTypes>();

// bool to char(1)
Map(x =>x.Discontinued) 
  .CustomType("YesNo");

```

#### References

To map a property that references another entity

```csharp
References(x =>x.Category);
```

```csharp
// mandatory reference, many-to-one
References(x =>x.Category) 
  .Not.Null();
```

Foreign key

If we do not specify it explicitly, then the name of the foreign key relatng the Product table to the Category table would be named Category_Id.

```csharp
// To specify the name of the foreign key
References(x =>x.Category) 
  .Not.Null() 
  .ForeignKey("CategoryId");

// Unique reference

References(x =>x.Category) 
  .Not.Null() 
  .Unique();
```

### Mapping our domain
### Use mapping conventions
### No mapping; is that possible?
### Using auto-mapping
### Using ConfORM to map our domain
### XML mapping
### Mapping a simple domain using XML


## 6. Sessions and Transactions
## 7. Testing, Profiling, Monitoring, and Logging
## 8. Configuration
## 9. Writing Queries
## 10. Validating the Data to Persist
## 11. Common Pitfalls - Things to avoid
