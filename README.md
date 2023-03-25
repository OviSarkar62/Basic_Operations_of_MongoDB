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
