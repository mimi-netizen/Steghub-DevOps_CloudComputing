# Install Express and set up routes to the server

Install mongoose  
`sudo npm install express mongoose`

In Books folder we create a folder named apps  
`mkdir apps && cd apps`

Create a file named routes.js  
`sudo nano routes.js`

Paste code

```powershell
var Book = require('./models/book');
module.exports = function(app) {
    app.get('/book', function(req, res) {
        Book.find({}, function(err, result) {
            if ( err ) throw err;
            res.json(result);
        });
    });
    app.post('/book', function(req, res) {
        var book = new Book( {
            name:req.body.name,
            isbn:req.body.isbn,
            author:req.body.author,
            pages:req.body.pages
        });
        book.save(function(err, result) {
            if ( err ) throw err;
            res.json( {
                message:"Successfully added book",
                book:result
            });
        });
    });
    app.delete('/book/:)
}
```

In the apps folder, create a folder named models  
`mkdir models && cd models`

Create a file named book.js  
`sudo nano book.js`

paste

```powershell

```
