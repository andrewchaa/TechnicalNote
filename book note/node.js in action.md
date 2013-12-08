# Node.js In Action

## Advanced Express

### Authenticating User
#### Saving and loading users

##### Creating a package json file
```
express -e -s shoutbox
```

* -e: to enable ejs support
* -s: session support

```javascript
{
  "name": "shoutbox",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node app"
  },
  "dependencies": {
    "express": "3.x",
    "ejs": "*",
    "bcrypt": "0.7.3",
    "redis": "0.7.2"
  }
}
```

npm install will install all the dependencies.

##### Creating a user model

/lib/user

```javascript
var redis = require('redis');
var bcrypt = require('bcrypt');
var db = redis.createClient();  // create long running redir connection

module.exports = User;  		// export User function from the module
function User(obj) = {
	for (var key in obj) {  	// iterate keys in the object passed
		this[key] = obj[key]; 	// merge values
	}
}
```

##### Saving  a user into Redis

```javascript
User.prototype.save = function(fn) {
	if (this.id) {
		update(fn);
	} else {
		var user = this;
		db.incr('user:ids', function(err, id) {
			if (err) return fn(err);
			user.id = id;
			user.hashPassword(function(err) {
				if (err) return fn(err);
				user.update(fn);
			});
		});
	}

};

User.prototype.update = function(fn) {
	var user = this;
	var id = user.id;
	db.set('user:id:' + user.name, id, function(err) {	// index user id by name
		if (err) return fn(err);
		db.hmset('user:' + id, user, function(err) {	// use redis hash to store data
			fn(err);
		});
	});
}
```

### Advanced Routing Techniques
### Creating a Public API
### Error Handling