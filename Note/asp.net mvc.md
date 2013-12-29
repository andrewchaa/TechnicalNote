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