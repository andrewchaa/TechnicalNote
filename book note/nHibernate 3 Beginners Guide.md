#nHibernate 3

## First Look

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

## A First Complete Sample

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

```  

### Creating the database schema
### Creating the database
### Creating a session factory
### Creating a session factory
### Opening a session
### Opening a session to the database
### Persisting objects to the database
### Adding a new category to the database
### Reading from the database
### Loading the list of all categories from the database
### Doing the same without NHibernate using ADO.NET only

