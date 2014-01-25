# Home

## Sushiwa

### Working on paging view of orders

Sat. 18/01/2014

Use view model if you only need a subset of nhibernate model class. Sometimes, a model has child entity with one-to-many mappings and is not suitable for representation

knockout binding

```html
<a data-bind="text: OrderNo, attr: {href: OrderNo, title: 'Order No.'}"></a>
```

Returned Data

```javascript
{"Page":1,"NextPage":null,"PreviousPage":null,"Items":[{"OrderNo":165,"OrderDate":"2014-01-11T18:23:30","CustomerName":"Andrew Chaa","PaymentMethod":"paypal","IsPaid":false,"DeliveryTime":"ASAP"},{"OrderNo":168,"OrderDate":"2014-01-11T18:24:04","CustomerName":"Andrew Chaa","PaymentMethod":"paypal","IsPaid":false,"DeliveryTime":"ASAP"}]}
```

Added ajax spinner

```javascript
<img id="spinner" class="ajax-loader" src="~/Images/spinner.gif" />
<script type="text/javascript">
    var $loading = $('#spinner').hide();
    $(document).ajaxStart(function () { $loading.show(); }).ajaxStop(function () { $loading.hide(); });
</script>
```

## Web Drive

### Display folder / image one by one

looping through images array

```javascript
$.get(api, function (data) {
    $.each(data, function (i, val) {
        self.images.push(val);
    });

    self.images(data);
});

```

Thu. 23/1/2014

## 	Installing Ubuntu on virtual box

You have [set the type to 64 ubuntu](http://askubuntu.com/questions/308937/cannot-install-ubuntu-in-virtualbox-due-to-this-kernel-requires-an-x86-64-cpu) to use 64 version.```

Sat. 25/1/2014

## Installing Nancy-fx

https://github.com/NancyFx/Nancy/wiki/Introduction
http://visualstudiogallery.msdn.microsoft.com/f1e29f61-4dff-4b1e-a14b-6bd0d307611a