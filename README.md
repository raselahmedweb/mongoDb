# MongoDB uses

1. MongoDB is a document oriented database system.
2. Stores data as JSON-like objects
3. Database = Database
4. Table = Collection
5. Rows = Document
6. Columns = Field

7. Show Database

```sh
show dbs
```

8. Create and switch database

```sh
use DatabaseName
```

9. check database im in

```sh
db
```

10. Delete database

```sh
db.dropDatabase()
```

11. show collection

```sh
show collections
```

12. create collection

```sh
db.createCollection("users")
```

13. MongoDB installation

- Download and install MongoDB from mongodb.com
- Download and install MongoDB compass from mongodb.com

14. To insert data into collection (single document)

```sh
db.users.insertOne({ name: "John", age: 30, language: [ "English", "Bangla" ] })
```

15. To insert data into collection (multiple document)

```sh
db.users.insertMany([{ name: "Joe", age:35, language: [ "English", "Bangla" ] }, {name: "Smith", age: 35, language: [ "English", "Bangla"]}, {name: "Narima", age: 35, language: [ "English", "Bangla]}])
```

16. Read all documents from the collection using find()

```sh
db.users.find()
```

17. Read a specific document

```sh
db.users.find({name: "john"})
```

18. Use limit() to limit the number of documents

```sh
db.users.find().limit(2)
```

19. use pretty() to read documents nicely

```sh
db.users.find().pretty()
```

20. Update documents

```sh
db.users.update({name: "John"}, {$set: {age:23}})
```

21. Delete documents

```sh
db.users.deleteOne({name: "John"})
```

22. Delete all documents

```sh
db.users.deleteMany({})
```

# 23. Connect mongoDb in nodejs by mongoose

- Install mongoose npm

```sh
npm install mongoose
```

- require mongoose

```sh
const mongoose = require('mongoose');
```

- connect mongoose

```sh
mongoose.connect('mongodb://127.0.0.1:27017/test');
```

- check mongoose is connected by then catch

```sh
mongoose.connect('mongodb://127.0.0.1:27017/test')
.then(()=>console.log("Database is connected"))
.catch((err)=>{
    console.log(err, "Database is not connected");
    process.exit(1);
})
```

24. Use async await instead

```sh
const connectDB = async () => {
    try {
        await mongoose.connect('mongodb://127.0.0.1:27017/test');
        console.log("Database is connected");
    } catch (err){
        console.log("Database is not connected");
        console.log(err.message);
        process.exit(1);
    }
}
```

call connectDb function

```sh
await connectDB();
```

25. Create Schema

```sh
const productSchema = new mongoose.Schema({
    title: String,
    price: Number,
    description: String,
    createdAt: {
        type: Date,
        default: Date.now,
    },
});
```

26. Create Model

```sh
const Product = mongoose.model("products", productSchema);
```

27. Create product or insert documents into collection

- we will use node server and post method request to create product.

```sh
app.post("/products", async (req, res) => {
    try{
    // get product from request body
    const {title, price, description } = req.body;
    const newProduct = new Product({
        title: title,
        price: price,
        description: description,
    });
    const productData = await newProduct.save();
    res.status(201).send(productData);

    } catch(error){
        res.status(500).send({message: error.message});
    }
});
```

28. Read products by get request
    **important: find() return array, findOne() return object one**

```sh
app.get("/products", async (req, res) => {
    try{

    const products = await Product.find();
    if(products){
        res.status(200).send(products);
    }else{
        res.status(404).send({message: "Product not found"});
    }

    } catch(error){
        res.status(500).send({message: error.message});
    }
});
```

28.1. Read single product by params value

```sh
app.get("/products/:id", async (req, res) => {
    try{
    const id = req.params.id;
    const product = await Product.findOne({_id: id});
    if(product){
        res.status(200).send({
            success: true,
            message: "Product found",
            data: product,
        });
    }else{
        res.status(404).send({
            success: false,
            message: "Product not found"
            });
    }

    } catch(error){
        res.status(500).send({
            message: error.message
            });
    }
});
```
