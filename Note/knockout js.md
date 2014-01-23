# Knockout js

## Bindings

### attribute

#### link

```html
<a data-bind="text: OrderNo, attr: {href: OrderNo, title: 'Order No.'}"></a>
``` 

#### Inline image

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

#### data-xxx attribute

```html
<li><a data-bind="attr: { href: previousPage, 'data-page': previousPage }">Previous</a></li>
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

## Conditionals

### ko if, ifnot

```html
<ul class="breadcrumb" id="breaddcrumb" data-bind="foreach: breadcrumbs">
	<li>
	    <!-- ko if: $index() == 0 -->&nbsp;&nbsp;<i class="icon-home"></i><!-- /ko -->

	    <a href="#" data-bind="text: Name, click: $parent.clickBreadcrumb"></a>
	    <span class="divider"><i class="icon-angle-right"></i></span>
	</li>
</ul>

<!-- ko if: IsPaid -->
<span class="label label-success">Yes</span>
<!-- /ko -->
<!-- ko ifnot: IsPaid -->
<span class="label label-warning">No</span>
<!-- /ko -->

```

### Conditional css and style

```html
<tr data-bind="style: { 'background-color': IsToday == true ? 'FloralWhite' : '' }">

```

## javascrip

### looping through an array

```javascript
$.get(api, function (data) {
    $.each(data, function (i, val) {
        self.images.push(val);
    });

    self.images(data);
});

```