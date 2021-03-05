An arangojs driver for [ley](https://github.com/lukeed/ley).

Expects a ley config file shaped like:

```js
module.exports = {
	driver: require('ley-driver-arangojs'),
	url,
	databaseName,
	auth: {
		username,
		password,
	}
}
```

## Migrations

Your ley migration functions are passed the arangojs database object.  The object has an extra property: `transaction`, representing the migration's transaction.

```js
exports.collections = [ 'todos' ]

exports.up = async DB => {
	console.log('hello from inside :: 002 @ up');

	const todos = DB.collection('todos')

	const items = [{
		name: 'get this working',
		completed: false,
	}, {
		name: 'test "UP" direction',
		completed: true
	}, {
		name: 'test "DOWN" direction',
		completed: false,
	}, {
		name: 'add `pg` driver',
		completed: false,
	}];

	for (const obj of items) {
		await DB.transaction.step(() => todos.save({
			name: obj.name,
			completed: obj.completed
		}))
	}
}
```

## Locking collections

In addition to the regular ley migration exports, you can also specify a `collections` export, giving a list of the collection names that should be locked (exclusive) for the migration's transaction.

```js
exports.collections = [ 'sweet_chart_data' ]
```

If you don't specify `collections`, all collections in the database will be locked.

This means that if you need to modify an index in an existing table, you should pass in an empty array so that the table in question is not locked.

```js
exports.collections = [ ]
```
