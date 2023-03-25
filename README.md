# Basic Operations of MongoDB

Basic operations of MongoDB like create, read, find, update, delete data with built-in and custom schema. MongoDBCompass must be connected to run the following operartions.

## CRUD Operations in MongoDB

### Create product schema

    const productSchema = new mongoose.Schema({
    title: {
        type: String,
        required: [true,"product title is required"],
        minlength:[3, "minimum length of the title should be 3"],
        maxlength:[15, "maximum length of the title should be 15"],
        trim: true
    },
    price: {
        type: Number,
        min: [300, "minimum price should be 300"],
        max: [5000000, "minimum price should be 5000000"],
        required: true
    },
    phone:{
        type:String,
        required:[true, "phone number is required"],
        validate:{
            validator: function(v){
                const phoneRegex = /\d{3}-\d{3}-\d{4}/;
                return phoneRegex.test(v);
            },
            message:(props)=>`${props.value} is not a valid phone number`,
        }
    },
    description: {
        type: String,
        required: true
    },
    createdAt:{
        type: Date,
        default: Date.now,
    },
    });

### Create product model

    const Product = mongoose.model("products", productSchema);
    const connectDB = async()=>{
    try{
        await mongoose.connect('mongodb://127.0.0.1:27017/productdb');
        console.log("DB is connected");
    } catch (error) {
        console.log("DB is not connected");
        console.log(error.message);
        process.exit(1);
    }
    }

### Create data

    app.post("/products", async(req,res)=>{
    try {
        const newProduct = new Product({
            title: req.body.title,
            price: req.body.price,
            description: req.body.description
        });
        const productData = await newProduct.save();
        res.status(201).send(productData);
    } catch(error){
        res.status(500).send({message: error.message});
    }
    })
 
 ### Read data
 
    app.get("/products", async(req,res)=>{
    try{
        const products = await Product.find().limit(10);
        if(products){
            res.status(200).send({
                success: true,
                message: "return all products",
                data: products
            });
        }else{
            res.status(404).send({
                success: false,
                message:"products not found"
            });
        }
    } catch(error){
        res.status(500).send({message: error.message})
    }
    })
 ### Find data(specific)
 
    app.get("/products/:id", async(req,res)=>{
    try{
        const id = req.params.id;
        const product = await Product.findOne({_id: id});
        if(product){
            res.status(200).send({
                success: true,
                message: "return single product",
                data: product
            });
        }else{
            res.status(404).send({
                success: false,
                message:"product not found"
            });
        }
    } catch(error){
        res.status(500).send({message: error.message})
    }
    })
    
 ### Update data
 
    app.put("/products/:id", async(req,res)=>{
    try{
        const id = req.params.id;
        const updatedProduct = await Product.findByIdAndUpdate(
            {
                _id:id
            },
            {
                $set:{
                    title: req.body.title,
                    price: req.body.price,
                    description: req.body.description
                },
            }
        );
        if(updatedProduct){
            res.status(200).send({
                success: true,
                message: "Updated single product",
                data: updatedProduct
            });
        }else{
            res.status(404).send({
                success: false,
                message:"product not updated"
            });
        }
    }catch(error){
        res.status(500).send({message: error.message});
    }
    })

### Delete data

    app.delete("/products/:id", async (req,res)=>{
    try{
        const id = req.params.id;
        const product = await Product.findByIdAndDelete({_id: id});
        if(product){
            res.status(200).send({
                success: true,
                message: "deleted single product",
                data: product
            });
        }else{
            res.status(404).send({
                success: false,
                message:"product not deleted"
            });
        }
    } catch(error){
        res.status(500).send({message: error.message})
    }
    })
