# Express
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');
const bodyParser = require('body-parser');
const crypto = require('crypto');
const app = express();
app.use(cors());
app.use(express.json());
app.use(bodyParser.urlencoded({ extended: true }));
mangoose.connect('mangodb+srv://v01d:puiQNMy3fUkvJra7@test-db.dxurd.mongodb.net/?retryWrites=true&w=majority&appName=test-db',
{
  useNewurlParser: true,
  useUnifiedTopplogy: true  
})
.then(() => console.log('MongoDB connected'))
.catch(err => console.error(err));
//Define User Schema
const userSchema = new mongoose.Schema({
    name: { type: String, required: true },
    email: { type: Number, required: true, unique: true },
    password: { type: String, required: true },
    upi_id: { type: String, required: true },
    balance: { type: Number}
});
//create user model
const User = mangoose.model('User', userSchema);
//Define Transaction Schema
const transactionSchema = new mangoose.Schema({
    sender_upi_id: { type: String, required: true  },
    receiver_upi_id: { type:String, required: true },
    amount: { type: Number, required: true },
    timestamp: { type:Date, default: Date.now }
});
//Create Transaction Model
const Transaction = mangoose.model('Transaction', transactionSchema);

//Function to generate a unique UPI ID
const generateUPI = () => {
    const randomID = crypto.randomBytes(4).toString('hex');
    return '${randomID}@fastpay';
};

//signup Route
app.post('/api/signup', async (req, res) => {
    try {
        const { name, email, password } = req.body;
        //check if user already exists
        let user = await User.findOne({ email });
        if(user) {
            return res.status(400).send({ message: 'User already exists' })
        }
        //Generate UPI ID
        const upi_id = generateUPI();
        const balance = 1000;
        //create new user
        user = new({ name, email, password, upi_id, balance});
        await user.save();
        res.status(201).send({ message: 'User registered successfully!', upi_id});
    }   catch (error) {
        console.error(error);
        res.status(500).send({ message: 'Server error' });
        }
});
 //Fetch User Details Route
 app.get('/api/user/:upi_id', async (req, res) => {
    try {
        const { upi_id } = req.params;
        const user = await User.findOne({ upi_id });
        if (!user) {
            return res.status(400).send({ message: 'User not found' });
        }
        res.status(200).send(user);
    }   catch (error) {
        console.error('Error fetching user:', error);
        }
    }
