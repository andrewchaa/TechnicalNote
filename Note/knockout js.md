# Knockout js

## Bindings

### attribute

```html
<ul class="thumbnails" data-bind="foreach: images">
  <li class="span3">
 	<a href="#" class="thumbnail"><img data-bind="attr: { src: src }" /></a>
  </li>
</ul>
	
```