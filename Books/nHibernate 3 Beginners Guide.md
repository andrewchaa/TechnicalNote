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

*Hi/Lo algorithm
http://stackoverflow.com/questions/282099/whats-the-hi-lo-algorithm?lq=1

The basic idea is that you have two numbers to make up a primary key- a "high" number and a "low" number. A client can basically increment the "high" sequence, knowing that it can then safely generate keys from the entire range of the previous "high" value with the variety of "low" values.
For instance, supposing you have a "high" sequence with a current value of 35, and the "low" number is in the range 0-1023. Then the client can increment the sequence to 36 (for other clients to be able to generate keys while it's using 35) and know that keys 35/0, 35/1, 35/2, 35/3... 35/1023 are all available.
It can be very useful (particularly with ORMs) to be able to set the primary keys on the client side, instead of inserting values without primary keys and then fetching them back onto the client. Aside from anything else, it means you can easily make parent/child relationships and have the keys all in place before you do any inserts, which makes batching them simpler.

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

#### Collections

One-to-many relations:  One parent entty holds a collecton of 
child enttes

```csharp
HasMany(x =>x.LineItems);
```

*Inverse*

Between the order and a line item, there is an associaton. We use the inverse atribute to specify the "owner" of the associaton. The associaton can have only one owner, so one end has to be set to inverse while the other has to remain "non-inverse".

When we mark the collecton end as Inverse, then NHibernate will frst persist the entty that owns the collecton (the order), and will persist the enttes that are in the collecton aferwards (the line items), avoiding the additonal UPDATE statement.

```csharp
HasMany(x =>x.LineItems).Inverse();
```

*Cascade*

```csharp
HasMany(x =>x.LineItems) 
  .Inverse() 
  .Cascade.DeleteAllOrphans();    
```

#### Many to many

A book can be written by many authors and an author can write many books

```csharp
HasMany(x =>x.Author);

HasMany(x =>x.Author) 
  .Table("BookAuthor");
```

#### Mapping value objects

Use ComponentMap<T>

```csharp
public class NameMap : ComponentMap<Name>
{
  public void NameMap()
  { 
    Map(x =>x.LastName).Not.Null.Length(50);
    Map(x =>x.MiddleName).Length(50);
    Map(x =>x.FirstName).Not.Null.Length(50);
  }
}
```

Now map the name property of the Customer

```csharp
Component(x =>x.Name);
```

### Mapping our domain

```csharp
public class EmployeeMap : ClassMap<Employee>
{
  public EmployeeMap()
  {
    Id(x =>x.ID).GeneratedBy.HiLo("100"); // intervals of 100
    Component(x =>x.Name);
  }
}
```

*HiLo*
As soon as all numbers of the given interval are used, NHibernate updates the IDs table and takes the next interval of numbers.


```csharp
public class NameMap : ComponentMap<Name>
{
  public NameMap() 
  {
    Map(x =>x.LastName).Not.Nullable().Length(100);
    Map(x =>x.FirstName).Not.Nullable().Length(100);
    Map(x =>x.MiddleName).Length(100);
  }
}
```

```csharp
public class CustomerMap : ClassMap<Customer>
{
  public CustomerMap()
  {
    Id(x =>x.ID).GeneratedBy.HiLo("100");
    Component(x =>x.CustomerName);
    Map(x =>x.CustomerIdentifier).Not.Nullable().Length(50);
    HasMany(x =>x.Orders).Access.CamelCaseField().Inverse().Cascade.AllDeleteOrphan();
  }
}
```

```csharp
public class AddressMap : ComponentMap<Address>
{
  public AddressMap()
  {
    Map(x =>x.Line1).Not.Nullable().Length(50);
    Map(x =>x.Line2).Length(50);
    Map(x =>x.ZipCode).Not.Nullable().Length(10);
    Map(x =>x.City).Not.Nullable().Length(50);
    Map(x =>x.State).Not.Nullable().Length(50);
  }
}
```


```csharp
public class OrderMap : ComponentMap<Order>
{
  public OrderMap()
  {
    Map(x =>x.OrderDate).Not.Nullable();
    Map(x =>x.OrderTotal).Not.Nullable();
    References(x =>x.Customer).Not.Nullable(); //map the Customer property as a one-to-many reference

    HasMany(x =>x.LineItems).Access.CamelCaseField().Inverse().Cascade.AllDeleteOrphan();
  }
}
```

```csharp
public class LineItemMap : ComponentMap<LineItem>
{
  public LineItemMap()
  {
    References(x =>x.Order).Not.Nullable();
    References(x =>x.Product).Not.Nullable();
    Map(x =>x.Quantity).Not.Nullable();
    Map(x =>x.UnitPrice).Not.Nullable();
    Map(x =>x.Discount).Not.Nullable();
  }
}
```

```csharp
public class ProductMap : ComponentMap<Product>
{
  public ProductMap()
  {
    Map(x =>x.Name).Not.Nullable().Length(50);
    Map(x =>x.Description).Length(4000);
    Map(x =>x.UnitPrice).Not.Nullable();
    Map(x =>x.ReorderLevel).Not.Nullable();
    Map(x =>x.Discontinued);
  }
}
```

### Use mapping conventions

#### ID conventions

```csharp
public class MyIdConvention : IIdConvention
{
  public void Apply(IIdentityInstance instance)
  {
    instance.GeneratedBy.HiLo("100");
  }
}
```

Once this conventon is added to the confguraton, we can simplify the mapping of Id of all our enttes to this:

```csharp
Id(x =>x.Id);
```

Defne the column name of the Primary Key as being a combinaton of the entty name and ID

```csharp
instance.Column(string.Format("{0}Id", instance.EntityType.Name));
```

#### Property conventions

Always map propertes of name Name to the non-nullable database felds of maximum length 50

```csharp
public class MyNameConvention : IPropertyConvention
{
  public void Apply(IPropertyInstance instance)
  {
    if (instance.Name != "Name") return;
    instance.Length(50);
    instance.Not.Nullable();
  }
}
```

Map a property of type bool to a char(1) feld containing 'Y' or 'N' for true or false, respectvely.

```csharp
public class MyBoolConvention : IPropertyConvention
{
  public void Apply(IPropertyInstance instance)
  {
    if (instance.Type == typeof(bool))
    instance.CustomType("YesNo");
  }
}
```

#### Foreign keys conventions

Customer_Id by conventon. We can change the convention to be like CustomerId

```csharp
public class MyForeignKeyConvention : ForeignKeyConvention
{
  protected override string GetKeyName(Member property, Type type)
  {
    // property == null for many-to-many, one-to-many, join 
    // property != null for many-to-one
    var refName = property == null ? type.Name :property.Name;
    return string.Format("{0}Id", refName);
  }
}
```


### No mapping; is that possible?

It is recommended when starting a brand new project

#### Auto-mapping with Fluent NHibernate

Instruct what domain classes to be auto-mapped

```csharp
public class OrderingSystemConfiguration : DefaultAutomappingConfiguration
{
  public override bool ShouldMap(Type type)
  {
    return type.Namespace == typeof(Employee).Namespace;
  }
}
```

Instruct which are the value objects

```csharp
public override boolIsComponent(Type type)
{
  var componentTypes = new[] {typeof (Name), typeof (Address)};
  return componentTypes.Contains(type);
}
```

Configure

```csharp
var cfg = new OrderingSystemConfiguration();
var configuration = Fluently.Configure() 
  .Database(/* database config */) 
  .Mappings(m =>m.AutoMappings.Add( 
  AutoMap.AssemblyOf<Employee>(cfg)) 
  .BuildConfiguration();
```

### XML mapping

Add the two XML schema defniton fles nhibernate-configuration.xsd and nhibernate-mapping.xsd
The XML file should be Embedded Resource.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<hibernate-mapping xmlns="urn:nhibernate-mapping-2.2" 
  namespace="OrderingSystem.Domain" 
  assembly="OrderingSystem">

  <class name="Product" table="Product">

    <id name="Id" type="Int32" unsaved-value="null">
      <generator class="hilo"/>
    </id>
    <property name="Name" type="String">
      <column name="Name" length="255" sql-type="varchar" 
        not-null="true"/>
    </property>
  </class>  

</hibernate-mapping>


```

#### One-to-many relations

the Order property of the LineItem entity
```xml
<many-to-one name="Order" class="Order"  
  column="OrderId" not-null="true"/>
```

In the Order mapping file, create a bag

```xml
<bag name="LineItems" inverse="true" cascade="all-delete-orphan">
  <key column="OrderId"/>
  <one-to-many class="LineItem"/>
</bag>  
```

#### Many-to-many relations

From the Book side

```xml
<bag name="Authors" table="BookAuthor">
  <key column="BookId"/>
  <many-to-many class="Author" column="AuthorId" />
</bag>
```

From the Author side

```xml
<bag name="Books" table="BookAuthor">
  <key column="AuthorId"/>
  <many-to-many class="Book" column="BookId" />
</bag>
```

#### Mapping value objects

```xml
<component name="Name" class="Name">
  <property name="LastName" length="50" not-null="true"/>
  <property name="FirstName" length="50" not-null="true"/>
  <property name="MiddleName" length="50"/>
</component>
```

### Mapping a simple domain using XML

```xml
<?xml version="1.0" encoding="utf-8" ?>
<hibernate-mapping xmlns="urn:nhibernate-mapping-2.2" assembly="XmlMappingSample" namespace="XmlMappingSample.Domain">
  <class name="Customer">
    <id name="Id"><generator class="hilo"/></id>
    <property name="CustomerName"/>
  </class>
</hibernate-mapping>


<?xml version="1.0" encoding="utf-8" ?>
<hibernate-mapping xmlns="urn:nhibernate-mapping-2.2" assembly="XmlMappingSample" namespace="XmlMappingSample.Domain">
  <class name="LineItem">
    <id name="Id"><generator class="hilo"/></id>
    <property name="Quantity"/>
    <property name="UnitPrice"/>
    <property name="ProductCode"/>
  </class>
</hibernate-mapping>

<?xml version="1.0" encoding="utf-8" ?>
<hibernate-mapping xmlns="urn:nhibernate-mapping-2.2" assembly="XmlMappingSample" namespace="XmlMappingSample.Domain">
  <class name="Order">
    <id name="Id"><generator class="hilo"/></id>
    <property name="OrderDate"/>
    <many-to-one name="Customer" column="CustomerId"/>
    <bag name="LineItems" inverse="true" >
      <key column="OrderId"/>
      <one-to-many class="LineItem"/>
    </bag>
  </class>
</hibernate-mapping>
```

## 6. Sessions and Transactions

### What are sessions and transactions

Through a session object, we are able to communicate with a database and execute various operations on it.
A transaction object make managing multiple operations as a unity

#### Session
an abstract or virtual conduit for the database

#### Transaction
Allow us to execuite a number of tasks as a single unit of work.

An operation is a transaction if and only if it fulfils ACID (Atomic, Consistent, Isolated, Durable)

* Atomic – the operaton can only be executed as a whole and not broken apart into smaller units of executon.
* Consistent – the outcome of the operaton has to leave the system in a consistent state.
* Isolated – the operaton has to run in isolaton. The outside world cannot see the result of an unfnished transacton.
* Durable – the outcome of the operaton is permanent.

### The session factory

Create as many sessions as required
Very expensive to create and specific to a database
Thread-safe. Code on different threads can use the same session factory

### Creating your first session

```csharp
using (var session = sessionFactory.OpenSession())
{
  using (var transaction = session.BeginTransaction())
  {
    // create, update, delete, or read data
    transaction.Commit();
  }
}

```

#### Adding new data to the database

```csharp
var newProductId = (int)session.Save(newProduct);
```

#### Reading data from the database

```csharp
var product = session.Get<Product>(1);
```

To load the list of all Categories

```csharp
var allCategories = session.Query<Category>().ToList();
```

LINQ to NHibernate returns a list of type IQueryable<Category>, which is lazy evaluated. If we want NHibernate to eagerly 
load all records, then we can force it to do so by calling ToList().

#### Get versus Load

Get loads an entity

```csharp
var product = session.Get<Product>(1);
```

Load is for lazy loading. It doesn't retrieve the entity from databaes but create an proxy object.

```csharp
var product = session.Load<Product>(1):
```

We get a proxied Product entty whose ID is equal to 1. The moment our code tries to access any property other than the ID, NHibernate loads the entty from the database.

When to use load, then?

```csharp
var product = session.Get<Product>(productId);
product.Category = session.Load<Category>(newCategoryId);
```

A product belongs to a Category and thus, the Product entty has a property of type Category. However, when manipulatng products, we don't want to change categories at the same tme, only use them.

#### Updating existing data

The moment the session object is fushed, the changes are automatcally persisted by NHibernate.

```csharp
using (var session = sessionFactory.OpenSession())
using (var tx = session.BeginTransaction())
{
  var product = session.Get<Product>(1);
  product.UnitPrice = 10.55m;
  // new unit price
  tx.Commit();
}
```

#### Deleting data

To delete an existng record in the database, we have to frst load it and can then pass the object to the Delete method of the session object

```csharp
var productToDelete = session.Load<Product>(productId);
session.Delete(productToDelete);
```

The previous optmizaton is not applicable in the case that the entty to be deleted contains dependent enttes and/or collectons of child enttes that are mapped with cascading delete constraints. In such a case, NHibernate will have to load the entty into memory.

### First level cache or identity map

The first level cache is created when the session object is created and gets destroyed when the session object is disposed.

An entity is loaded from database by using its id. NHibernate puts this entity into the first level cache. When the system tries to load the same entty again from the database, then the session object frst consults its cache. If the entty already exists in the cache, then NHibernate returns the cached instance. Only if the cache does not yet contain the entty with the requested ID, does the NHibernate session object load the entty from the database.

* Applicaton requests a product with ID = 1 from the session
* Session asks the frst level cache: "Do you contain product with ID 1?"
* First level cache responds: "No"
* Session loads product with ID 1 from the database
* Session puts product into the frst level cache, assigning it as a key for the value of its ID (= 1)
* Session returns product instance to applicaton
* Applicaton executes more operatons
* Applicaton again requests a product with ID = 1 from the session
* Session asks frst level cache: "Do you contain product with ID 1?"
* First level cache responds: "Yes"
* Session loads product with ID 1 from the cache using the ID as a key and returns it to the applicaton

This frst level cache is also called an identty map.

#### Clearing the cache

Remove an entity from the cache

```csharp
session.Evict(product);
```

To completely clean the cache

```csharp
session.Clear();
```

### No database operation without a transaction

Whenever we execute operatons that manipulate data in a database, we should wrap these operatons inside a transacton:

```csharp
using( var session = sessionFactory.OpenSession())
{
  using (var transaction = session.BeginTransaction())
  {
    // code that manipulates data…
  }
}
```

#### Transaction when querying data?

For optmal performance and predictability of the result, we should also wrap read operatons into an explicit transacton. In fact, this is a recommended practce when working with NHibernate.

### NHibernate session versus database session

ADO.NET Connection

An open connection to a database that lasts as long as it is open.

```csharp
using (var connection = new SqlConnection("..."))
{
  connection.Open();
  //... write to or read from DB
  connection.Close();
}
```

A NHibernate session is slightly different. It can span multiple ADO.NET connections and a physical connection is not maintained during its life. NHibernate session opens ADO.NET connection only if a write or read operation is taking place 

* it manages the first level cache
* it's a unit of work container and keeps track of all the changes

### Session management

#### Web-based applications

A session object is cheap to create, but the session factory isn't. So create the session factory only once during the life cycle of the application. The session factory is thread-safe. 

The common pattern is to create NHibernate sessions per request. A new session object is created at the beginning of a new web request and then flushed and disposed at the end of the web request. Store the session object in the IoC container

### Implementing session management for a web application

```csharp
public class Product
{
  public virtual int Id { get; set; }
  public virtual string Name { get; set; }
}

public class ProductMap : ClassMap<Product>
{
  public ProductMap()
  {
    Id(x =>x.Id).GeneratedBy.HiLo("1000");
    Map(x =>x.Name);
  }
}

public class SessionProvider
{
  public static SessionProvider Instance { get; private set; }

  private static ISessionFactory sessionFactory;
  
  static SessionProvider()
  {
    var provider = new SessionProvider();
    provider.Initialize();
    Instance = provider;
  }

  private SessionProvider()
  {
  }

  private void Initialize()
  {
    const string connString = 
      "server=.\\SQLEXPRESS;database=SilverlightSample;" + 
      "user id=sa;password=sa;";
    var configuration = Fluently.Configure() 
      .Database(MsSqlConfiguration.MsSql2008 
      .ConnectionString(connString) 
      .ShowSql()) 
      .Mappings(m =>m.FluentMappings 
      .AddFromAssemblyOf<Product>()) 
      .BuildConfiguration();

     var exporter = new SchemaExport(configuration);
      exporter.Execute(true, true, false);
      sessionFactory = configuration.BuildSessionFactory();
    }

  public ISession OpenSession()
  {
    return sessionFactory.OpenSession();
  }

}
```

### Unit of Work

A Unit of Work (UoW) is used to keep track of everything that happens during a business transaction and that affects the database. It keeps track of every single step needed to update the database once the business transaction is completed.
In NHibernate, the session object is a UoW container. Only when the session is being flushed, will the database be altered. 

### Handling exception

In case of an exception during a database operation, the session object is in an incosistent state and should not be used any more. Immediately rollback the active transaction, dispose the current session and start over.

```csharp
var session = sessionFactory.OpenSession();
var transaction = session.BeginTransaction();
try
{
  // do some work
  ...
  transaction.Commit();
}
catch (Exception e)
{
  transaction.Rollback();
  throw;
}
finally
{
  session.Close();
}  
```

### Second level cache

when we want to cache data globally. The second level cache is defned per session factory and lives as long as the session factory is not disposed.  Once an entty is loaded by its unique ID and the second level cache is actve, the entty is available for all other sessions (of the same session factory). To enable the second level cache, we have to defne which cache provider we want to use. There exist various implementatons of a second level cache.

```csharp
using (var session1 = sessionFactory.OpenSession())
{
  var product = session1.Get<Product>(1);
}
using (var session2 = sessionFactory.OpenSession())
{
  var product = session2.Get<Product>(1); // this will come from the 2nd level cache
}

// for extra mapping
Cache.ReadWrite();
```
#### Cache region

Use region to only clear part of the 2nd level cache

```csharp
sessionFactory.EvictQueries("My Region"); // to clear the cache region
```

#### Second level cache implementation

* SysCache
* SysCache2
* Velocity
* Prevalence 
* MemCache

### Using a second level cache

```csharp
public class Product
{
  public virtual int Id { get; set; }
  public virtual string Name { get; set; }
  public virtual decimal UnitPrice { get; set; }
  public virtual int ReorderLevel { get; set; }
  public virtual bool Discontinued { get; set; }
}

public class ProductMap : ClassMap<Product>
{
  publicProductMap()
  {
    Cache.ReadWrite();
    Id(x =>x.Id).GeneratedBy.HiLo("1000");
    Map(x =>x.Name);
    Map(x =>x.UnitPrice);
    Map(x =>x.ReorderLevel);
    Map(x =>x.Discontinued);
  }
}

//Program.cs
private static ISessionFactory sessionFactory;

private static void ConfigureSystem()
{
  const string connString= 
    "server=.\\SQLEXPRESS;database=SecondLevelCacheSample;" + 
    "integrated security=SSPI;";

var configuration = Fluently.Configure() 
  .Database(MsSqlConfiguration.MsSql2008 
  .ConnectionString(connString) 
  .ShowSql) 
  .Mappings(m =>m.FluentMappings 
  .AddFromAssemblyOf<Product>()) 
  .BuildConfiguration();

  configuration.Properties["cache.provider_class"] = 
     "NHibernate.Cache.HashtableCacheProvider"; // Don't use this on production
  configuration.Properties["cache.use_second_level_cache"] = 
     "true";    

  var exporter = new SchemaExport(configuration);
  exporter.Execute(true, true, false);   

  sessionFactory = configuration.BuildSessionFactory();
}

private static void TestLoadEntity()
{
  int productId;
  var product = new Product
  {
    Name = "Apple",
    UnitPrice = 1.55m,
    ReorderLevel = 100,
    Discontinued = false
  };

  using (var session = sessionFactory.OpenSession())
  using (var tx = session.BeginTransaction())
  {
    productId = (int) session.Save(product);
    tx.Commit();
  }

  using (var session1 = sessionFactory.OpenSession())
  {
    var product1 = session1.Get<Product>(productId);
  }
  using (var session2 = sessionFactory.OpenSession())
  {
    var product2 = session2.Get<Product>(productId);
  }

}

static void Main()
{
  ConfigureSystem();
  TestLoadEntity();
  Console.Write("\r\nHit enter to exit:");
  Console.ReadLine();
}
```

## 7. Testing, Profiling, Monitoring, and Loggi ng

### Why do we need tests?

Safety net for refactoring

### What should we test?

* Complex mathematcal or statstcal algorithms
* Involved business logic or business processes
* Code that is used as the foundaton or framework in an applicaton
* The mapping of the domain model to the underlying database schema
* Complex database queries

### What about the database?

Ways to set up test data

* In the frst approach, we wipe out the database schema each tme, recreate it, and fll it with the base data we need prior to each test
* We write compensatng code that removes the changes done by our tests in the clean-up method of our test class
* We wrap each test inside a transacton that is rolled back at the end of the corresponding test
* We use a snapshot of the database that is restored afer each test run

Download and use Sqlite for testing.

### Preparing our environment for testing

Download NUnit

Given, When, Then skeleton

```csharp
public class SpecificationBase
{
  [TestFixtureSetUp]
  public virtual void TestFixtureSetUp()
  {
    BeforeAllTests();
  }
  [SetUp]
  public void SetUp()
  {
    Given();
    When();
  }
  [TearDown]
  public void TearDown()
  {
    CleanUp();
  }

  [TestFixtureTearDown]
  public void TestFixtureTearDown()
  {
    AfterAllTests();
  }

  protected virtual void BeforeAllTests(){}
  protected virtual void Given(){}
  protected virtual void When(){}
  protected virtual void CleanUp(){}
  protected virtual void AfterAllTests(){}
}  

public class ThenAttribute : TestAttribute {}
```

```csharp
[TestFixture]
public class when_creating_a_name_value_object 
  : SpecificationBase
{
  private string lastName;
  private string firstName;
  private string middleName;
  private Name result;
  protected override void Given()
  {
    lastName = "Schenker";
    firstName = "Gabriel";
    middleName = "N.";
  }
  protected override void When()
  {
    result = new Name(lastName, firstName, middleName);
  }
    protected override void When()
  {
    result = new Name(lastName, firstName, middleName);
  }
  [Then]
  public void it_should_populate_the_last_name_property()
  {
    Assert.Equals(result.LastName, lastName);
  }
  [Then]
  public void it_should_populate_the_first_name_property()
  {
    Assert.Equals(result.FirstName, firstName);
  }
  [Then]
  public void it_should_populate_the_middle_name_property()
  {
    Assert.Equals(result.MiddleName, middleName);
  }
}
```

#### Testing the mapping

Create an entity and populate it with precanned values

```csharp
var product = new Product
{
  Name = "Apple",
  Description = "Description for apple",
  UnitPrice = 1.50m,
  ReorderLevel = 10,
  Discontinued = true,
};

session.Save(product);
session.Flush(); //Flush() enforces the writing of all changes to the database
```

Now load the data back.

```csharp
session.Evict(product); // eliminates the entity from the first level cache.
var fromDb = session.Get<Product>(product.Id);

Assert.That(fromDb, Is.Not.Null);
```

Compare the newly hydrated entity property-by-property with the new entity

```csharp
Assert.That(fromDb.Name, Is.EqualTo(product.Name));
Assert.That(fromDb.Description, Is.EqualTo(product.Description));
Assert.That(fromDb.UnitPrice, Is.EqualTo(product.UnitPrice));
Assert.That(fromDb.ReorderLevel, Is.EqualTo(product.ReorderLevel));
Assert.That(fromDb.Discontinued, Is.EqualTo(product.Discontinued));
```

#### Testing the mapping with Fluent NHibernate

PersistenceSpecification<T>

```csharp
new PersistenceSpecification<Product>(session) 
  .CheckProperty(x => x.Name, "Apple") 
  .CheckProperty(x => x.Description, "Description for apple") 
  .CheckProperty(x => x.UnitPrice, 1.50m) 
  .CheckProperty(x => x.ReorderLevel, 10) 
  .CheckProperty(x => x.Discontinued, true) 
  .VerifyTheMappings();
```

### Creating the base for testing

```csharp
public class ThenAttribute : TestAttribute
{ }

public abstract class SpecificationBase
{
  [TestFixtureSetUp]
  public void TestFixtureSetUp()
  {
    BeforeAllTests();
  }
  [SetUp]
  public void SetUp()
  {
    Given();
    When();
  }
  [TearDown]
  public void TearDown()
  {
    CleanUp();
  }
  [TestFixtureTearDown]
  public void TestFixtureTearDown()
  {
    AfterAllTests();
  }
  protected virtual void BeforeAllTests(){ }
  protected virtual void Given(){ }
  protected virtual void When(){ }
  protected virtual void CleanUp(){ }
  protected virtual void AfterAllTests(){ }
}      

public abstract class MappingSpecificationBase : SpecificationBase
{
  protected Configuration configuration;
  private ISessionFactory sessionFactory;
  protected ISession session;
  protected override void BeforeAllTests()
  {
    configuration = Fluently.Configure() 
      .Database(DefineDatabase) 
      .Mappings(DefineMappings) 
      .BuildConfiguration();
    CreateSchema(configuration);
    sessionFactory = configuration.BuildSessionFactory();
  }
  protected ISession OpenSession()
  {
    return sessionFactory.OpenSession();
  }
  protected override void Given()
  {
    base.Given();
    session = OpenSession();
  }
  protected abstract IPersistenceConfigurer DefineDatabase();
  protected abstract void DefineMappings(MappingConfiguration m);
  protected virtual void CreateSchema(Configuration cfg){}
}

[TestFixture]
public class entity_mapping_spec : MappingSpecificationBase
{
  protected override IPersistenceConfigurer DefineDatabase()
  {
    return MsSqlConfiguration.MsSql2008
    .ConnectionString("server=.\\SQLEXPRESS;"+ 
      "database=NH3BeginnersGuide;"+ 
      "integrated security=SSPI;");
  }
  protected override void DefineMappings(MappingConfiguration m)
  {
    m.FluentMappings.AddFromAssemblyOf<ProductMap>();
  }
  protected override void CreateSchema(Configuration cfg)
  {
    new SchemaExport(cfg).Execute(false, true, false);
  }

  [Then]
  public void it_should_correctly_map_a_product()
  {
    new PersistenceSpecification<Product>(session)
    .CheckProperty(x => x.Name, "Apple")
    .CheckProperty(x => x.Description, "Some description")
    .CheckProperty(x => x.UnitPrice, 1.50m)
    .CheckProperty(x => x.ReorderLevel, 10)
    .CheckProperty(x => x.Discontinued, true)
    .VerifyTheMappings();
  }
}
```

### Using SQLite in our tests

```csharp
[TestFixture]
public class entity_mapping_spec_for_sqlite  : MappingSpecificationBase
{
  protected override IPersistenceConfigurer DefineDatabase()
  {
    return SQLiteConfiguration.Standard 
      .InMemory() 
      .ShowSql();
  }

  protected override void DefineMappings(MappingConfiguration m)
  {
    m.FluentMappings.AddFromAssemblyOf<ProductMap>();
  }

  [Then]
  public void it_should_correctly_map_a_product()
  {
    new PersistenceSpecification<Product>(session)
    .CheckProperty(x => x.Name, "Apple")
    .CheckProperty(x => x.Description, "Some description")
    .CheckProperty(x => x.UnitPrice, 1.50m)
    .CheckProperty(x => x.ReorderLevel, 10)
    .CheckProperty(x => x.Discontinued, true)
    .VerifyTheMappings();
  }

}
```

#### Testing Queries

Use Linq to NHibernate driver

```csharp
public interface IRepository<T>
{
  IQueryable<T> Query();
  T Get(int id);
  T Load(int id);
  // possibly other members of ISession...
}

public class Repository<T> : IRepository<T>
{
  private readonly ISession session;

  public Repository(ISession session)
  {
    this.session = session;
  }
  public IQueryable<T> Query() { return session.Query<T>(); }
  public T Get(int id) { return session.Get<T>(id); }
  public T Load(int id) { return session.Load<T>(id); }
}

```

```csharp
public class GetAllProductsToOrderQueryHandler
{
  private readonly IRepository<Product> repository;
  public GetAllProductsToOrderQueryHandler( 
    IRepository<Product> repository)
  {
    this.repository = repository;
  }
  public IEnumerable<Product> Execute()
  {
    return repository.Query() 
      .Where(p => !p.Discontinued && p.UnitsOnStock < p.ReorderLevel) 
      .OrderBy(p => p.Name) 
      .ToArray();
  }
}
```

```csharp
// Stub

public class StubbedRepository : IRepository<Product>
{
  public IQueryable<Product> Query()
  {
    return new[]
    {
      new Product {Name = "Pineapple", UnitPrice = 1.55m,
      ReorderLevel = 10, UnitsOnStock = 20,
      Discontinued = false},
      new Product {Name = "Hazelnut", UnitPrice = 0.25m,
      ReorderLevel = 100, UnitsOnStock = 20,
      Discontinued = true},
      new Product {Name = "Orange", UnitPrice = 1.15m,
      ReorderLevel = 20, UnitsOnStock = 10,
      Discontinued = false},
      new Product {Name = "Apple", UnitPrice = 1.15m,
      ReorderLevel = 20, UnitsOnStock = 50,
      Discontinued = false},
    }
    .AsQueryable();
  }

  public Product Get(int id)
  {
    throw new NotImplementedException();
  }
  public Product Load(int id)
  {
    throw new NotImplementedException();
  }
}  

public class when_querying_products_to_reorder : SpecificationBase
{
  private IRepository<Product> repository;
  private GetAllProductsToOrderQueryHandler sut;
  private IEnumerable<Product> result;
  protected override void Given()
  {
    repository = new StubbedRepository();
    sut = new GetAllProductsToOrderQueryHandler(repository);
  }
  protected override void When()
  {
    result = sut.Execute();
  }

  [Then]
  public void it_should_only_return_active_products()
  {
    Assert.That(result.Any(p => p.Discontinued), Is.False);
  }

  [Then]
  public void it_should_only_return_products_to_reorder()
  {
    Assert.That(result.All(p => p.ReorderLevel > p.UnitsOnStock), 
      Is.True);
  }

  [Then]
  public void it_should_return_an_ordered_list()
  {
    Assert.That(result.First().Name, Is.EqualTo("Apple"));
    Assert.That(result.Last().Name, Is.EqualTo("Orange"));
  }

}


```
### Logging

For "post-mortem" analysis

Log4Net is one of the best known logging framework.

### Adding logging to our application

#### Configuration

1. Add a definition for the existence of of a log4net section

```xml
<?xml version="1.0"?>
<configuration>
  <configSections>
    <section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler, log4net"/>
  </configSections>
```

2. Add log4net section

```xml
<log4net>
  <appender name="ConsoleAppender" type="log4net.Appender.ConsoleAppender">
    <layout type="log4net.Layout.PatternLayout">
      <conversionPattern value="[%C.%M] %-5p %m%n"/>
    </layout>
  </appender>
</log4net>
```

3. Instruct log4net which appender it should use.
4. Define what level of debugging we want to enable

```xml
<root>
  <appender-ref ref="ConsoleAppender"/>
  <level value="DEBUG"/>
</root>

</configuration>
```

```csharp
[assembly: log4net.Config.XmlConfigurator(Watch = true)]

private static readonly ILog log =  
  LogManager.GetLogger(typeof(Program));

log.Debug("This is a Debug message.");
log.Info("This is a Info message.");
log.Warn("This is a Warn message.");
log.Error("This is a Error message.");
log.Fatal("This is a Fatal message.");

```

### Enable logging in NHibernate

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <configSections>
    <section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler, log4net" />
  </configSections>
  <log4net debug="false">
    <appender name="console" type="log4net.Appender.ConsoleAppender, log4net">
      <layout type="log4net.Layout.PatternLayout,log4net">
        <param name="ConversionPattern" value="%d [%t] %-5p %c - %m%n" />
      </layout>
    </appender>
    <logger name="NHibernate">
      <level value="WARN"/>
    </logger>
    <logger name="NHibernate.SQL">
      <level value="ALL"/>
    </logger>
    <root>
      <level value="DEBUG" />
      <appender-ref ref="console" />
    </root>
  </log4net>
</configuration>
```

```csharp
protected override void BeforeAllTests()
{
  base.BeforeAllTests();
  log4net.Config.XmlConfigurator.Configure();
}
```

### Monitoring and profiling

There are a few ways to monitor the application that uses NHibernate.

* Alalysing log files
* Using Sql Server Profiler
* NHibernate Profiler

#### NHibernate Profiler

One of the best tools and a real-time visual debugger. http://www.nhprof.com

### Adding NHibernate Profiler support

1. Download NHibernate Profiler and install it
2. Request a trial license and receive it via email
3. Create a new XML file and copy and paste the license key into it. Select the xml file when asked.
4. Copy HibernatingRhinos.Profiler.Appender.dll and reference it in the application
5. Put it before test code. HibernatingRhinos.Profiler.Appender.NHibernate.NHibernateProfiler.Initialize();

## 8. Configuration

### Elements of the configuration

#### Which dadabase do we want to use?

```xml
<property name="connection.provider">
  NHibernate.Connection.DriverConnectionProvider
</property>
<property name="connection.driver_class">
  NHibernate.Driver.SqlClientDriver
</property>
<property name="dialect">
  NHibernate.Dialect.MsSql2008Dialect
</property>
<property name="connection.connection_string">
  server=.\SQLEXPRESS;database=sample;integrated security=SSPI;
</property>
```

The following elements can thus be omited:
* NHibernate now implements its own proxy generator. No additonal external dependency is needed, and thus the confguraton does not need to defne the proxy factory factory element.
* The DriverConnectionProvider class is the default connecton provider, and thus no explicit confguraton is needed.
* Each dialect has associated a default driver, and again we can omit this declaraton.

#### What byte code provider and proxy factory?

When using lazy loading, we need to define which proxy generator NHibernate shall use. Currently,

* [Castle] (http://www.castleproject.org/)
* [LinFu] (http://code.google.com/p/linfu/)
* [Spring.NET] (http://www.springframework.net/)

```xml
<property name="proxyfactory.factory_class">
  NHibernate.ByteCode.Castle.ProxyFactoryFactory, NHibernate.ByteCode.Castle
</property>
```

From NHibernate 3.2, it has its own version of byte code proxy, which is a modified version of LinFu. So no need to configure it.

#### Mappings

```csharp
configuration.AddClass(typeof (Product));

// or

configuration.AddAssembly(typeof (Product).Assembly);
```

### Configuring NHibernate using XML

1. Reference NHibernate.dll, NHibernate.ByteCode.Castle.dll, and System.Data.SQLite.dll 
2. Add the two fles nhibernate-configuration.xsd and nhibernate-mapping.xsd
3. Domain class

```csharp
public class Account
{
  public int Id { get; set; }
  public string Name { get; set; }
  public decimal Balance { get; set; }
  public string CurrencyCode { get; set; }
  public bool IsActive { get; set; }
}
```

4. hbm for the class

```xml
<?xml version="1.0" encoding="utf-8" ?>
<hibernate-mapping xmlns="urn:nhibernate-mapping-2.2"  
  assembly="XmlConfigurationSample"  
  namespace="XmlConfigurationSample" >
  <class name="Account" lazy="false">
    <id name="Id">
      <generator class="hilo"/>
    </id>
    <property name="Name"/>
    <property name="Balance"/>
    <property name="CurrencyCode"/>
    <property name="IsActive"/>
  </class>
</hibernate-mapping>
```

Set the build action to Embedded Resource

5. Configure NHibernate in Application config

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <configSections>
    <section name="hibernate-configuration" type= 
      "NHibernate.Cfg.ConfigurationSectionHandler, NHibernate" />
  </configSections>
</configuration>

<hibernate-configuration xmlns="urn:nhibernate-configuration-2.2">
  <session-factory>
    <property name="connection.provider">
      NHibernate.Connection.DriverConnectionProvider
    </property>
    <property name="connection.driver_class">
      NHibernate.Driver.SQLite20Driver
    </property>
    <property name="dialect">
      NHibernate.Dialect.SQLiteDialect
    </property>
    <property name="proxyfactory.factory_class">
      NHibernate.ByteCode.Castle.ProxyFactoryFactory,  
      NHibernate.ByteCode.Castle
    </property>
    <property name="connection.connection_string_name">
      Sample
    </property>
    <property name="show_sql">
      true
    </property>
  </session-factory>
</hibernate-configuration>
```

6. Set the connection string

```xml
<connectionStrings>
  <add name="Sample"  
    connectionString= 
    "data source=xmlconfig.dbf;version=3;new=true;"/>
</connectionStrings>
```

7. In the main method

```csharp
using NHibernate.Cfg;
using NHibernate.Tool.hbm2ddl;

var configuration = new Configuration();
configuration.AddAssembly(typeof(Account).Assembly);

private static void BuildSchema(Configuration configuration)
{
  new SchemaExport(configuration).Execute(true, true, false);
}

BuildSchema(configuration);
var factory = configuration.BuildSessionFactory();

using (var session = factory.OpenSession())
{
  var account = new Account
  {
    Name = "USB-10234-R1",
    Balance = 1545.55m,
    CurrencyCode = "CHF",
    IsActive = true
  };
  session.Save(account);

}

session.Flush();
session.Clear();

var fromDb = session.Get<Account>(account.Id);
System.Console.Write("\r\n\nHit enter to exit:");
System.Console.ReadLine();
```

### Configuring NHibernate in code

1. Add reference to NHibernate.dll and NHibernate.ByteCode.Castle.dll
2. Add nhibernate-configuration.xsd and nhibernate-mapping.xsd
3. Product class

```csharp
public class Product
{
  public int Id { get; set; }
  public string Name { get; set; }
  public decimal UnitPrice { get; set; }
  public int ReorderLevel { get; set; }
  public int UnitsOnStock { get; set; }
  public bool Discontinued { get; set; } 
}
```

4. Product.hbm.xml

```xml
<?xml version="1.0" encoding="utf-8" ?>
<hibernate-mapping xmlns="urn:nhibernate-mapping-2.2" assembly="CodeConfigurationSample" 
  namespace="CodeConfigurationSample" >
  <class name="Product" lazy="false">
    <id name="Id">
      <generator class="hilo"/>
    </id>
    <property name="Name"/>
    <property name="UnitPrice"/>
    <property name="ReorderLevel"/>
    <property name="UnitsOnStock"/>
    <property name="Discontinued"/>
  </class>
</hibernate-mapping>
```

5. Program.cs

```csharp
using NHibernate.Cfg;
using NHibernate.Tool.hbm2ddl;

private static Configuration GetConfiguration()
{
}
```

6. Connection Provider

```csharp
  var cfg = new Configuration();
  cfg.Properties.Add(Environment.ConnectionProvider, typeof(DriverConnectionProvider).FullName);
```

7. Connection driver and dialect

```csharp
  cfg.Properties.Add(Environment.ConnectionDriver, typeof(SqlClientDriver).FullName);
  cfg.Properties.Add(Environment.Dialect, typeof(MsSql2008Dialect).FullName);
```

8. Proxy factory factory
The desired class does not live in the NHibernate assembly but in the NHibernate.ByteCode.Castle assembly, we cannot use the FullName this tme, but have to use the property AssemblyQualifiedName

```csharp
  cfg.Properties.Add(Environment.ProxyFactoryFactoryClass, typeof(ProxyFactoryFactory).AssemblyQualifiedName);
```

9. Connection string

```csharp
  cfg.Properties.Add(Environment.ConnectionString, @"server=.\SQLEXPRESS;database= CodeConfigurationSample;" + 
  "integrated security=SSPI;");
```

10. Log the sql

```csharp
cfg.Properties.Add(Environment.ShowSql, "true");
```

11. Declarle which mapping to use

```csharp
cfg.AddClass(typeof (Product));
```

### Using Loquacious to configure NHibernate

1. Connection string in app.config

```csharp
<?xml version="1.0"?>
<configuration>
  <connectionStrings>
    <add name="Sample"  
      connectionString="data source=loquaciousConfig.dbf; 
      version=3;new=true;"/>
  </connectionStrings>
</configuration>
```

2. Define mapping (Person.hbm.xml.)

```xml
<?xml version="1.0" encoding="utf-8" ?>
<hibernate-mapping xmlns="urn:nhibernate-mapping-2.2" 
  assembly="LoquatiousConfigurationSample" 
  namespace="LoquatiousConfigurationSample" >
  <class name="Person" lazy="false">
    <id name="Id">
      <generator class="guid.comb"/>
    </id>
    <component name="Name">
      <property name="FirstName"/>
      <property name="LastName"/>
      <property name="MiddleName"/>
    </component>
    <property name="SSN"/>
    <property name="Birthdate"/>
  </class>
</hibernate-mapping>    
```

3. Monitor with NHibernate Profiler

```csharp
HibernatingRhinos.Profiler.Appender.NHibernate.NHibernateProfiler.Initialize();
```

4. Configuration

```csharp
private static Configuration GetConfiguration()
{
  var cfg = new Configuration();
  cfg.SessionFactory() 
    .Proxy 
      .Through<ProxyFactoryFactory>() 
      .Mapping 
      .UsingDefaultCatalog("sampleCatalog") 
      .UsingDefaultSchema("dbo") 
      .Integrate 
      .LogSqlInConsole() 
      .Using<SQLiteDialect>() 
      .Connected 
      .Through<DriverConnectionProvider>() 
      .By<SQLite20Driver>() 
      .ByAppConfing("Sample");
  cfg.AddAssembly(typeof(Person).Assembly);
  return cfg;
}

// or with lambda
var cfg = new Configuration();
cfg.Proxy(p => { p.ProxyFactoryFactory<ProxyFactoryFactory>(); });
cfg.Mappings(m =>
{
  m.DefaultCatalog = "NH3BeginnersGuide";
  m.DefaultSchema = "dbo";
});
cfg.DataBaseIntegration(db =>
{
  db.ConnectionProvider<DriverConnectionProvider>();
  db.Driver<SqlClientDriver>();
  db.Dialect<MsSql2008Dialect>();
  db.ConnectionStringName = "Sample2";
  db.LogSqlInConsole = true;
});

```

## 9. Writing Queries

### How can we get to our data?

* HQL: references the entities in the domain model and query the data
* Criteria Query: string based query
* QueryOver: type-safe variant of Criteria query
* Linq Provider for NHibernate: Created on top of Criteria. The new one is built upon HQL
* Native SQL

### The LINQ to NHibernate provider

#### Defining the root of the query

```csharp
var list = session.Query<Product>();
```

The query is not exeuted immediately, until we access and use the data. It's called *lazy evaluation*.

```csharp
var list = session.Query<Product>();
foreach(var product in list)
{
  // do something with product
}
```

It generateds the sql like this:

```sql
SELECT [list of all mapped felds] FROM PRODUCT
```

#### Limiting the number of records returned

use take()

```csharp
var orders = session.Query<Order>().Take(200);
```

#### Filtering a set of records

Use Where(Func<T, bool> predicate)

```csharp
var discontinuedProducts = session 
  .Query<Product> 
  .Where(p => p.Discontinued);
```

This filter function is sometimes called a *reduce function* or operation.

#### Mapping a set of records

Select(Func<TSource, TResult> mapper)

```csharp
public class NameID
{
  public int Id {get; set;}
  public string Name {get; set;}
}

var discontinuedProducts = session 
  .Query<Product> 
  .Where(p => p.Discontinued) 
  .Select(p => new NameID{Id = p.Id, Name = p.Name});
```

#### Sorting the result set

```csharp
var people = session.Query<Person>() 
  .OrderBy(p => p.LastName);
```

Multiple sorts

```csharp
var people = session.Query<Person>() 
  .OrderBy(p => p.LastName) 
  .ThenBy(p => p.FirstName);

var people = session.Query<Person>() 
  .OrderByDescending(p => p.LastName) 
  .ThenByDescending(p => p.FirstName);  
```

#### Grouping records

```csharp
var personsPerLetter = session.Query<Person>() 
  .GroupBy(p => p.LastName.SubString(0,1)) 
  .Select(g => new { Letter = g.Key, Count = g.Count() });
```

#### Forcing a Linq query to execute immediately

End a query with a call to ToArray() or ToList()

```csharp
var products = session.Query<Product>() 
  .Where(p => p.ReorderLevel > p.UnitsOnStock) 
  .ToArray()
```

ToDictionary() enables to retrieve a list of entities from the database and store them in a collection.

```csharp
var personsPerLetter = session.Query<Person>() 
  .GroupBy(p => p.LastName.SubString(0,1)) 
  .Select(g => new { Letter = g.Key, Count = g.Count() }) 
  .ToDictionary(x => x.Letter, x => x.Count);
```

### Creating a report using LINQ to NHibernate

1. Add the two fles nhibernate-configuration.xsd and nhibernate-mapping.xsd, located in the lib folder to this Schema soluton folder.

2. Domain classes

```csharp
public class Star
{
  public virtual Guid Id { get; set; }
  public virtual string Name { get; set; }
  public virtual IList<Planet> Planets { get; set; }
  public virtual StarTypes Class { get; set; }
  public virtual SurfaceColor Color { get; set; }
  public virtual double Mass { get; set; }
}

public class Planet
{
  public virtual Guid Id { get; set; }
  public virtual string Name { get; set; }
  public virtual bool IsHabitable { get; set; }
  public virtual Star Sun { get; set; }
}

public enum SurfaceColor
{
  Blue, BueToWhite, WhiteToYellow, OrangeToRed, Red
}

public enum StarTypes
{
  O, B, A, F, G, K, M
}
```

3. Mapping xml

* <generator class="guid.comb"/>
* <property name="Class" type="StarTypes"/>
* <bag name="Planets" inverse="true" cascade="all-delete-orphan">

```xml
<?xml version="1.0" encoding="utf-8" ?>
<hibernate-mapping xmlns="urn:nhibernate-mapping-2.2" assembly="LinqToNHibernateSample" 
 namespace="LinqToNHibernateSample">
  <class name="Star">
    <id name="Id">
      <generator class="guid.comb"/>
    </id>
    <property name="Name"/>
    <property name="Mass"/>
    <property name="Class" type="StarTypes"/>
    <property name="Color" type="SurfaceColor"/>
    <bag name="Planets" inverse="true" cascade="all-delete-orphan">
      <key column="StarId" />
      <one-to-many class="Planet"/>
    </bag>
  </class>
</hibernate-mapping>      
```

* <many-to-one name="Sun" class="Star" column="StarId"/>

```xml
<?xml version="1.0" encoding="utf-8" ?>
<hibernate-mapping xmlns="urn:nhibernate-mapping-2.2" assembly="LinqToNHibernateSample" 
  namespace="LinqToNHibernateSample">
  <class name="Planet">
    <id name="Id">
      <generator class="guid.comb"/>
    </id>
    <property name="Name"/>
    <property name="IsHabitable"/>
    <many-to-one name="Sun" class="Star" column="StarId"/>
  </class>
</hibernate-mapping>

```

4. hibernate.config.xml

* Set 'Copy to Output directory' to Copy Always
* configuration.Configure() will search for a file called hibernate.config.xml

```xml
<?xml version="1.0" encoding="utf-8" ?>
<hibernate-configuration xmlns="urn:nhibernate-configuration-2.2">
  <session-factory name="Sample">
    <property name="connection.provider">NHibernate.Connection.DriverConnectionProvider</property>
    <property name="connection.driver_class">NHibernate.Driver.SqlClientDriver</property>
    <property name="dialect">NHibernate.Dialect.MsSql2008Dialect</property>
    <property name="connection.connection_string">
      server=.\SQLEXPRESS;database=LinqToNHibernateSample;integrated security=true
    </property>
    <property name="proxyfactory.factory_class">
      NHibernate.ByteCode.Castle.ProxyFactoryFactory, NHibernate.ByteCode.Castle
    </property>
  </session-factory>
</hibernate-configuration>    
```

### Criteria queries

#### Untyped Criterial queries

```csharp
var query = session.CreateCriteria<Product>();
var products = session.CreateCriteria<Product>().List<Product>(); // the query is immediately executed

var first10Products = session.CreateCriteria<Product>() 
  .SetMaxResults(10) 
  .List<Product>();
```

Restriction

```csharp
var discontinuedProducts = session.CreateCriteria<Product>() 
  .Add(Restrictions.Eq("Discontinued", true)) 
  .List<Product>();

var discontinuedProducts = session.CreateCriteria<Product>() 
  .Add(Restrictions.Eq("Discontinued", false)) 
  .Add(Restrictions.GeProperty("ReorderLevel", "UnitsOnStock")) 
  .List<Product>();    
```

With NHibernate 3, we can use lambda expression for property name

```csharp
Projections.Property("Name")
Projections.Property<Product>(p => p.Name)
```

Sorting

```csharp
var sortedProducts = session.CreateCriteria<Product>() 
  .AddOrder(Order.Asc("Name")) 
  .List<Product>();
```

Grouping

```csharp
var productsGrouped = session.CreateCriteria<Product>() 
  .SetProjection(Projections.ProjectionList() 
    .Add(Projections.GroupProperty("Category")) 
    .Add(Projections.RowCount(), "Num") 
  ) 
  .List();
```

#### Strongly-typed Criteria queries

```csharp
var query = session.QueryOver<Product>();
var products = session.QueryOver<Product>().List();
var first10Products = session.QueryOver<Product>() 
  .Take(10) 
  .List();

var productsToReorder = session.QueryOver<Product>() 
  .Where(p => p.Discontinued == false) 
  .Where(p => p.ReorderLevel >= p.UnitsOnStock) 
  .List();  

var productsToReorder = session.QueryOver<Product>() 
  .Where(p => p.Discontinued == false &&  
    p.ReorderLevel >= p.UnitsOnStock) 
  .List();

var sortedProducts = session.QueryOver<Product>() 
  .OrderBy(p => p.Name).Asc 
  .ThenBy(p => p.UnitPrice).Desc 
  .List();    

var productsLookup = session.QueryOver<Product>() 
  .Select(p => p.Id, p => p.Name) 
  .TransformUsing(Transformers.AliasToBean<NameID>()) 
  .List<NameID>();

var productsGrouped = session.QueryOver<Product>() 
  .Select(Projections.Group<Product>(p => p.Category), 
    Projections.Avg<Product>(p => p.UnitPrice), 
    Projections.Sum<Product>(p => p.UnitsOnStock), 
    Projections.RowCount()) 
  .List<object[]>();  
```

### Hibernate Query Language

```csharp
var products = session.CreateQuery("from Product p").List<Product>();
var first10Products = session.CreateQuery("from Product p") 
  .SetFirstResult(10) 
  .SetMaxResults(10) 
  .List<Product>();

var discontinuedProducts = session 
  .CreateQuery("from Product p where p.Discontinued") 
  .List<Product>();

var hql = "from Product p" + 
  " where p.Category = :category" + 
  " and p.UnitPrice <= :unitPrice";
var cheapFruits = session 
  .CreateQuery(hql) 
  .SetString("category", "Fruits") 
  .SetDecimal("unitPrice", 1.0m) 
  .List<Product>();

var productsLookup = session 
  .CreateQuery("select Id as Id, Name as Name from Product") 
  .SetResultTransformer(Transformers.AliasToBean<NameID>()) 
  .List<NameID>();

var sortedProducts = session 
  .CreateQuery("from Product p order by p.Name, p.UnitPrice desc") 
  .List<Product>();

var productsGrouped = session 
  .CreateQuery("select p.Category as Category," +  
  "       count(*) as Count," +  
  "       avg(p.UnitPrice) as AveragePrice" +  
  " from  Product p" +  
  " group by p.Category") 
  .List();

var productsGrouped = session 
  .CreateQuery("select p.Category as Category," +  
    "       count(*) as Count," +  
    "       avg(p.UnitPrice) as AveragePrice" +  
    " from  Product p" +  
    " group by p.Category") 
  .SetResultTransformer(Transformers.AliasToEntityMap)
  .List<IDictionary>()
  .Select(r => new
  {
    Category = r["Category"],
    Count = r["Count"],
    AveragePrice = r["AveragePrice"],
  });  
```

### Lazy loading properties

A new feature of NHibernate 3 is the ability to lazy load specific properties of an entity.

```xml
<property name="SomeProperty" lazy="true" … />
```

```csharp
Map(x => x.SomeProperty).LazyLoad();

public class Book
{
  public virtual int Id { get; set; }
  public virtual string BookTitle { get; set; }
  public virtual int YearOfPublication { get; set; }
  public virtual string Review { get; set; }
}

public class BookMap : ClassMap<Book>
{
  public BookMap()
  {
    Id(x => x.Id);
    Map(x => x.BookTitle);
    Map(x => x.YearOfPublication);
    Map(x => x.Review) 
      .CustomType("StringClob") 
      .LazyLoad();
  }
}
```

### Executing multiple queries in a batch

The LINQ to NHibernate provider defnes a ToFuture extension method just for this purpose. All queries that are terminated with ToFuture are sent to the database as a batch at the moment when the data of the frst query is accessed.

```csharp
using (var session = factory.OpenSession())
using (var tx = session.BeginTransaction())
{
  var categories = session.Query<Category>().ToFuture();
  var query = session.Query<Product>() 
    .Where(p => !p.Discontinued) 
    .Where(p => p.Category.Name == "Fruits");
  var products = query 
    .ToFuture();
 
  var count = query
   .GroupBy(p => p.Discontinued) 
    .Select(x => x.Count()) 
    .ToFutureValue();
  // get the results
  var result = new Result
  {
    Categories = categories.ToArray(),
    Products = products.ToArray(),
    ProductCount = count.Value
  };
}
```

### Eager loading versus lazy loading

Person

* Id
* Firstname
* Lastname

Hobby

* Id
* Name

Person ->> Hobby
Hobby -> Person

```csharp
var listOfPersons = session.Query<Person>();
foreach (var person in listOfPersons)
{
  Console.WriteLine("{0} {1}", person.LastName, person.FirstName);
  foreach (var hobby in person.Hobbies)
  {
    Console.WriteLine("  {0}", hobby.Name);
  }
}
```

Then the sql becomes like

```sql
SELECT ... FROM "Person" person0_
SELECT ... FROM "Hobby" hobbies0_ WHERE hobbies0_.Person_id = ...
SELECT ... FROM "Hobby" hobbies0_ WHERE hobbies0_.Person_id = ...
SELECT ... FROM "Hobby" hobbies0_ WHERE hobbies0_.Person_id = ...
```

This is a typical select (n+1) problem. NHibernate frst loads the list of all person and then when we access the hobbies of each person, it lazy loads its respectve list of hobbies. 

We can instruct NHibernate to eagerly load all hobbies together with the person entities.

* Linq-to-nhibernate: Fetch
* Critria: LeftOuterJoin
* hql: left join fetch

NHibernate uses an outer join to load the person and hobbies data in one go.

```csharp
var persons = session.Query<Person>() 
  .Fetch(p => p.Hobbies);

var persons = session.CreateCriteria<Person>("p") 
  .CreateCriteria("p.Hobbies", JoinType.LeftOuterJoin) 
  .List<Person>();

var hql = "select p from Person as p left join fetch p.Hobbies as h";
var listOfPersons = session.CreateQuery(hql) 
  .List<Person>();
```

### Bulk data changes

Use hql

```csharp
var hql = "update Product p set p.UnitPrice = 1.1 * p.UnitPrice";
session.CreateQuery(hql).ExecuteUpdate();

var hql = "delete Product p where p.Discontinued=true";
session.CreateQuery(hql).ExecuteUpdate();

var hql = "delete Product p where p.Discontinued=true";
session.CreateQuery(hql).ExecuteUpdate();

var hql = "insert into Product(Id, Name, Category, UnitPrice) " +  
  "select t.Id, t.Name, t.Category, t.UnitPrice " +  
  "from ProductTemp t";
session.CreateQuery(hql).ExecuteUpdate();
```

## 10. Validating the Data to Persist
## 11. Common Pitfalls - Things to avoid
