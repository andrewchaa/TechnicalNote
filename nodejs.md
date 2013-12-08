#node js


##Template engines
###ejs
####passing data
```javascript
exports.get = function(req, res) {
	var Measure = require('../models/Measure');

	Measure.find({}, function(err, measures) {

		console.log(measures);
		res.render('review', { measures : measures });

	});

};
```

####looping an array
```javascript
	<%  measures.forEach(function(measure) { %>

		<tr><td><%= measure.ofDate %></td><td><%= measure.weight %> kg</td></tr>
		<tr><td>Tue. 7/12/2013</td><td>52.4 kg</td></tr>

	<%	}) %>
```

##Utilities
### Momemnt.js
http://momentjs.com/

```
npm install moment -save
```