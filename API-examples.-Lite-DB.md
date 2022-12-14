### Create a database

Call new LiteDatabase() to create a database. It's ok if database already exists. It will just open it.

```
using LiteDB;
...
LiteDatabase db = new LiteDatabase(@"testing.db");
```

### Create a collection

```
public class CustomerDataLdb
{
    [BsonId] // the unique identifier
    public string Username { get; set; }

    public string Password { get; set; }
    public string Email { get; set; }
    public Dictionary<string, string> Properties { get; set; }
}

LiteCollection<CustomerDataLdb> _customers;

string collectionName = "customers";

if (db.CollectionExists(collectionName)) // can check if collection already exists
    db.DropCollection(collectionName);

// create new collection or get it if it already exists
_customers = db.GetCollection<CustomerDataLdb>(collectionName);
```

### Check if a collection exists
```
if (db.CollectionExists("customers")) {
    Debug.Log("It exists!");
}
```

### Delete a collection
```
db.DropCollection("customers");
```

### Enumerate all collections in database
```
foreach (string collectionName in db.GetCollectionNames())
{
    Debug.Log(collectionName);
}
```

### Insert into a collection

```
var customer = new CustomerDataLdb() { 
    Username = "username", 
    Password = "password", 
    Email = "email.address@gmail.com" 
};
_customers.Insert(customer);
```
### Find an entry in a collection

```
var entry = _customers.FindOne(a => a.Username == "username");
if (entry != null)
    Debug.Log("Found " + entry.Username + " " + entry.Email);
```

### Delete an entry in a collection

```
_customers.Delete("username"); // matches against the [BsonId] field Username
```

### Update an entry in a collection
```
entry = _customers.FindOne(a => a.Username == "username");
if (entry != null)
{
    entry.Password = "secret";
    _customers.Update(entry);
}
```

### Count number of entries in a collection
```
Debug.Log("Customers: " + _customers.Count());
```

### Iterate over all entries in a collection
```
foreach (CustomerDataLdb customer in _customers.FindAll())
{
    Debug.Log(customer.Username);
}
```


Hope this helps.
