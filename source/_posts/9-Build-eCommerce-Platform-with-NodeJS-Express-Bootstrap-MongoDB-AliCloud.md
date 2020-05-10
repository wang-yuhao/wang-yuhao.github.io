---
title: 9-Build-eCommerce-Platform-with-NodeJS/Express/Bootstrap/MongoDB/AliCloud
date: 2020-04-09 15:19:59
tags:
---

## Introduction(说明):

<!--more-->
#### Menu(菜单): 
![menu.png](menu.png)

#### signup(注册): 
![signup.png](signup.png)

#### Products(商品): 
![products.png](products.png)

#### Shopping cart(购物车): 
![shoppingcart.png](shoppingcart.png)

#### Checkout(填写订单): 
![checkout1.png](checkout1.png)
![checkout2.png](checkout2.png)

#### Order(订单): 
![order.png](order.png)

#### Comment(建议): 
![comment.png](comments.png)


#### Last semester did a Paktikum in a german company, the main job was independently building an eCommerce Platform to sell german products to China customs, and this Website is the outcome. It should have more functions, but due to Corona-virus, the plan was forced to be interrupted, and more content will be perfected one after another.


#### This eCommerce Platform was designed primarily for a mobile device, Use Express structure, bootstrap, and handlebars for frontend, Nodejs control the backend, Mongodb store the database, early the project was deployed on the Azure, but due to the GFW limits the network speed, user can not open the Website fast, so have to change to AliCloud on HongKong, this server is efficiency, cheaper and doesn't need to register in the government department, it is an excellent choice for the global eCommerce Platform.

## Try to use different functionality to deconstruct the whole project and review the development process again.

#### 1.  Structure:
The first and most important part is to use npm install express framework for the eCommerce platform structure. 

+ You can set up middleware to respond to HTTP requests.

+ The routing table is defined to perform different HTTP request actions.

+ You can dynamically render HTML pages by passing parameters to the template.

#### 2. Router
You can think of it as a "mini-application", which can operate middleware and routing methods. Every Express program has a built-in app routing. You can use Router () to create a new router object.

#### 3. Use Mongoose to operate database:
create a user schema:
```
var mongoose = require('mongoose');
var Schema = mongoose.Schema;
var bcrypt = require('bcrypt-nodejs');

var userSchema = new Schema({
    username: {type: String, required: true},
    password: {type: String, required: true},
    searchHistory: [Object],
    advice: [Object],
    dreamList: [Object]
});

userSchema.methods.encryptPassword = function(password){
    return bcrypt.hashSync(password, bcrypt.genSaltSync(5), null);
};

userSchema.methods.validPassword = function(password){
    return bcrypt.compareSync(password, this.password);
}

module.exports = mongoose.model('User',userSchema)

```

use a user schema



#### 4. Pass values between frontend, backend, and database.
4.1 Pass values from frontend to backend
```
        <form action="/user/signup" method="post">
            <div class="form-group">
                <label for="username"> 用户名 </label>
                <input type="text" id="username" name="username" class="form-control">
            </div>
            <div class="form-group">
                 <label for="password">密码</label>
                 <input type="password" id="password" name="password" class="form-control">
            </div>
            <input type="hidden" name="_csrf" value="{{ csrfToken }}">
            <button class="btn btn-primary btn-center" type="submit"> 注册</button>
        </form>
```

4.2 Router receive the value from the frontend
```
passport.use('local.signup', new LocalStrategy({
    usernameField: 'username',
    passwordField: 'password',
    passReqToCallback: true
},
    function(req, username, password, done){
        const errors = validationResult(req).array()
        console.log(errors.length);
        if (errors.length>0) {
            var messages = [];
            for(i = 0; i < errors.length; i++){
                console.log(errors[i].msg);
                messages.push(errors[i].msg);
            }
         console.log(messages);
         return done(null, false, req.flash('error', messages));
        }

        User.findOne({'username': username}, function(err, user){
           if(err){
               console.log("err:"+err);
               return done(err);  
           }
           if(user){
               return done(null, false, {message: 'Email is already in use.'});
           }
           var newUser = new User();
           newUser.username = username;
           newUser.password = newUser.encryptPassword(password);
           newUser.save(function(err, result){
               if(err){
                   return done(err);
               }
               return done(null, newUser);
           });
      });
}));
```

4.3 Router store the data into database
+ Router find and dave one record
```
passport.use('local.signup', new LocalStrategy({
    usernameField: 'username',
    passwordField: 'password',
    passReqToCallback: true
},
    function(req, username, password, done){
        const errors = validationResult(req).array()
        console.log(errors.length);
        if (errors.length>0) {
            var messages = [];
            for(i = 0; i < errors.length; i++){
                console.log(errors[i].msg);
                messages.push(errors[i].msg);
            }
         console.log(messages);
         return done(null, false, req.flash('error', messages));
        }

        User.findOne({'username': username}, function(err, user){
           if(err){
               console.log("err:"+err);
               return done(err);  
           }
           if(user){
               return done(null, false, {message: 'Email is already in use.'});
           }
           var newUser = new User();
           newUser.username = username;
           newUser.password = newUser.encryptPassword(password);
           newUser.save(function(err, result){
               if(err){
                   return done(err);
               }
               return done(null, newUser);
           });
      });
}));
```
+ Router update and push one record from database
```
router.post('/dreamList', isLoggedIn, function(req, res, next){
    var userId = req.user.id;
    var user= req.user;
    var name= req.body.name;
    var description= req.body.description;

    User.updateOne({"_id": userId},{$push: {"dreamList": {"name":name, "description": description}}},{multi:1}, function(err){
        if(err){
            return res.write('Error!');
        }
        res.redirect('/dreamList');
    });
});
```

+ Router pass a record to frontend
```
router.post('/search', function(req, res, next){
    var replace = req.body.searchItem;
    var re = new RegExp(replace);
    Product.find({$or:[{title: re},{description: re}]}, function(err, products){
        res.render('shop/search-result', {title: 'search result', products: products, searchItem: replace});
    });
});
```

+ Javascript receive the data from the router

Js received two variables
```
var category = "{{category}}";
var products = {{{json products}}};
```

Router passed two variables in different way
```
/* GET second level page. */
router.get('/second-level/:category', function(req, res, next) {
  category = req.params.category;
  console.log(category);
  Product.find({category: category}, function(err, docs){
/*adjust the data sequence to fit the mobil interface by change productChunks to docs */
    res.render('shop/second-level', { title: 'Shopping cart', products: docs, category: category });
  });
});
```
The second way have to install handlebars-helper.

#### 5. [Handlebars](http://handlebarsjs.com/guide/#what-is-handlebars)
Handlebars is a simple templating language.

It uses a template and an input object to generate HTML or other text formats. Handlebars templates look like a regular text with embedded Handlebars expressions.

# if 
Use handlebars can simplify many steps
```
<div class="entry">
{{#if author}}
<h1>{{firstName}} {{lastName}}</h1>
{{/if}}
</div>
```

Input:
```
{
  author: true,
  firstName: "Yehuda",
  lastName: "Katz",
}
```
Output:
```
<div class="entry">
<h1>Yehuda Katz</h1>
</div>
```

# each
```
<ul class="people_list">
  {{#each people}}
    <li>{{this}}</li>
  {{/each}}
</ul>
```

Input: 
```
{
  people: [
    "Yehuda Katz",
    "Alan Johnson",
    "Charles Jolley",
  ],
}
```
Output:
```
<ul class="people_list">
    <li>Yehuda Katz</li>
    <li>Alan Johnson</li>
    <li>Charles Jolley</li>
</ul>
```

#### 6. [Bootstrap](https://getbootstrap.com/docs/4.4/getting-started/introduction/)
Bootstrap is an open-source toolkit for developing with HTML, CSS, and JS. Quickly prototype your ideas or build your entire app with our Sass variables and mixins, responsive grid system, extensive prebuilt components, and powerful plugins built on jQuery.

There are many beauty model, you can directly reference some model in you programm. Like:

+ Navbar
```
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <a class="navbar-brand" href="#">Navbar</a>
  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>

  <div class="collapse navbar-collapse" id="navbarSupportedContent">
    <ul class="navbar-nav mr-auto">
      <li class="nav-item active">
        <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">Link</a>
      </li>
      <li class="nav-item dropdown">
        <a class="nav-link dropdown-toggle" href="#" id="navbarDropdown" role="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
          Dropdown
        </a>
        <div class="dropdown-menu" aria-labelledby="navbarDropdown">
          <a class="dropdown-item" href="#">Action</a>
          <a class="dropdown-item" href="#">Another action</a>
          <div class="dropdown-divider"></div>
          <a class="dropdown-item" href="#">Something else here</a>
        </div>
      </li>
      <li class="nav-item">
        <a class="nav-link disabled" href="#" tabindex="-1" aria-disabled="true">Disabled</a>
      </li>
    </ul>
    <form class="form-inline my-2 my-lg-0">
      <input class="form-control mr-sm-2" type="search" placeholder="Search" aria-label="Search">
      <button class="btn btn-outline-success my-2 my-sm-0" type="submit">Search</button>
    </form>
  </div>
</nav>
```

+ Carousel:
```
<div id="carouselExampleControls" class="carousel slide" data-ride="carousel">
  <div class="carousel-inner">
    <div class="carousel-item active">
      <img src="..." class="d-block w-100" alt="...">
    </div>
    <div class="carousel-item">
      <img src="..." class="d-block w-100" alt="...">
    </div>
    <div class="carousel-item">
      <img src="..." class="d-block w-100" alt="...">
    </div>
  </div>
  <a class="carousel-control-prev" href="#carouselExampleControls" role="button" data-slide="prev">
    <span class="carousel-control-prev-icon" aria-hidden="true"></span>
    <span class="sr-only">Previous</span>
  </a>
  <a class="carousel-control-next" href="#carouselExampleControls" role="button" data-slide="next">
    <span class="carousel-control-next-icon" aria-hidden="true"></span>
    <span class="sr-only">Next</span>
  </a>
</div>
```

#### 6. Javascript 

Use javascript to display the products more flexible, especially when we can not ensure the count of the products.
```
        var div = document.getElementById("products-div");

        //create a new product card div
        var div_pro = document.createElement("div");
        div_pro.setAttribute("class","row");
        div.appendChild(div_pro);
        var div_pro_col = document.createElement("div");
        div_pro_col.setAttribute("class","def-col-sm-6");
        div_pro.appendChild(div_pro_col);
 
        var div_pro_col_link = document.createElement("a");
        div_pro_col_link.href = "/product/" + product._id;
        var div_pro_col_img = document.createElement("img");
        div_pro_col_img.setAttribute("class","img w-50");
        div_pro_col_img.src = product.imagePath;
```

#### 7. Decrypt and encrypt, force user to login
```
var LocalStrategy = require('passport-local').Strategy;
var {check, validationResult} = require('express-validator');

passport.serializeUser(function(user, done){
    done(null, user.id);
});

passport.deserializeUser(function(id, done){
    User.findById(id, function(err, user){
        done(err, user);
    });
});

passport.use('local.signup', new LocalStrategy({
    usernameField: 'username',
    passwordField: 'password',
    passReqToCallback: true
},
    function(req, username, password, done){
       ......
}));
```


## Feature Work
> + send email to admin mailbox
> + collect user visit and input data
> + analyse user data
> + support Wechat, Taobao and google account Login
> + add Wechatpay and Alipay Link
> + add more new interest product
> + provide prompt words in search input based on user history.
> + prompt words are provided according to the background products.
> + recommend products based on historical purchases and browsing history
> + add user chat
> + add a website and product reviews
> + add shipping method


+ Website: [germanfamily.eu](germanfamily.eu)
+ Github: [https://github.com/wang-yuhao/shopping-cart](https://github.com/wang-yuhao/shopping-cart)
