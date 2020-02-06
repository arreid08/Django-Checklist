# Django Checklist

*NOTE*: Use your own variable names throughout this!!! Also, don't add `my` onto your variables! That's just for the cheat sheet!

## Setup

### Install all dependencies and created our virtual environment
- [ ] Make a directory for the project
- [ ] Create a virtual environment `pip3 install virtualenv`
- [ ] `python3 venv .env` - .env can be named anything you want.
- [ ] `source .env/bin/activate` - this will activate the virtual environment
- [ ] Don't forget to active each time you work on your project!
- [ ] You should see your environment name in the line of your terminal (i.e. .env)
- [ ] Install Django `pip install django`
- [ ] Install the library for connection Django to PostgreSQL`pip install psycopg2-binary`

### Start the Django Project
- [ ] `django-admin startproject tunr_django .` - the . on the end will create the project without creating a subfolder. 
- [ ] Create our app: `django-admin startapp tunr` (tunr should be changed to the name of your app)
- [ ] 
- [ ] 
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  {{{ body }}}
</body>
</html>
```

## Connect to MongoDB

- [ ] `npm install mongoose`
- [ ] Create a `connection.js` file in the `db/` directory
- [ ] Require mongoose in the `connection` file
- [ ] Connect to your database: `mongoose.connect('mongodb://localhost/db_name')`
- [ ] Set the promise library: `mongoose.Promise = Promise` if you are using an older Mongoose version than v5.
- [ ] Export your setup mongoose: `module.exports = mongoose`

## Define a Schema + Model

- [ ] Create a file with your model's name in `models/`
- [ ] Import mongoose from your connection file: `const mongoose = require("../db/connection")`
- [ ] Create your schema and set it to a variable.
```js
const mySchema = new mongoose.Schema({
  field: DataType,
})
```
- [ ] Create a model with your schema: `const myModel = mongoose.model("modelName", mySchema)`
- [ ] Export your model: `module.exports = myModel`

## Seed your DataBase

- [ ] Create `db/seed.js`
- [ ] Import your schema from your model file: `const myModel = require('../models/myModel')`
- [ ] Create your seed data in JSON format in `db/seeds.json`
- [ ] Import your seed data into your `seed.js` file: `const seedData = require("./seeds.json")`
- [ ] Remove all of the items currently in your database: `myModel.remove({})`
- [ ] Insert your seeds into your database within the `.then` method.
- [ ] Exit the seeding process in another `.then` method.
```js
myModel.deleteMany({})
  .then(() => {
    return myModel.insertMany(seedData)
  })
  .then(() => {
    process.exit()
  })
```
- [ ] Run your seed file in the terminal: `$ node db/seed.js`
- [ ] Check to see if the items were inserted using the Mongo CLI.
  - [ ] Run `$ mongo` to enter the Mongo CLI
  - [ ] Run `> use my_database` and `db.my_collection.find()` to see your items

## Set Up your Controller

- [ ] Create a controller file: `controllers/myItems.js`
- [ ] Require your controller in `index.js`: `const myController = require('./controllers/myController')`
- [ ] Use your controller **below any other configuration**: `app.use("/myUrlPrefix", myController)`
- [ ] Require Express in your controller file: `const express = require("express")`
- [ ] Create a router instance in your controller file: `const router = express.Router()`
- [ ] Import your model: `const myModel = require('../models/myModel')`
- [ ] Export your router instance at the very bottom of the file: `module.exports = router`

## Create your Index Route

- [ ] Setup a GET handler for the '/' route: `router.get("/", (req, res) => {})`
- [ ] In the body of the GET handler, find all of the instances of your model: `myModel.find({})`
- [ ] Once the items are returned from your database, render your index view: `res.render('index', { myInstances })`
```js
router.get("/", (req, res) => {
  myModel.find({}).then(myInstances => res.render('index', { myInstances }))
})
```
- [ ] Create your view file: `views/index.hbs`
- [ ] Create your view template with Handlebars:
```hbs
<h1>myModel</h1>

<ul>
  {{#each myInstances as |myInstance|}}
    <li>
      {{myInstance.attribute}}
    </li>
  {{/each}}
</ul>
```

## Create your Show Route

- [ ] Setup a GET handler for the '/:id' route: `router.get("/:id", (req, res) => {})`
- [ ] In the body of the GET handler, find an instance of your model: `myModel.findOne({ _id: req.params.id })`
- [ ] Once the items are returned from your database, render your index view: `res.render('show', { myInstance })`
```js
router.get("/:id", (req, res) => {
  myModel.findOne({ _id: req.params.id }).then(myInstance => res.render('show', { myInstance }))
})
```
- [ ] Create your view file: `views/show.hbs`
- [ ] Create your view template with Handlebars:
```hbs
<h1>myModel</h1>

<h2>{{ myInstance.attribute }}</h2>
```

## Create your New Route

- [ ] Setup a GET handler to display the form at '/new' route: `router.get("/:id", (req, res) => {})` **note: put this above your `/:id` route!**
```js
router.get('/new', (req, res) => {
  res.render('new')
})
```
- [ ] Install body-parser: `$ npm install body-parser`
- [ ] Require body-parser in your `index.js`: `const parser = require('body-parser')`
- [ ] Setup body-parser in your `index.js`: `const parser = app.use(parser.urlencoded({ extended: true }))` **note: put this above where you `use` your controllers!**
- [ ] Create your view file for your form: `views/new.hbs`
- [ ] Create your view template with Handlebars:
```html
<h2>New Instance:</h2>

<form action="/" method="post" accept-charset="utf-8">
  <p>
    <label>myField:</label>
    <input type="text" name="myField"/>
  </p>
  <input type="submit" value="Create" />
</form>
```
- [ ] Setup a POST handler for your post reqest to create a new item at url `/`: `router.post("/", (req, res) => {})`
- [ ] In the body of the POST handler, create an instance of your model with the data from the form: `myModel.create(req.body)`
- [ ] Once the items are returned from your database, redirect to your home page: `res.redirect('/')`
```js
router.post('/', (req, res) => {
  myModel.create(req.body)
    .then(myNewItem => {
      res.redirect('/')
    })
})
```

## Create your Update Route

- [ ] Install method-override: `$ npm install method-override`
- [ ] Require `method-override` in your `index.js`: `const methodOverride = require('method-override')`
- [ ] Set up `method-override` in your `index.js`: `app.use(methodOverride('_method'))` **note: make sure this is above where you use your controller!**
- [ ] Create a route to render your `edit` form: `router.get('/edit/:id', (req, res) => {})`
- [ ] Find the item you want to edit in your database: `myModel.findOne({_id: req.params.id})`
- [ ] Render the form: `res.render('edit', { myInstance })`
```js
router.get('/edit/:id', (req, res) => {
  myModel.findOne({_id: req.params.id})
    .then(instance => {
      res.render("edit", { myInstance })
    })
})
```
- [ ] Create a form using handlebars for editing your item in `edit.hbs`
```hbs
<h2>Edit Item:</h2>

<form action="/{{myInstance.id}}?_method=put" method="post">
  <p>
    <label>Attribute:</label>
    <input type="text" value="{{myInstance.attribute}}" name="attribute"/>
  </p>
  <input type="submit" value="Update" />
</form>

```
- [ ] Create a route to edit the item in the database: `router.put('/:id', (req, res) => {})`
- [ ] Find an item in the database and edit it: `myModel.findOneandUpdate({_id: req.params.id}, req.body, { new: true })`
- [ ] Redirect to the home page: `res.redirect('/')`
```js
router.put('/:id', (req, res) => {
  myModel.findOneAndUpdate({_id: req.params.id}, req.body, { new: true })
    .then(myInstance => {
      res.redirect('/')
    })
})
```

## Create your Delete Route

- [ ] Create your delete route: `router.delete(':id', (req, res) => {})`
- [ ] Remove the item from the database: `myModel.findOneAndRemove({ _id: req.params.id })`
- [ ] Redirect to the home page: `res.redirect('/')`
```js
router.delete('/:id', (req, res) => {
  myModel.findOneAndRemove({ _id: req.params.id })
    .then(() => {
      res.redirect('/')
    })
})
```
- [ ] Add a form to delete your item in `edit.hbs`
```hbs
<form action="/{{myInstance.id}}?_method=delete" method="post">
  <input type="submit" value="Remove">
</form>
```

### Bonus: Set up CSS

- [ ] Create a `public/` folder with a `css/` subdirectory within it
- [ ] Create a `styles.css` file within `/public/css` and add your styling
- [ ] Host your `public/` folder: `app.use('/assets', express.static('public'))`
- [ ] Include your CSS in the head of your `views/layout.hbs` file: `<link rel="stylesheet" href="/assets/css/styles.css" />`

### Read More!

* [Lesson Plan](https://git.generalassemb.ly/ga-wdi-lessons/express-mongoose)
* [ToDo App](https://git.generalassemb.ly/ga-wdi-exercises/express-to-do/tree/solution)
* [WhenPresident App](https://git.generalassemb.ly/ga-wdi-exercises/w