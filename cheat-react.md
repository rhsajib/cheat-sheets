
### Vite + React
#### process-1
```sh
npm create vite@latest
```

```sh
Project name: … ema-john-simple
```
```sh
Select a framework: › React
```
```sh
Select a variant: › JavaScript
```
```sh
cd ema-john-simple
npm install
npm run dev
```
#### process-2
Reference: https://reactrouter.com/en/main/start/tutorial#setup
```sh
npm create vite@latest price-list-cards -- --template react
```

```sh
npm i
# or
npm install
```

```sh
npm run dev
```
### Configure react vite for docker image

```jsx
// vite.config.js
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

// https://vitejs.dev/config/

export default defineConfig({
    plugins: [react()],
    server: {
        watch: {
            usePolling: true,
        },
        host: true, // needed for the Docker Container port mapping to work
        strictPort: true,
        port: 5173, // you can replace this port with any port
    },
});
```


### Remove red waved line form a variable that is not used in vs code
```js
# go to `.eslintrc.cjs` file
# add `node: none` inside `env` object

# .eslintrc.cjs
module.exports = {
  root: true,
  env: { browser: true, es2020: true, node: none },
}
```




### Create Component

```js
import './css/App.css';

const user = {
    name: 'Sajib',
    age: '30'
}

function Person(props){
    // console.log(props)  
    if (props.name && props.age) {
        return (
            <div className='person' key={props.name}>
                <h1>Name: {props.name}</h1>
                <p>Age: {props.age}</p>
            </div>
        )
    } else {
        return (
            <div className='person'>
                <h1>Name: {user.name}</h1>
                <p>Age: {user.age}</p>
            </div>
        )
    }
    // <h1>Name: {user.name}</h1> 
    // <p>Age: {user.age}</p> 

}

const friends = [
    {name: 'Kuber', age: 'friends age'},
    {name: 'Piash', age: 'friends age'},
    {name: 'Akash', age: 'friends age'},
    {name: 'Faruk', age: 'friends age'},
]


function App() {
    return (
        <div className='App'>
            {
                friends.map(friend => <li>{friend.name}</li>)
            }
            <h1 style={{color: 'red', backgroundColor: 'yellow', textAlign: 'center'}}>Component Writing style -1 </h1>
            <Person />
            <Person name="Rubel" age="40"></Person>
            <Person></Person>
            <Person name="Bapparaz" age="50"></Person> 
            
            <h1 style={{color: 'red', backgroundColor: 'yellow', textAlign: 'center'}}>Component Writing style -2 </h1>
            {
                friends.map(friend => <Person name={friend.name} age={friend.age} />)
            }
            
            <h1 style={{color: 'red', backgroundColor: 'yellow', textAlign: 'center'}}>Component Writing style -3 </h1>
            {
                friends.map(({name, age}) => <Person name={name} age={age} />)
            }
        </div>
    )
}

export default App;
```
### Style
```js
# App.js
import './css/App.css';

function App() {
    return (
        <div className='App'>
            <District name='Dhaka' specility='Capital'></District>
            <District name='Noakhali' specility='Noakhaillllllllaaaaa'></District>
        </div>
    )
}


const districtStyle = {
    backgroundColor: 'yellow',
    margin: '20px',
    borderRadius: '20px'
}

function District(props){
    return (
        <div style={districtStyle}>
            <h2>Name: {props.name}</h2>
            <p>Speciality: {props.specility} </p>
        </div>
    )
}


export default App;
```


### useState
we use `useState` when the state/value changes dynamically specially after click.
```js
# App.js
import { useState } from 'react';
import './css/App.css';


function App() {
    return (
        <div className='App'>
            <Counter />
        </div>
    )
}

function Counter () {
    const [count, setCount] = useState(0)   // here, useState(0) means useState(initialState)
    // useState is a functionor hook which return 
    // an array of a value and a function like [value, function]
    // generally we use set prefix for function like [value, setValue] 

    // set event handeler
    // const increaseCount = () =>{
    //     const newCount = count + 1;
    //     setCount(newCount);
    // }


    // const decreaseCount = () =>{
    //     const newCount = count - 1;
    //     setCount(newCount);
    // }

    const increaseCount = () => setCount(count + 1)
    const decreaseCount = () => setCount(count - 1)

    return (
        <div>
            <h2>Count: {count}</h2>
            <button onClick={increaseCount}>Increase</button>
            <button onClick={decreaseCount}>Decrease</button>
        </div>
    )
}
```

```js
# App.js
import { useState } from 'react';
import './css/App.css';


function App() {
    return (
        <div className='App'>
            <District name='Dhaka'></District>
            <District name='Noakhali'></District>
        </div>
    )
}

const districtStyle = {
    backgroundColor: 'lightblue',
    margin: '20px',
}

function District(props){
    const [power, setPower] = useState(1);
    const boostPower = () => setPower(power * 2)
    return (
        <div style={districtStyle}>
            <h2>Name: {props.name}</h2>
            <h4>Power: {power}</h4>
            <button onClick={boostPower}>Boost the power</button>
        </div>
    )
}

export default App;
```


```jsx
// multiple event handler
// one is foreign handler
// another one is self handler
<button
    onClick={() => {
        handleSendMesaage(messageText), handleMessageText;
    }}
>
    Send
</button>
```


```jsx
// useSatate to see hidden element
# App.js
import { useState } from 'react';
import './css/App.css';

function App() {
    return (
        <div className='App'>
            <Element></Element>
        </div>
    )
}

function Element() {
    const [element, setElement] = useState([]);

    const newElement = () => {
        return (
            <div>
                <h1>this is new element</h1>
            </div>
        )
    };

    const seeElement = () => setElement(newElement());

    return (
        <div>
            <button onClick={seeElement}>Click here to see new element</button>
            <h4>{element}</h4>
        </div>
    )
};

export default App;
```



### useEffect
```js
import { useEffect, useState } from 'react';
import './css/App.css';


function App() {
    return (
        <div className='App'>
            <ExternalUsers />
        </div>
    )
}


function ExternalUsers () {
    const [users, setUsers] = useState([]);   // here, [] means no dependency

    // genral format to write useEffect is 
    // useEffect(arrowFunction, dependencies)
    // useEffect( () => {}, [])    
    // here, [] means no dependency

    useEffect( () => {
        fetch('https://jsonplaceholder.typicode.com/users')  // API = https://jsonplaceholder.typicode.com/users
        .then(res => res.json())
        // .then(data => console.log(data))
        .then(data => setUsers(data))
    }, [])

    return (
        <div>
            <h2>External Users</h2>
            <p>Total users: {users.length}</p>
            {
                users.map(user => <User name={user.name} email={user.email}/>)
            }
        </div>
    )
}


function User(props) {
    return (
        <div style={{
            border: '2px solid red',
            margin: '20px',
            borderRadius: '20px'
            }}>

            <h3>Name: {props.name}</h3>
            <p>Email: {props.email}</p>
        </div>
    )
}

export default App;
```

```js
# App.js
import { useEffect, useState } from 'react';
import './css/App.css';

function App() {
    return (
        <div className='App'>
            <LoadPost></LoadPost>
        </div>
    )
}



function LoadPost() {
    const [posts, setPost] = useState([])

    useEffect( () => {
        fetch('https://jsonplaceholder.typicode.com/posts')
        .then(res => res.json())
        .then(data => setPost(data))
    }, []) 

    return (
        <div>
            <h2>Total Posts : {posts.length}</h2>
            {
                posts.map(post => <Post post={post}></Post>)
            }
        </div>
    )
}

function Post(props){
    const {title, body} = props.post
    return (
        <div style={{
            margin: '20px',
            padding: '10px'
            }}>
            <h3>Title: {title}</h3>
            <p>Body: {body}</p>
        </div>
    )
}

export default App;
```


```js
# public/data.json
[
  {
    "_id": "65103696b6f2179839592a60",
    "index": 0,
    "guid": "822597a4-ece6-4046-bcf8-b699561eae72"
  },
  {
    "_id": "65103696038a289f2817db62",
    "index": 3,
    "guid": "4d1b4277-4bf7-4f0d-9ef4-6642de757338"
  }
]

# src/App.js
import { useEffect, useState } from 'react';
import './css/App.css';

function App() {
    return (
        <div className='App'>
            <LoadPost></LoadPost>
        </div>
    )
}


function LoadPost() {
    const [posts, setPost] = useState([])

    useEffect( () => {
        fetch('data.json')        // data.json is here
        .then(res => res.json())
        .then(data => setPost(data))
    }, []) 

    return (
        <div>
            <h2>Total Posts : {posts.length}</h2>
            {
                posts.map(post => <Post post={post}></Post>)
            }
        </div>
    )
}

function Post(props){
    const {title, body} = props.post
    return (
        <div style={{
            margin: '20px',
            padding: '10px'
            }}>
            <h3>Title: {title}</h3>
            <p>Body: {body}</p>
        </div>
    )
}

export default App;
```



#### Conditional rendering
```js
const useCaseConditionalRender = props => {
    const [condition, setCondition] = useState(false);

    const clickHandler = () => {
        setCondition(true);
    };

    return (
        <>
            <h2>Conditional Rendering</h2>
            <button onclick={clickHandler}>Set Condition</button>
            {condition && <p>Hello!</p>}
        </>
    );
};
```

### Children of a component

```js
// Shop.jsx

import React from "react";

const Shop = () => {
    return (
        <div>
            <Cart>
                <div> I am from Shop </div>
            </Cart>
        </div>
    );
};

export default Shop;
```


```js
// Orders.jsx

import React from "react";

const Orders = () => {
    return (
        <div>
            <Cart>
                <div> I am from Orders </div>
            </Cart>
        </div>
    );
};

export default Orders;
```

We can access the elements inside of the Cart component by using 'children'

```js
// Cart.jsx

import React from "react";

const Cart = ({children}) => {       // look at here
    return (
        <div>
            {children}
            // in Shop component it will show 'I am from Shop'
            // in Shop component it will show 'I am from Orders'
        </div>
    );
};

export default Cart;
```


### `React extension Pack` extension for VS Code
```js
// this extension will be used to create component easily
// create seperate file for component
```

```js
# Countries.js

// type 'rsc' in vs code
// it will automatically create the format to create a component
// rsc = react stateless component
```


```
# Components in different folders
// project directory

react-app/
    src/
        components/
            Countries/
                Countries.js
                Countries.css
            Country/
                Country.js
                Country.css
            Header/
                Header.js
                Header.css
            Footer/
                Footer.js
                Footer.css
        css/
            App.css
        App.js
        index.js
        index.css
```

Countries

```js
# components/Countries/Countries.js
import React, { useEffect, useState } from 'react';
import Country from '../Country/Country';
import './Countries.css';

const Countries = () => {
    const [countries, setCountries] = useState([])

    useEffect( () => {
        fetch('https://restcountries.com/v3.1/all')
        .then(res => res.json())
        .then(data => setCountries(data))
    }, [])

    return (
        <div>
            <h3>Total Countries: {countries.length}</h3>
{/* 
            {
                countries.map(country => console.log(country))
            } */}


            <div className='countries-container'>
                {
                    countries.map(country => <Country 
                        country={country}
                        key={country.cca3}
                        ></Country>)
                }
            </div>
        </div>
    );
};

export default Countries;
```

```css
# components/Countries/Countries.js
.countries-container {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-gap: 10px;
}
```

Country

```js
# components/Country/Country.js
import React from 'react';
import './Country.css';


const Country = (props) => {
    // console.log(props.country)

    // const name = props.country.name.common
    // const population = props.country.population
    // const region = props.country.region

    const {area, region, flags, population, name} = props.country;

    return (
        <div className='country'>
            <h2>Name: {name.common}</h2>
            <img src={flags.png} alt="country flag" />
            <p>Population: {population}</p>
            <p>Area: {area}</p> 
            <p><small>Region: {region}</small></p>
        </div>
    );
};

export default Country;
```
```css
# components/Country/Country.css
.country{
    background-color: blanchedalmond;
    border: 3px solid blue;
    border-radius: 10px;
    margin: 20px;
    padding: 5px;
}

.country img{
    width: 150px;
    height: 80px;
}
```

Header

```js
# components/Header/Header.js
import './Header.css';

const Header = () => {
    return (
        <div>
            <h1>Welcome to my Rest Countries</h1>
            <nav className='menu'>
                <a href='/'>Home</a>
                <a href='/'>Countries</a>
                <a href='/'>Area</a>
                
            </nav>
        </div>
    );
};

export default Header;
```

```css
# components/Header/Header.css
.menu a{
    text-decoration: none;
    margin: 10px;
}
```


Footer

```js
# components/Footer/Footer.js
import React from 'react';

const Footer = () => {
    return (
        <div>
            <p><small>Copyright @sajib</small></p>
        </div>
    );
};

export default Footer;
```


```css
# components/Footer/Footer.css

```

App.js

```js
# src/App.js
import './css/App.css';
import Countries from './components/Countries/Countries';
import Footer from './components/Footer/Footer';
import Header from './components/Header/Header';

function App() {
    return (
        <div className='App'>
            <Header />
            <Countries />
            <Footer />
        </div>
    )
}
export default App;
```

### Export & Import

```
# directory

src/
    components/
        Countries/
            Countries.js
            Countries.css
        Users/
            Users.js
            Users.css
    utils/
        Funcs/
            Funcs.js
```


#### Export single and multiple object
```js
// export single object
# src/components/Countries/Countries.js
function Countries() {
    return (
        <div>
            <h1>I am from Countries</h1>
        </div>
    );
};
export default Countries;      // look at here
```

```js
// export multiple objects
# src/utils/Funcs/Funcs.js
function FuncsFirst() {
    return (
        <div>
            <h1>I am from Countries</h1>
        </div>
    );
};

function FuncsSecond() {
    return (
        <div>
            <h1>I am from FuncsSecond</h1>
        </div>
    );
};
export {FuncsFirst, FuncsSecond};       // look at here
```
#### import
```js
# src/components/Users/Users.js
import {FuncsFirst} from '../../utils/Funcs/Funcs';
import Countries from '../../components/Countries/Countries';


function Countries() {
    return (
        <div>
            <h1>I am from Countries</h1>
        </div>
    )
}
export default Countries;
```


### Using local storage + Event handler

```js
# process 1

import React from 'react';
import './Cosmetic.css'


const Cosmetic = (props) => {
    const {id, name, price} = props.cosmetic;

    //add event handler
    const addToCart = (id) => {
        console.log('item added', id)
    };
    
    const addToCartWithPara = () => addToCart(id);

    return (
        <div className='cosmetic'>
            <h2>Name: {name}</h2>
            <p>Only for ${price}</p>
            <p><small>Id: {id}</small></p>
            <button onClick={() => addToCart(id)}>Purchase</button>
            <button onClick={addToCartWithPara}>Add to Cart</button>
        </div>
    );
};

export default Cosmetic;
```


```js
# process 2

import React from 'react';
import './Cosmetic.css'


// using local storage of browser
const addToDb = id => {
    const quantity = localStorage.getItem(id)
    if (quantity) {

        const newQuantity = parseInt(quantity) + 1;
        localStorage.setItem(id, newQuantity);
    }
    else{
        localStorage.setItem(id, 1);
    }
}


const Cosmetic = (props) => {
    const {id, name, price} = props.cosmetic;

    //add event handler
    const addToCart = (id) => {
        addToDb(id)
    };

    return (
        <div className='cosmetic'>
            <h2>Name: {name}</h2>
            <p>Only for ${price}</p>
            <p><small>Id: {id}</small></p>
            <button onClick={() => addToCart(id)}>Add to Cart</button>
        </div>
    );
};

export default Cosmetic;
```

### Multiple event handler for parent and child component + Event handler for user input

```jsx
// parent component
// messages.jsx
import React, { useEffect, useState } from "react";
import Message from "../Message/Message";
import { useLoaderData } from "react-router-dom";
import NoMessage from "../NoMessage/NoMessage";
import SendMessage from "../SendMessage/SendMessage";

const Messages = () => {

    // previous messages
    const [previousMessages, setPreviousMessages] = useState([]);
    useEffect(() => {
        setPreviousMessages(messages);
    }, [messages]);

    // handler to send message
    const handleSendMesaage = (message) => {
        // step 1: handle messages in client side
        const modelMessage = {
            user_id: "2123bb0ec29d4471bd295be4cca68aed",
            message: message,
            created_at: Date.now() ,
        };
        setPreviousMessages([...previousMessages, modelMessage]);

        // step : handle messages in server side
    };


    return (
        <div>
            <div>
                {previousMessages.length !== 0 ? (
                    previousMessages.map((message, index) => (
                        <Message key={index} message={message} />
                    ))
                ) : (
                    <NoMessage />
                )}
            </div>
            <div>
                <SendMessage handleSendMesaage={handleSendMesaage} />
            </div>
        </div>
    );
};

export default Messages;

```
```jsx
// chield component
// sendMessage.jsx
import React, { useEffect, useState } from "react";

const SendMessage = ({ handleSendMesaage }) => {
    const [messageText, setMessageText] = useState("");

    const handleSendClick = () => {
        // Call the parent's handleSendMessage function to send the message
        handleSendMesaage(messageText);

        // Clear the input field by resetting messageText to an empty string
        setMessageText("");
    };

    const handleMessageTextChange = async (e) => {
        // console.log(e);
        setMessageText(e.target.value);
    };

    return (
        <div>
            <input
                type="text"
                id="message"
                name="message"
                value={messageText}
                onChange={handleMessageTextChange}
                placeholder="Write text..."
                autoComplete="off"
            />
            <button onClick={handleSendClick}>
                <span>Send</span>
            </button>
        </div>
    );
};
```


### Event Handler (advanced)
pass event handler through tag attribute to another component


```js
// Shop.jsx
import { addToDb, getShoppingCart } from '../../utilities/fakedb';
  
const Shop = () => {
    // define variable which will change its state after clicking
    const [cart, setCart] = useState([]);
    
    // load cart initially after loading the page
    useEffect( () => {
        const storedCart = getShoppingCart();
        const savedCart = [];
    
        // write code here
        // do some operation for 'savedCart'
        
        setCart(savedCart)
        
    }, [dependencies]);
    
    
    // load cart after clicking button
    const handleAddToCart = (product) => {
    
        // Step 1: handle client side
        let newCart = []
   
        // write code here
        // do some operation for 'newCart'
        // use filter or find function for operations
        setCart(newCart);
        
        // Step 2: handle database side
        addToDb(product.id);
    }
    
    
    return (
        <div>
            products.map(product => <Product
                product = {product}
                key = {product.id}
                handleAddToCart = {handleAddToCart}   // pass event handler into 'Product' component
                />)
        </div>
    )
export default Shop;
```


```js
// Product.jsx

// click korar pore state er je change hobe oi state toh eikhane nai
// tai state ta ke event haldler er maddhome upper level e pass kore dite hobe
import React from 'react';

const Product = ({product, handleAddToCart) => {
    const {id, img, seller, quantity, name, price, ratings} = product;
    
    return (
        <div className='product'>
            <h6>Name: {name}</h6>
            <p>Price: {price}</p>
            
            // click korar pore state er je change hobe oi state toh eikhane nai
            // tai state ta ke event haldler er maddhome upper level e pass kore dite hobe
            <button onClick={() => handleAddToCart(product)}>
                Add to cart
            </button>
        </div>
    );
};

export default Product;
```



### Use of Local storage for cart operation
```js
import Cosmetics from './components/Cosmetics/Cosmetics';

function App() {
  return (
    <div>
      <h1>Welcome to Cosmetics Store</h1>
      <Cosmetics></Cosmetics>
    </div>
  );
}

export default App;
```


``` js
# Cosmetics.js
import React, { useEffect, useState } from 'react';
import Cosmetic from '../Cosmetic/Cosmetic';

const Cosmetics = () => {
    const [cosmetics, setCosmetics] = useState([])

    useEffect( () => {
        fetch('data.json')
        .then(res => res.json())
        .then(data => setCosmetics(data))
    }, [])

    return (
        <div>
            {
                cosmetics.map(cosmetic => <Cosmetic cosmetic={cosmetic}></Cosmetic>)
            }
        </div>
    );
};

export default Cosmetics;
```

```js
# Cosmetic.js
import React from 'react';
import { addToDb, removeFromDb } from '../../utils/fakedb';

const Cosmetic = (props) => {
    const {id, name, price} = props.cosmetic;

    //add event handler
    const addToCart = (id) => {
        addToDb(id)
    };
    
    const removeFromCart = id => {
        removeFromDb(id)
    }

    return (
        <div>
            <h2>Name: {name}</h2>
            <p>Only for ${price}</p>
            <p><small>Id: {id}</small></p>
            <button onClick={() => addToCart(id)}>Add to Cart</button>
            <button onClick={() => removeFromCart(id)}>Remove</button>
        </div>
    );
};

export default Cosmetic;
```

```js
# fakedb.js
// using local storage of browser 
const addToDb = id => {
    let shoppingCart = {};

    // get the shopping cart
    const storedCart = localStorage.getItem('shopping-cart');
    if (storedCart) {
        shoppingCart = JSON.parse(storedCart);
    }

    // get the quantity
    const quantity = shoppingCart[id];
    if (quantity) {
        shoppingCart[id] = quantity + 1;
    }
    else{
        shoppingCart[id] = 1;
    }

    localStorage.setItem('shopping-cart', JSON.stringify(shoppingCart));
}


// remove all quantity from cart
const removeFromDb = id => {

    // get the shopping cart
    const storedCart = localStorage.getItem('shopping-cart');
    if (storedCart) {
        const shoppingCart = JSON.parse(storedCart);

        // get the quantity
        if (id in shoppingCart) {       
            delete shoppingCart[id];
            localStorage.setItem('shopping-cart', JSON.stringify(shoppingCart));
        }
    }
}


// delete shopping cart
const deleteShoppingCart = () => {
    localStorage.removeItem('shopping-cart')
}


export{
    addToDb,
    removeFromDb,
    deleteShoppingCart
}
```


### Tailwind + Vite

```
# go to 
# https://tailwindcss.com/docs/installation/framework-guides

# follow the gideline after clicking Vite
# https://tailwindcss.com/docs/guides/vite
```

```
# icons for react application
# https://github.com/tailwindlabs/heroicons
```

### Axios
```js
// Axios is used to do some operations after loading data 
// and before sending data to useState

// https://axios-http.com/docs/intro
```
```sh
npm install axios
```
```js
// Phone.jsx
import React, { useEffect, useState } from 'react';
import axios from 'axios';
import Phone from '../Phone/Phone';

const Phones = () => {
    const [phones, setPhones] = useState([])

    useEffect( () => {
        // fetch('https://openapi.programming-hero.com/api/phones?search=iphone')
        // .then(res => res.json())
        // .then( data => setPhones(data))

        axios.get('https://openapi.programming-hero.com/api/phones?search=iphone')
        .then(data => {
            const phonesData = data.data.data
            // console.log(phonesData)
            {
                const newPhonesData = phonesData.map(phone => {
                    const parts = phone.slug.split('-')
                    // console.log(parts[1], typeof parts[1])
                    const price = parseInt(parts[1])

                    const updatedPhone = {...phone, price}
                    return updatedPhone;
                })
                // console.log(newPhonesData)
                setPhones(newPhonesData)
            }
        })
    }, [])


    return (
        <div>
            <h1>Welcome to mobile house</h1>
             <div>
                {
                    phones.map((phone, index) => <Phone 
                        key ={index}
                        phone={phone}
                    />)
                }
            </div>
        </div>
    );
};

export default Phones;
```

### Route

Follow: `https://reactrouter.com/en/main/start/tutorial`

```sh
npm install react-router-dom localforage match-sorter sort-by
```

```js
// main.jsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App.jsx'
import './index.css'
import {
  createBrowserRouter,
  RouterProvider,
} from "react-router-dom";

const router =  createBrowserRouter([
  {
    path: '/main',
    element: <div>This is main page from router</div>
  },
  {
    path: '/',
    element: <App />
  }
])

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>,
)
```

### Nested route + Link to fetch data without reloading
**Steps:**
- Set children inside createBrowserRouter()
- set <Outlet /> tag where to render
- Set Link tag (put a '/' at the begining of 'to' attribute inside of the Link tag)

```js
// main.jsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App.jsx'
import {
  createBrowserRouter,
  RouterProvider,
} from "react-router-dom";
import Home from './components/Home/Home.jsx';
import ErrorPage from './components/ErrorPage/ErrorPage.jsx';
import Contact from './components/Contact/Contact.jsx';
import About from './components/About/About.jsx';
import Test from './components/Test/Test.jsx';


const router = createBrowserRouter([
  // add children for navigating without reloading
  {
    path: '/',
    element: <App />,
    errorElement: <ErrorPage />,
    children: [
      {
        path: 'contacts/',
        element: <Contacts />
      },
      {
        path: 'home/',
        element: <Home />
      },
      {
        path: 'about/',
        element: <About />
      }
    ]
  },
  
  // test with reload
  {
    path: '/test',
    element: <Test />,
  },
  
  // * means all path except our mentioned path
  {
    path: '*',
    element: <ErrorPage />
  }
])


ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>,
)
```

```js
// App.jsx
import './App.css'
import Header from './components/Header/Header'
import { Outlet } from 'react-router-dom'

function App() {

    return (
        <div>
            <h1>This is main page</h1>
            <Header></Header>
            <Outlet />           // all childern component will be rendered inside Outlet tag
        </div>
    )
}

export default App;
```

```js
// components/Header/Header.jsx
import React from 'react';
import { Link } from 'react-router-dom';
import './Header.css';

const Header = () => {
    return (
        <nav>
            {/* // with reloading the page */}
            <a href="/test">Test</a>
            
            {/* // without reloading the page */}
            {/* put a '/' at the begining of 'to' attribute inside of the Link tag*/}
            <Link to="/home">Home</Link>
            <Link to="/about">About</Link>
            <Link to="/contacts">Contacts</Link>
        </nav>
    );
};

export default Header;
```


### Dynamic data loader + Loading Spinner + Dynamic routing

```js
// main.jsx

import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App.jsx'
import {
  createBrowserRouter,
  RouterProvider,
} from "react-router-dom";
import Contacts from './components/Contacts/Contacts.jsx';
import ContactDetail from './components/ContactDetail/ContactDetail.jsx';


const router = createBrowserRouter([
  {
    path: '/',
    element: <App />,
    children: [
      {
        path: 'contacts/',
        element: <Contacts />,
        loader: () => fetch('https://jsonplaceholder.typicode.com/users')
      },
      {
        path: 'contacts/:contactId',
        element: <ContactDetail />,
        // loader: ({params}) => console.log(params)
        loader: ({params}) => fetch(`https://jsonplaceholder.typicode.com/users/${params.contactId}`)
      },

    ]
  },
])

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>,
)
```

Here we will use Loding Spinner
```js
// App.jsx
import './App.css'
import Header from './components/Header/Header'
import { Outlet, useNavigation } from 'react-router-dom'

function App() {
    const navigation = useNavigation();
    return (
        <div>
            <h1>This is main page</h1>
            <Header></Header>
            
            // this will show Loading during loading data
            <div>{navigation.state === 'loading' && 'Loading...'}</div>
            
            // all childern component will be rendered inside 'Outlet' tag
            <Outlet />           
        </div>
    )
}

export default App;
```

```js
// components/Header/Header.jsx
import React from 'react';
import { Link } from 'react-router-dom';
import './Header.css';

const Header = () => {
    return (
        <nav>
            {/* with reloading the page */}
            <a href="/test">Test</a>
            
            {/* without reloading the page */}
            {/* put a '/' at the begining of 'to' attribute inside of the Link tag*/}
            <Link to="/about">About</Link>
            <Link to="/contacts">Contacts</Link>
        </nav>
    );
};

export default Header;
```

```js
// components/Contacts/Contacts.jsx

import React from 'react';
import { useLoaderData } from 'react-router-dom';
import Contact from '../Contact/Contact';

const Contacts = () => {
    const contacts = useLoaderData();
    // console.log(contacts)
    return (
        <div>
            <h1>Total Contacts: {contacts.length}</h1>
            {
                contacts.map(contact =>               
                <Contact 
                    key={contact.id}
                    contact={contact}
                />)
            }
        </div>
    );
};

export default Contacts ;
```


Dynamic routing
```js
// components/Contact/Contact.jsx
import React from 'react';
import './Contact.css'
import { Link, useNavigate } from 'react-router-dom';

const Contact = ({contact}) => {
    // console.log(contact)   
    const {name, id} = contact;

    // create navigation handler
    const navigate = useNavigate();
    const handleNavigation = () => {
        navigate(`/contacts/${id}`);
    }


    return (
        <div className='contact'>
            <h4>{name}</h4>

            {/* put a '/' at the begining of 'to' attribute inside of the Link tag*/}

            {/* 3 ways of dynamic routing */}

            {/* 1 - dynamic routing using link*/}
            <Link to={`/contacts/${id}`}><small>Show me detail</small></Link>

            {/* 2 - dynamic routing using link and button */}
            <Link to={`/contacts/${id}`}><button>Detail button</button></Link>

            {/* 3 - dynamic routing using useNavigation() hook*/}
            <button onClick={handleNavigation}>See Detail Using Hook</button>
        </div>
    );
};

export default Contact;
```

Use of `useLoaderData()` and `Go Back` button to navigate previous page
```js
// components/ContactDetail/ContactDetail.jsx
import React from 'react';
import { useLoaderData, useNavigate } from 'react-router-dom';

const ContactDetail = () => {
    const detail = useLoaderData()
    const {address, company, id, username, phone, email} = detail
    const {name} = company
    
    // create navigation handler
    const navigate = useNavigate();
    const handleNavigation = () => {
        navigate(-1);
    }
    
    return (
        <div>
            <p>Email: {email}</p>
            <p>Username: {username}</p>
            <p>Company name: {name}</p>
            {/* dynamic routing using useNavigation() hook to go back */}
            <button onClick={handleNavigation}>Go Back</button>
        </div>
    )
};

export default ContactDetail;
```

### Use of `ActiveLink`

Change the `Header.jsx` file of the previous section (Dynamic data loader + dynamic routing)

```js
// components/Header/Header.jsx

import React from 'react';
import { Link } from 'react-router-dom';
import ActiveLink from '../ActiveLink/ActiveLink';

const Header = () => {
    return (
        <nav>
            {/* without reloading the page */}
            {/* put a '/' at the begining of 'to' attribute inside of the Link tag*/}

            <ActiveLink to="/home">Home</ActiveLink>
            <ActiveLink to="/about">About</ActiveLink>
            <ActiveLink to="/contacts">Contacts</ActiveLink>
        </nav>
    );
};

export default Header;
```


```css
// ActiveLink.css
.active{
    background-color: red;
}
```

```
// components/ActiveLink/ActiveLink.jsx

import React from 'react';
import { NavLink } from 'react-router-dom';
import './ActiveLink.css';

const ActiveLink = ({to, children}) => {
    return (
        <NavLink
            to={to}
            className={({isActive}) => isActive ? "active" : ""}
        >
            {children}
        </NavLink>            
    );
};

export default ActiveLink;
```


### Use of `Websocket`
#### Server-Side (FastAPI)
```python
from fastapi import FastAPI, WebSocket, WebSocketDisconnect
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

origins = [
    'http://localhost',
    'http://localhost:8080',
    'http://localhost:5173',
]

app.add_middleware(
    CORSMiddleware,
    allow_origins=origins,
    allow_credentials=True,
    allow_methods=['*'],
    allow_headers=['*'],
)


# App root
@app.get('/', tags=['Root'])
async def root():
    return {'message': 'Welcome to this fantastic app!'}

# Store connected WebSocket clients
connected_clients = set()

# WebSocket endpoint to handle connections
@app.websocket('/ws/chat/{client_id}')
async def websocket_endpoint(client_id: str, websocket: WebSocket):
    await websocket.accept()
    print(f"WebSocket connection established for client {client_id}.")

    # Add the WebSocket to the set of connected clients
    connected_clients.add((client_id, websocket))
    print(connected_clients)

    try:
        while True:
            data = await websocket.receive_text()
            print("Message received:", data)

            # Handle incoming messages here
            # ............................
            # ............................
            # ............................

            # Broadcast the message to all connected clients
            for client in connected_clients:
                client_id, client_ws = client
                print('client_ws', client_ws)
                await client_ws.send_text(data)

    except WebSocketDisconnect:
        print("WebSocket connection closed.")

    finally:
        # Remove disconnected client from the set
        print(f'Remove disconnected client from the set {client_id}, {websocket}')
        connected_clients.remove((client_id, websocket))
        print(connected_clients)
```

```jsx
import { useEffect, useState } from "react";
import "./App.css";

function App() {
    const [messages, setMessages] = useState([]);
    const [messageText, setMessageText] = useState("");
    const [socket, setSocket] = useState(null); // State to store the WebSocket instance

    const client_id = "a4sfdkj490";
    const url = `ws://127.0.0.1:8000/ws/chat/${client_id}`;

    useEffect(() => {
        // Create a WebSocket instance
        const newSocket = new WebSocket(url);

        newSocket.onopen = () => {
            console.log("WebSocket connection established.");
        };

        newSocket.onmessage = (event) => {
            // console.log("Message received:", event);
            setMessages([...messages, event.data]);
        };

        newSocket.onclose = () => {
            console.log("WebSocket connection closed.");
        };

        setSocket(newSocket);

        return () => {
            // Clean up WebSocket when component unmounts
            console.log("WebSocket cleaned up.");
            newSocket.close();
        };
    }, [messages]);

    const handleFormSubmit = (e) => {
        e.preventDefault(); // Prevent the default form submission behavior

        if (socket) {
            socket.send(messageText);
            setMessageText("");
        }
    };

    return (
        <div>
            <div className="text-3xl">Chat app</div>
            <form onSubmit={handleFormSubmit}>
                <input
                    className="border border-red-500"
                    placeholder="Write here..."
                    id="message"
                    type="text"
                    value={messageText}
                    onChange={(e) => setMessageText(e.target.value)}
                />
                <button type="text" className="m-3 border py-1 rounded-md px-4">
                    <span>Send</span>
                </button>
                <div>
                    <h1>Messages</h1>
                    {messages.map((message, idx) => (
                        <p key={idx}>{message}</p>
                    ))}
                </div>
            </form>
        </div>
    );
}

export default App;

```



```jsx
import { useEffect, useState } from "react";
import "./App.css";
import { io } from "socket.io-client";



// npm install socket.io-client

function App() {
    const socket = io(`ws://127.0.0.1:8000/api/v1/ws/chat/${"a4sfdkj490"}`);
    const [messages, setMessages] = useState([]);
    const [message, setMessage] = useState("");

    // The useEffect hook in React allows to perform side effects in your functional components.
    // In the code you provided, a useEffect hook that listens for incoming messages
    // from a WebSocket server and updates the component's state (messages) accordingly

    // useEffect(() => {...}, [messages]):
    // This hook is executed when the component is mounted or whenever the messages variable changes.
    // The array [messages] in the second argument specifies the dependencies for this effect. In this case,
    // it means the effect will be re-run whenever messages changes.

    // return () => {...}: The useEffect hook can return a cleanup function.
    // In this case, it returns a function that will be executed when the component unmounts or when the dependencies change.
    // The purpose of this cleanup function is to disconnect the WebSocket (socket.disconnect()) to prevent
    // memory leaks and unexpected behavior when the component is no longer in use.

    useEffect(() => {
        socket.on("chat_message", (data) => {
            // chat_message is the event name defined in server side websocket.
            setMessages([...messages, data]);
        });
        return () => {
            socket.disconnect();
        };
    }, [messages]);

    const handleFormSubmit = () => {
        socket.emit("chat_message", message);
        setMessage("");
    };

    return (
        <div>
            <div className="text-3xl">Chat app</div>
            <form onSubmit={handleFormSubmit}>
                <input
                    className="border border-red-500"
                    placeholder="Write here..."
                    type="text"
                    value={message}
                    onChange={(e) => setMessage(e.target.value)}
                />
                <button
                    type="submit"
                    className="m-3 border py-1 rounded-md px-4"
                >
                    <span>Send</span>
                </button>
                <div>
                    <h1>Messages</h1>
                    {
                        messages.map(message => 
                            <p>message</p>
                        )
                    }
                </div>
            </form>
        </div>
    );
}

export default App;

```


The "message" event that you see in the code is part of the WebSocket communication and is typically defined and emitted from the WebSocket server. In the code examples I provided earlier, I demonstrated a basic setup for a WebSocket chat application.

Here's how it works:

1. **Server-Side (FastAPI):** In the FastAPI backend code, you define a WebSocket endpoint like this:

   ```python
   @app.websocket("/ws/{client_id}")
   async def websocket_endpoint(websocket: WebSocket, client_id: int):
       # ...
   ```

   Inside this WebSocket endpoint, you have a loop that listens for incoming messages from connected WebSocket clients:

   ```python
   while True:
       data = await websocket.receive_text()
       # ...
   ```

   When a message is received from a client, you can choose to emit it to all connected clients. In the code, it's broadcasted to all connected clients like this:

   ```python
   for client in connected_clients:
       _, client_ws = client
       await client_ws.send_text(f"Client {client_id}: {data}")
   ```

   This line sends the message with the "message" event from the server to all connected clients.

2. **Client-Side (React):** On the React client side, you use a WebSocket library (e.g., `socket.io-client` or a native WebSocket API) to establish a connection to the WebSocket server:

   ```javascript
   const socket = io("ws://localhost:8000/ws/1"); // Change the WebSocket URL as needed
   ```

   Then, you set up an event listener for the "message" event emitted from the server:

   ```javascript
   socket.on("message", (data) => {
     setMessages([...messages, data]);
   });
   ```

   In this code, when the WebSocket server emits a "message" event, the callback function is triggered, and it updates the client's state (`messages`) with the incoming chat message.

So, the "message" event is essentially the event that's emitted by the WebSocket server to indicate the arrival of a new chat message. It's defined and handled on both the server and client sides to facilitate real-time communication between clients.


### passdata from one component to another component 
```js
// https://dev.to/esedev/how-to-pass-and-access-data-from-one-route-to-another-with-uselocation-usenavigate-usehistory-hooks-1g5m#:~:text=Fortunately%2C%20React%20Router%20provides%20several,from%20the%20current%20location%20object.
```











