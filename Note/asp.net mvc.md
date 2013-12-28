# ASP.NET MVC

## Thumbnails

```csharp
public ActionResult GetThumbnail(string path)
{
    var image = new WebImage(@"C:\Users\andrew\Documents\Projects\WebDrive\WebDrive\Images\Desert.jpg")
        .Resize(100, 100, true, true)
        .GetBytes();

    return File(image, "image/jpeg");
}

```