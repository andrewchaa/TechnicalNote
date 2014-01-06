# CSharp

## Linq

#### Difference between Expression<Func<T, bool>> and Func<T, bool>

* [Difference](http://stackoverflow.com/questions/2664841/difference-between-expressionfunc-and-func)
* [Conversion](http://stackoverflow.com/questions/1217749/how-to-convert-an-expressionfunct-bool-to-a-predicatet)

```csharp
Func<T, bool> func = expression.Compile();
Predicate<T> pred = t => func(t);
Predicate<T> pred = func.Invoke;
```

## File and directory handling

```csharp
private IEnumerable<Document> GetFiles(string baseFolder, DirectoryInfo directory)
{
    return directory
        .GetFiles()
        .Where(f => !f.Name.StartsWith("~"))
        .Where(f => f.Extension != ".ini" && f.Extension != ".lnk" && f.Extension != ".config" &&
            f.Extension != ".db" && f.Extension != ".exe")
        .Select(file => new Document(
                        file.FullName.Replace(baseFolder, string.Empty),
                        file.LastWriteTimeUtc.ToShortDateString(),
                        false,
                        GetFileImage(file.Extension))
                        ).ToList();
}

```

### Get Filename from full path

Use Path.GetFileName()

```csharp
public Thumbnail(string fullname, byte[] content, string contentType, bool isDirectory)
{
    Fullname = fullname;
    Name = Path.GetFileName(fullname); 
    Content = content;
    ContentType = contentType;
    IsDirectory = isDirectory;
}
```


## Iterators

### yield

* [Interesting use of the C# yield keyword in Nerd Dinner tutorial](http://stackoverflow.com/questions/1971053/interesting-use-of-the-c-sharp-yield-keyword-in-nerd-dinner-tutorial)

