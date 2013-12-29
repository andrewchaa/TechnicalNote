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

## Iterators

### yield

* [Interesting use of the C# yield keyword in Nerd Dinner tutorial](http://stackoverflow.com/questions/1971053/interesting-use-of-the-c-sharp-yield-keyword-in-nerd-dinner-tutorial)

