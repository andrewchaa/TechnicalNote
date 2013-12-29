# Knockout js

## Bindings

### attribute

```html
<ul class="thumbnails" data-bind="foreach: images">
  <li>
    <a href="#" class="thumbnail">
      <img data-bind="attr: { src: 'data:' + ContentType + ';base64,' + FileContents }" />
    </a>
  </li>
</ul>
```

cf) [Embedding base64 image](http://stackoverflow.com/questions/1207190/embedding-base64-images)

```html
<img data-bind="attr: { src: 'data:' + ContentType + ';base64,' + FileContents }" src="data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEAYABgAAD/2wBDAAgGBgcGBQgHBwcJCQgKDBQNDAsLDBkSEw8UHRofHh0aHBwgJC4nICIsIxwcKDcpLDAxNDQ0Hyc5PTgyPC4zNDL/2wBDAQkJCQwLDBgNDRgyIRwhMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjL/wAARCABLAGQD...">
```

### Observable Array

Add an element

```javascript
self.getImage = function() {
    $.get('/api/thumbnails/5', function(data) {
        self.images.push(data);
    });
};
```