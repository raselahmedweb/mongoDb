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

```js
npm install mongoose
```

- require mongoose

```js
const mongoose = require('mongoose');
```

- connect mongoose

```js
mongoose.connect('mongodb://127.0.0.1:27017/test');
```

- check mongoose is connected by then catch

```js
mongoose.connect('mongodb://127.0.0.1:27017/test')
.then(()=>console.log("Database is connected"))
.catch((err)=>{
    console.log(err, "Database is not connected");
    process.exit(1);
})
```

24. Use async await instead

```js
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

```js
await connectDB();
```

25. Create Schema

```js
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

```js
const Product = mongoose.model("products", productSchema);
```

27. Create product or insert documents into collection

- we will use node server and post method request to create product.

```js
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

```js
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

```js
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
## 29. Read document from Database in node.js & comparsion query operators
- comparsion query operators: https://www.mongodb.com/docs/manual/reference/operator/query-comparison/
  - $eq = equal, $ne = not equal, $gt = greater than, $lt = less than, $gte = greater than or equal, $lte = less than or equal, $in = in, $nin = not in

```js
const getProducts = async (req, res) => {
  try {
    const products = await Product.find();
    const products = await Product.find().limit(1);
    const products = await Product.find({ id: req.params.id });
    const products = await Product.find({ isAvailable: true });
    const products = await Product.find({ price: { $eq: 1200 } });
    const products = await Product.find({ price: { $in: [1200, 1300] } });
    const products = await Product.find({ price: { $nin: [1200, 1300] } });
    const products = await Product.find({ price: { $ne: 1200 } });
    const products = await Product.find({ price: { $gt: 1200 } });
    const products = await Product.find({ price: { $lt: 1200 } });
    const products = await Product.find({ price: { $gte: 1200 } });
    const products = await Product.find({ price: { $lte: 1200 } });
    const products = await Product.find({ price: 1200 });

    const products = await Product.find({ price: { $gt: 1200 } }).select({
      title: 1,
    });
    const products = await Product.find({ price: { $gt: 1200 } }).select({
      _id: 0,
      title: 1,
    });

    const products = await Product.find({ price: { $gt: 1200 } }).select({
      _id: 0,
      title: 0,
    });

    console.log(products);
  } catch (error) {
    console.log(error);
  }
};
getProducts();
```
## 30. Query Operators: Logical operators

- $and, $or, $not, $nor

```js
// {$and : [{},{}]}
const products = await Product.find({
  $and: [{ price: { $lt: 1400 } }, { rating: { $gt: 4 } }],
});

const products = await Product.find({
  $or: [{ price: { $lt: 1400 } }, { rating: { $gt: 4 } }],
});

// returns all that fail both clauses
const products = await Product.find({
  $nor: [{ price: { $lt: 1400 } }, { rating: { $gt: 4 } }],
});

// $not
const products = await Product.find({ price: { $not: { $lt: 900 } } });
```
## 31. counting and sorting

- countDocuments() / count()

```js
const products = await Product.find({ price: { $gt: 900 } }).countDocuments();

// ascending
const products = await Product.find().sort({ price: 1 });

// descending
const products = await Product.find().sort({ price: -1 });

// sort and select
const products = await Product.find()
  .sort({ title: 1 })
  .select({ title: 1, _id: 0 });
console.log(products);
```
## 32. Delete documents
```js
app.delete("/products/:id", async (req,res) => {
    try{
        const id = req.params.id;
        const product = await Product.deleteOne({_id: id});
        if(product){
            res.status(200).send({
                success: true,
                message: "Product deleted successfully",
                data: product,
            })
        }else{
            res.status(404).send({
                success: false,
                message: "Product was not deleted with this id",
            });
        }

    } catch(error) {
        res.status(500).send({
            message: error.message
            });
    }
})
```
## 33. Delete documents and get the all info of deleted document.
```js
app.delete("/products/:id", async (req,res) => {
    try{
        const id = req.params.id;
        const product = await Product.findByIdAndDelete({_id: id});
        if(product){
            res.status(200).send({
                success: true,
                message: "Product deleted successfully",
                data: product,
            })
        }else{
            res.status(404).send({
                success: false,
                message: "Product was not deleted with this id",
            });
        }

    } catch(error) {
        res.status(500).send({
            message: error.message
            });
    }
})
```

## 34. update data

- update data syntax: `db.collectionName.updateOne(selection_item, update_data)`
- update data syntax: `db.collectionName.findByIdAndUpdate(selection_item, update_data, {new: true})`
```js
app.put("/products/:id", async (req,res) => {
    try{
        const id = req.params.id;
        const product = await Product.updateOne({_id: id}, {$set: {price: 500}});
        if(product){
            res.status(200).send({
                success: true,
                message: "Product deleted successfully",
                data: product,
            })
        }else{
            res.status(404).send({
                success: false,
                message: "Product was not deleted with this id",
            });
        }

    } catch(error) {
        res.status(500).send({
            message: error.message
            });
    }
})
```
```js
// updateOne
const products = await Product.updateOne({ _id }, { $set: { rating: 4.8 } });
console.log(products);
updateProduct("63432689c564aea397b3d210");

// findByIdAndUpdate it returns old data
const products = await Product.findByIdAndUpdate(
  { _id },
  { $set: { rating: 4.8 } }
);
console.log(products);

// findByIdAndUpdate it returns updated data
const products = await Product.findByIdAndUpdate(
  { _id },
  { $set: { rating: 4.7 } },
  { new: true }
);
console.log(products);
```

## 35. mongoose validations

- [official documentation](https://mongoosejs.com/docs/validation.html)
- A common gotcha for beginners is that the unique option for schemas is not a validator.
- Numbers have: min, max validators
- Strings have: minlength, maxlength, trim, lowercase, enum
- validator error message can be provided using array syntax and object syntax

```js
Array syntax: min: [6, 'Must be at least 6, got {VALUE}']
Object syntax: enum: { values: ['Coffee', 'Tea'], message: '{VALUE} is not supported' }
```

```js
// validation when creating schema
  title: {
    type: String,
    required: [true, "product title is required"],

    minlength: 3, 
    minlength: [3, "error message here"],    
    maxlength: 3, 
   
    lowercase: true,
    uppercase: true,

    trim: true // "     iphone 7      ",
    enum: ["iphone", "samsung", "motorola"] // no other value is allowed other than these,
    enum: {
      values: ['iphone', 'samsung', motorola"],
      message: '{VALUE} is not supported'
    }
  },
  price:{
     type: String,
     required: true,
     min: 20,
     max: 30
  }
```

## 36. Custom validations

- for fulfilling own requirements based on certain situation we need to create custom validations.
- read about email vliadation
- [create your own validation regular expression](https://regexr.com/3e48o)

```js
price:{
    type: String,
    required: [true, "title is required"],
    validate: {
      validator: function (v) {
        return v.length === 10;
      },
      message: (props) => `${props.value} is not a valid product title!`,
    },
},
phone: {
    type: String,
    validate: {
      validator: function(v) {
        return /\d{3}-\d{3}-\d{4}/.test(v);
      },
      message: props => `${props.value} is not a valid phone number!`
    },
    required: [true, 'User phone number required']
  }
  email:{
    // ^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$
    type: String,
    required: [true, 'User email is required'],
    trim: true,
    lowercase: true,
    unique: true,
    validate: {
      validator: function(v) {
        const emailRegex = /^([\w-\.]+@([\w-]+\.)+[\w-]{2,4})?$/;
        return emailRegex.test(v);
      },
      message: props => `${props.value} is not a valid phone number!`
    },
     email: {
        type: String,
        trim: true,
        lowercase: true,
        unique: true,
        required: 'Email address is required',
        validate: [validateEmail, 'Please fill a valid email address'],
        match: [/^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/, 'Please fill a valid email address']
    }

  }
```

## 37. npm validator

- `npm i validator`

```js
email: {
    type: String,
    unique: true,
    required: [true, "email is required"],
    trim: true,
    lowercase: true,
    validate: {
      validator: validator.isEmail,
      message: (props) => `${props.value} is not a valid email!`,
    },
  },
```

## 38. pagination

```js
const { page = 1, limit = 10 } = req.query;
const products = await Product.find()
  .limit(limit)
  .skip((page - 1) * limit);
console.log(products);
