# ASP.NET MVC

## Controllers

### Difference between ApiController and (MVC) Controller

[Difference](http://stackoverflow.com/questions/9494966/difference-between-apicontroller-and-controller-in-asp-net-mvc?rq=1)


### File result that returns a image for thumbnails

```csharp
// api/thumbnails/path
public FileContentResult Get(string path)
{
    var image = new WebImage(@"C:\Users\andrew\Documents\Projects\WebDrive\WebDrive\Images\Desert.jpg")
        .Resize(100, 100, true, true)
        .GetBytes();

    var result = new FileContentResult(image, "image/jpeg");
    return result;
}

```

### Get ContentType (MimeType) from file extension

[MimeMapping.GetMimeMapping](http://msdn.microsoft.com/en-us/library/system.web.mimemapping.getmimemapping)

```csharp
var thumbnail = new Thumbnail
    {
        Fullname = file,
        Content = new WebImage(file).Resize(100, 100, true, true).GetBytes(),
        ContentType = MimeMapping.GetMimeMapping(file)
    };

```

### Web Api controller construction with parameters

http://www.strathweb.com/2012/05/using-ninject-with-the-latest-asp-net-web-api-source/

