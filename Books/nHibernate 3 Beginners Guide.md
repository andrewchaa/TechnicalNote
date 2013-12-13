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
### Creating a Name value object
### Creating an entity
### Creating a base entity
### Creating a Customer entity
### Defining relations between entities
### The order entry model
### Implementing an order entry model

