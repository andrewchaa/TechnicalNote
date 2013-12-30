# CSharp

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

