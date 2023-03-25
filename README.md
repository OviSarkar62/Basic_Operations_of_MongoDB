# Basic Operations of MongoDB

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
