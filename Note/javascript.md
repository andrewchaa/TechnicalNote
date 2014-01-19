# javascript

#### Spinner

* [Download spinner image](http://preloaders.net/)
* [Show a loading image on ajax](http://stackoverflow.com/questions/68485/how-to-show-loading-spinner-in-jquery)
* [Centering on the screen, not page](http://stackoverflow.com/questions/6256043/css-position-loading-indicator-in-the-center-of-the-screen)

Showing the image

```javascript
var $loading = $('#spinner').hide();
$(document).ajaxStart(function () { $loading.show(); }).ajaxStop(function () { $loading.hide(); });
```

Position the image centre

```css
<style>
    .ajax-loader {
        position: fixed;
        left: 50%;
        top: 50%;
        margin-left: -32px; /* -1 * image width / 2 */
        margin-top: -32px;  /* -1 * image height / 2 */
        display: block;     
    }
</style>

```

Spinner

```javascript
<img id="spinner" class="ajax-loader" src="~/Images/spinner.gif" />
<script type="text/javascript">
    var $loading = $('#spinner').hide();
    $(document).ajaxStart(function () { $loading.show(); }).ajaxStop(function () { $loading.hide(); });
</script>
```
