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