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
