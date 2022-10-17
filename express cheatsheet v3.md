### 0. To run with babel and environment variables and cors

```bash
npm install @babel/core @babel/node --save-dev
```

add to npm start script:

```json
    "start": "nodemon --exec babel-node src/index.js",
```

add most common used Babel preset to your application:

```bash
npm install @babel/preset-env --save-dev
```

create .babelrc file in the command line `touch .babelrc`

in this configuration file for Babel you can include the recently installed dependency:

```json
// .babelrc file
{
  "presets": ["@babel/preset-env"]
}
```

#### 2. .env with es6

1. `touch .env` to create file, `npm i dotenv` to install dotenv
2. import dotenv:

```js
// As early as possible in your application, import and configure dotenv:

require('dotenv').config();
console.log(process.env); // remove this after you've confirmed it is working
```

ES6 WAY like this:

```js
import 'dotenv/config';
```

or like this (from dotenv documentation):

```js
import * as dotenv from 'dotenv'; // see https://github.com/motdotla/dotenv#how-do-i-use-dotenv-with-import
dotenv.config();
```

IF YOU WANT TO USE ENVIRONMENT VARIABLES INSIDE OF OTHER MODULE, REMEMBER TO PUT IT FIRST:

```js
import 'dotenv/config';

import saySomething from './my-other-file.js';
```

IF YOU WANT TO CHECK IF WE ARE IN DEVELOPMENT:

```js
// means we are in development
if (process.env.NODE_ENV !== 'production') {
  require('dotenv').config();
}
```

#### 3. cors

CORS - cross-origin resource sharing

- application-level middleware to add the CORS HTTP header to every request by default. We could write a middleware ourseles or use a library.

- `npm install cors`
- `import cors from cors`
- `app.use(cors())`

### 1. require express

### 2. start an app with `const app = express()`

### 3. start listening with app.listen `app.listen(3000)`

### 4. setup view engine with `app.set('view engine', 'ejs');`

- you can do: `app.set('views', path.join(__dirname, 'views'));` to show where is the folder

### 5. make public folder public with `app.use(express.static('public'))`

You can call static multiple times to serve multiple directories (If it not find a file on one middleware then it checks the other one.)

you can also do a virtual prefix: `app.use('/media', express.static('public'));`

- you can do: `app.use(express.static(path.join(__dirname, 'public')));` if you want to be more pro

### 6. add body parser middleware

- to handle json: `app.use(express.json())`
- to handle forms (url encoding): `app.use(express.urlencoded({ extended: false }));`

### 6. add views and partials in `root/views/partials` with ejs `<%-include(./partials/head.ejs) >`

### 7. add css in head of every file with head partial and `src="/styles.css"`

### 8. start add addiing routes with

```js
app.get('/', (req, res) => {
  res.render('filename', { props: 'text', VariableSameAsKey });
});
```

### 9. To use routes:

```js
const routeRouter = require('./routes/route');
app.use('/route', routeRouter);
```

- inside of router:

```js
const router = express.Router();
router.get('/', () => {});
module.exports = router;
```

- you can also do this for varios requests:

```js
router
  .route('/:id')
  .get((req, res) => {
    console.log(req.params.id);
  })
  .put()
  .delete();
```

- access route parameters:

This is an url: `http://localhost:3000/users/34/books/8989`

```js
app.get('/users/:userId/books/:bookId', (req, res) => {
  // Access userId via: req.params.userId
  // Access bookId via: req.params.bookId
  res.send(req.params);
});
```

If you are using controllers:

```js
// require controller modules

const book_controller = require('../controllers/bookController');

// use one of the exported functions
router.get('/', book_controller.index);
```

### 10. add Controllers

Controllers take information from the database, work with it and send that information to the view so it can be shown.

Require models you are going to use:

```js
const Bicycle = require('../models/bicycle');
```

Controller scheleton:

```js
exports.author_detail = (req, res) => {
  res.send(`NOT IMPLEMENTED: Author detail: ${req.params.id}`);
};
```

Working controler function

```js
// Display list of all Books.
exports.book_list = function (req, res, next) {
  Book.find({}, 'title author')
    .sort({ title: 1 })
    .populate('author')
    .exec(function (err, list_books) {
      if (err) {
        return next(err);
      }
      //Successful, so render
      res.render('book_list', { title: 'Book List', book_list: list_books });
    });
};
```

### 10. add middleware (with next and app.use) `app.use((req, res, next) => {//code; next();});`

```js
const myLogger = function (req, res, next) {
  console.log('Request IP: ' + req.ip);
  console.log('Request Method: ' + req.method);
  console.log('Request date: ' + new Date());

  next(); // THIS IS IMPORTANT!
};

app.use(myLogger);
```

### 11. Add Views:

- to add a partial:

```html
<%- include('./partials/head.ejs') %>
```

- to declare a variable:

```html
<% const name = 'mario' %>
```

- to loop through (blogs is passed as a parameter):

```html
<!-- prettier-ignore -->
<div class="blogs content">
      <h2>All Blogs</h2>
      <p><%= name %></p>
      <% if (blogs.length > 0) { %>
        <% blogs.forEach((blog) => { %>
          <h3 class="title"><%= blog.title %></h3>
          <p class="snippet"><%= blog.snippet %></p>
        <% }) %>
      <% } else { %>
        <p>There are no blogs to display</p>
      <% } %>
    </div>
<!-- prettier-ignore -->
```

- to add props:

```js
res.render('index', { title: 'Home', blogs });
// express will look for that file in view folder
// it will choose index.ejs instead of index.html
```

- to use parameter inside partials:
  https://community.wappler.io/t/how-can-i-use-parameter-inside-partials/21877/4

- safer way to access props:

```html
<body>
  <!--'=' means output that code to the page -->
  <!-- locals will always be defined, and so no error -->

  Hello <%= locals.text || 'Default' %>
</body>
```

### 11. Add error handling with:

```js
app.use((req, res) => {
  res.status(404).render('404', { title: '404' });
});
```

MDN way:

```js
app.use(function (err, req, res, next) {
  console.error(err.stack);
  res.status(500).send('something broke!');
});
```

### 12. add redirects with `res.redirect('/path')`

### 13. Add forms:

#### 1. Adding Express-validator

- Specify functions you want to use:

```js
const { body, validationResult } = require('express-validator');
```

`body([fields, message])` - fields to validate or sanitze, and optional error message.

```js
  // .trim() - removes whitespace
  // isLength({ min: 1}) checks if it is not empty
  // .escape() to remove HTML characters from the variable
  // to prevent JavaScript cross-site scripting attacks.
  body('name', 'Empty name').trim().isLength({ min: 1 }).escape(),
```

```js
// optional to specify that null and apmty strings will NOT fail validation
body('age', 'Invalid age')
  .optional({ checkFalsy: true })
  .isISO8601()
  .toDate(),
```

Add messages that are displayed if the preceding validators are true.

```js
body("name")
  .trim()
  .isLength({ min: 1 })
  .withMessage("Name empty.")
  .isAlpha()
  .withMessage("Name must be alphabet letters."),
```

`validationResult(req)` - runs the validation returns an error if there was an.

```js
(req, res, next) => {
  // Extract the validation errors from a request.
  const errors = validationResult(req);

  if (!errors.isEmpty()) {
    // wtf is that shit
    // There are errors. Render form again with sanitized values/errors messages.
    // Error messages can be returned in an array using `errors.array()`.
    errors.array().forEach((error) => {
      console.log(error);
    });
  } else {
    // Data from form is valid.
  }
};
```

#### 2. Form Design

- form can only create an object using objects that already exist
- delete an object that is not referenced by other objects!

#### 3.Routes

get - to display
post - to validate, save and redirect or errors

```js
// GET request for creating a Genre. NOTE This must come before route that displays Genre (uses id).
router.get('/genre/create', genre_controller.genre_create_get);

// POST request for creating Genre.
router.post('/genre/create', genre_controller.genre_create_post);
```

#### 4. Create get route

```js
// Display Genre create form on GET.
exports.genre_create_get = (req, res, next) => {
  res.render('genre_form', { title: 'Create Genre' });
};
```

#### 5. Create a post route

```js
// Handle Genre create on POST.
// why it is an array?
// the array of middleware functions is passed to the router function and each method is called in order.
// validators are middleware funcitons.
exports.genre_create_post = [
  // Validate and sanitize the name field.
  body('name', 'Genre name required').trim().isLength({ min: 1 }).escape(),

  // Process request after validation and sanitization.
  (req, res, next) => {
    // Extract the validation errors from a request.
    const errors = validationResult(req);

    // Create a genre object with escaped and trimmed data.
    const genre = new Genre({ name: req.body.name });

    if (!errors.isEmpty()) {
      // There are errors. Render the form again with sanitized values/error messages.
      res.render('genre_form', {
        title: 'Create Genre',
        genre,
        errors: errors.array(),
      });
      return;
    } else {
      // Data from form is valid.
      // Check if Genre with same name already exists.
      Genre.findOne({ name: req.body.name }).exec((err, found_genre) => {
        if (err) {
          return next(err);
        }

        if (found_genre) {
          // Genre exists, redirect to its detail page.
          res.redirect(found_genre.url);
        } else {
          genre.save((err) => {
            if (err) {
              return next(err);
            }
            // Genre saved. Redirect to genre detail page.
            res.redirect(genre.url);
          });
        }
      });
    }
  },
];
```

#### 6. Create a View

```js
extends layout

block content
  h1 #{title}

  form(method='POST' action='')
    div.form-group
      label(for='name') Genre:
      input#name.form-control(type='text', placeholder='Fantasy, Poetry etc.' name='name' value=(undefined===genre ? '' : genre.name))
    button.btn.btn-primary(type='submit') Submit

  if errors
   ul
    for error in errors
     li!= error.msg

```

#### 7. Adding a book form:

Controller:

```js
// Display book create form on GET.
exports.book_create_get = (req, res, next) => {
  // Get all authors and genres, which we can use for adding to our book.
  async.parallel(
    {
      authors(callback) {
        Author.find(callback);
      },
      genres(callback) {
        Genre.find(callback);
      },
    },
    (err, results) => {
      if (err) {
        return next(err);
      }
      res.render('book_form', {
        title: 'Create Book',
        authors: results.authors,
        genres: results.genres,
      });
    }
  );
};
```

Controller-post route:

```js
// Handle book create on POST.
exports.book_create_post = [
  // Convert the genre to an array.
  (req, res, next) => {
    if (!Array.isArray(req.body.genre)) {
      req.body.genre =
        typeof req.body.genre === 'undefined' ? [] : [req.body.genre];
    }
    next();
  },

  // Validate and sanitize fields.
  body('title', 'Title must not be empty.')
    .trim()
    .isLength({ min: 1 })
    .escape(),
  body('author', 'Author must not be empty.')
    .trim()
    .isLength({ min: 1 })
    .escape(),
  body('summary', 'Summary must not be empty.')
    .trim()
    .isLength({ min: 1 })
    .escape(),
  body('isbn', 'ISBN must not be empty').trim().isLength({ min: 1 }).escape(),
  body('genre.*').escape(),

  // Process request after validation and sanitization.
  (req, res, next) => {
    // Extract the validation errors from a request.
    const errors = validationResult(req);

    // Create a Book object with escaped and trimmed data.
    const book = new Book({
      title: req.body.title,
      author: req.body.author,
      summary: req.body.summary,
      isbn: req.body.isbn,
      genre: req.body.genre,
    });

    if (!errors.isEmpty()) {
      // There are errors. Render form again with sanitized values/error messages.

      // Get all authors and genres for form.
      async.parallel(
        {
          authors(callback) {
            Author.find(callback);
          },
          genres(callback) {
            Genre.find(callback);
          },
        },
        (err, results) => {
          if (err) {
            return next(err);
          }

          // Mark our selected genres as checked.
          for (const genre of results.genres) {
            if (book.genre.includes(genre._id)) {
              genre.checked = 'true';
            }
          }
          res.render('book_form', {
            title: 'Create Book',
            authors: results.authors,
            genres: results.genres,
            book,
            errors: errors.array(),
          });
        }
      );
      return;
    }

    // Data from form is valid. Save book.
    book.save((err) => {
      if (err) {
        return next(err);
      }
      // Successful: redirect to new book record.
      res.redirect(book.url);
    });
  },
];
```

We then use a wildcard (\*) in the sanitizer to individually validate each of the genre array entries. The code below shows how - this translates to "sanitize every item below key genre".

```js
[
  // …
  body('genre.*').escape(),
  // …
];
```

### 14. Delete Author form:

Only allow to delete objects that are not referenced

Controller-get

```js
// Display Author delete form on GET.
exports.author_delete_get = (req, res, next) => {
  async.parallel(
    {
      author(callback) {
        Author.findById(req.params.id).exec(callback);
      },
      authors_books(callback) {
        Book.find({ author: req.params.id }).exec(callback);
      },
    },
    (err, results) => {
      if (err) {
        return next(err);
      }
      if (results.author == null) {
        // No results.
        res.redirect('/catalog/authors');
      }
      // Successful, so render.
      res.render('author_delete', {
        title: 'Delete Author',
        author: results.author,
        author_books: results.authors_books,
      });
    }
  );
};
```

Controller post route:

```js
// Handle Author delete on POST.
exports.author_delete_post = (req, res, next) => {
  async.parallel(
    {
      author(callback) {
        Author.findById(req.body.authorid).exec(callback);
      },
      authors_books(callback) {
        Book.find({ author: req.body.authorid }).exec(callback);
      },
    },
    (err, results) => {
      if (err) {
        return next(err);
      }
      // Success
      if (results.authors_books.length > 0) {
        // Author has books. Render in same way as for GET route.
        res.render('author_delete', {
          title: 'Delete Author',
          author: results.author,
          author_books: results.authors_books,
        });
        return;
      }
      // Author has no books. Delete object and redirect to the list of authors.
      Author.findByIdAndRemove(req.body.authorid, (err) => {
        if (err) {
          return next(err);
        }
        // Success - go to author list
        res.redirect('/catalog/authors');
      });
    }
  );
};
```

In view:

```html
<a href='<%= locals.bicycle.url + "/delete" %>'>Delete</a> this bicycle
```

In route:

```js
// POST request to delete Author.
router.post('/author/:id/delete', author_controller.author_delete_post);
```

Delete button:
make action empty to send post request to the same page you are on.

```js
<form method="post" action="">
  <p>Are you sure you want to delete <%= locals.bicycle.name %>?</p>
  <button type="submit">Delete</button>
</form>
```

do it before alone :id

### 15. Update Book form:

Controller-get route:

```js
// Display book update form on GET.
exports.book_update_get = (req, res, next) => {
  // Get book, authors and genres for form.
  async.parallel(
    {
      book(callback) {
        Book.findById(req.params.id)
          .populate('author')
          .populate('genre')
          .exec(callback);
      },
      authors(callback) {
        Author.find(callback);
      },
      genres(callback) {
        Genre.find(callback);
      },
    },
    (err, results) => {
      if (err) {
        return next(err);
      }
      if (results.book == null) {
        // No results.
        const err = new Error('Book not found');
        err.status = 404;
        return next(err);
      }
      // Success.
      // Mark our selected genres as checked.
      for (const genre of results.genres) {
        for (const bookGenre of results.book.genre) {
          if (genre._id.toString() === bookGenre._id.toString()) {
            genre.checked = 'true';
          }
        }
      }
      res.render('book_form', {
        title: 'Update Book',
        authors: results.authors,
        genres: results.genres,
        book: results.book,
      });
    }
  );
};
```

Controller-post route:

```js
// Handle book update on POST.
exports.book_update_post = [
  // Convert the genre to an array
  (req, res, next) => {
    if (!Array.isArray(req.body.genre)) {
      req.body.genre =
        typeof req.body.genre === 'undefined' ? [] : [req.body.genre];
    }
    next();
  },

  // Validate and sanitize fields.
  body('title', 'Title must not be empty.')
    .trim()
    .isLength({ min: 1 })
    .escape(),
  body('author', 'Author must not be empty.')
    .trim()
    .isLength({ min: 1 })
    .escape(),
  body('summary', 'Summary must not be empty.')
    .trim()
    .isLength({ min: 1 })
    .escape(),
  body('isbn', 'ISBN must not be empty').trim().isLength({ min: 1 }).escape(),
  body('genre.*').escape(),

  // Process request after validation and sanitization.
  (req, res, next) => {
    // Extract the validation errors from a request.
    const errors = validationResult(req);

    // Create a Book object with escaped/trimmed data and old id.
    const book = new Book({
      title: req.body.title,
      author: req.body.author,
      summary: req.body.summary,
      isbn: req.body.isbn,
      genre: typeof req.body.genre === 'undefined' ? [] : req.body.genre,
      _id: req.params.id, //This is required, or a new ID will be assigned!
    });

    if (!errors.isEmpty()) {
      // There are errors. Render form again with sanitized values/error messages.

      // Get all authors and genres for form.
      async.parallel(
        {
          authors(callback) {
            Author.find(callback);
          },
          genres(callback) {
            Genre.find(callback);
          },
        },
        (err, results) => {
          if (err) {
            return next(err);
          }

          // Mark our selected genres as checked.
          for (const genre of results.genres) {
            if (book.genre.includes(genre._id)) {
              genre.checked = 'true';
            }
          }
          res.render('book_form', {
            title: 'Update Book',
            authors: results.authors,
            genres: results.genres,
            book,
            errors: errors.array(),
          });
        }
      );
      return;
    }

    // Data from form is valid. Update the record.
    Book.findByIdAndUpdate(req.params.id, book, {}, (err, thebook) => {
      if (err) {
        return next(err);
      }

      // Successful: redirect to book detail page.
      res.redirect(thebook.url);
    });
  },
];
```

# Mongoose:

### 1. require mongoose with `const mongoose = require('mongoose')`

### 2. connect to mongoose with `mongoose.connect('url', () => {success}, (err) => {clg(err)}`

- url to local connect: `'mongodb://localhost/testdb'`
- or do: `mongoose.connect('url').then((result) => {success}).catch((err) => {})`
- or do:

```js
// Import the mongoose module
const mongoose = require('mongoose');

// Set up default mongoose connection
const mongoDB = 'mongodb://127.0.0.1/my_database';
mongoose.connect(mongoDB, { useNewUrlParser: true, useUnifiedTopology: true });

// Get the default connection (Connection Object)
const db = mongoose.connection;

// Bind connection to error event (to get notification of connection errors)
db.on('error', console.error.bind(console, 'MongoDB connection error:'));
// if you need additional connection use mongoose.createConnection() which returns Connection object
```

### 3. create schema:

```js
const newSchema = new mongoose.Schema({
  field: value,
});
```

### 4. add methods to schema - virtual, static or methods, use normal funcitons

### 5. add middleware to schema .pre, .post

### 6. export schema as a model with `module.exports = mongoose.model('user', userSchema);`

### 7. create documents using `const user = await User.create({})`

- or do: `const user = new User({}); user.save();` (save returns a promise, you can do `.then((result) => {}).catch((err) => {}))`

# Documents setup:

- `node_modules`
- `views/partials` - views for normal views and partials to use inside of that views
- `routes` - have your User.js and other express routes
- `public` - has your public files like styles.ss `public/images` or `public/javascripts` or `public/stylesheets`
- `models` - for mongoose models
- `app.js` - main app

# Using Curl:

```
curl http://localhost:3000
-> Received a GET HTTP method

curl -X POST http://localhost:3000
-> Received a POST HTTP method

curl -X PUT http://localhost:3000
-> Received a PUT HTTP method

curl -X DELETE http://localhost:3000
-> Received a DELETE HTTP method
```

# REST API

I just copied the whole code

```js
const express = require('express');
const router = express.Router();
const Subscriber = require('../models/subscriber');

// Getting all
router.get('/', async (req, res) => {
  try {
    const subscribers = await Subscriber.find();
    res.json(subscribers);
  } catch (err) {
    res.status(500).json({ message: err.message });
  }
});

// Creating One
router.post('/', async (req, res) => {
  const subscriber = new Subscriber({
    name: req.body.name,
    subscriberToChannel: req.body.subscriberToChannel,
  });
  try {
    const newSubscriber = await subscriber.save();
    // 201 means you created something
    res.status(201).json(newSubscriber);
  } catch (error) {
    // 400 - something is wrong with user data
    res.status(400).json({ message: error.message });
  }
});

// Getting one
router.get('/:id', getSubscriber, (req, res) => {
  res.json(res.subscriber);
});

// Updating One
router.patch('/:id', getSubscriber, async (req, res) => {
  if (req.body.name != null) {
    res.subscriber.name = req.body.name;
  }
  if (req.body.subscriberToChannel != null) {
    res.subscriber.subscriberToChannel =
      req.body.subscriber.subscriberToChannel;
  }
  try {
    const updatedSubscriber = await res.subscriber.save();
    res.json(updatedSubscriber);
  } catch (error) {
    res.status(400).json({ message: err.message });
  }
});

// Deleting One
router.delete('/:id', getSubscriber, async (req, res) => {
  try {
    await res.subscriber.remove();
    res.json({ message: 'Deleted Subscriber' });
  } catch (error) {
    res.status(500).json({ message: error.message });
  }
});

async function getSubscriber(req, res, next) {
  let subscriber;
  try {
    subscriber = await Subscriber.findById(req.params.id);

    if (subscriber == null) {
      return res.status(404).json({ message: 'Cannot find subscriber' });
    }
  } catch (error) {
    res.status(500).json({ message: error.message });
  }
  res.subscriber = subscriber;
  next();
}

module.exports = router;
```

# .rest files

1. Create `<filename>.rest` file
2. Test with:

```
DELETE http://localhost:3000/subscribers/633f20ab0af00aa106f0f9df

###

PATCH http://localhost:3000/subscribers/633f254f560ea3fa7bbc54b1
Content-Type: application/json

{
  "name": "new name"
}
```

if you want to include a token:

```
POST  http://localhost:5000/api/posts
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjp7ImlkIjoxLCJ1c2VybmFtZSI6ImJyYWQiLCJlbWFpbCI6ImJyYW5kQGdtYWlsLmNvbSJ9LCJpYXQiOjE2NjYwMjA3OTYsImV4cCI6MTY2NjAyMDgyNn0.f2hR2a9niOS5vKCO1XzFha7pCRtZYUCziGaJK6Jlq7A
```

etc.

# PASSWORDJS - AUTHENTICATION - JWT TOKENS - BCRYPT

## BCRYPT:

You can install either bcryptjs (odin recommended) or bcrypt which is written in c++.
The second one is faster but might require more setup.

1. require bcrypt: `const bcrypt = require('bcrypt')` or `const bcrypt = require('bcryptjs')`
2. Setup register page with `const hashedPassword = await bcrypt.hash(req.body.password, 10)`:

```js
try {
  // leave empty for default (10)
  const salt = await bcrypt.genSalt()
  const hashedPassword = await bcrypt.hash(req.body.password, salt)
} catch (error) {
  res.status(500).json(error: error.message)
}
```

or simpler:

```js
try {
  const hashedPassword = await bcrypt.hash(req.body.password, 10);
  const user = { name: req.body.name, password: hashedPassword };
  users.push(user);
  res.status(201).json(user);
} catch {
  res.status(500).json({error.message})
}
```

3. Setup logging in with `if(await bcrypt.compate(req.body.password, user.password)`:

```js
app.post('/users/login', async (req, res) => {
  const user = users.find((user) => user.name === req.body.name);
  if (user == null) {
    return res.status(400).send('Cannot find user');
  }
  try {
    if (await bcrypt.compare(req.body.password, user.password)) {
      res.send('Success');
    } else {
      res.send('Not Allowed');
    }
  } catch (error) {
    res.status(500).send();
  }
});
```

## PASSWORD-JS

1. install: `passport`, `passport-local`, `express-session`

```js
const passport = require('passport');
const session = require('express-session');
const LocalStrategy = require('passport-local').Strategy;
```

2. setup session:

```js
app.use(
  session({
    // secret code, it is the best to take from environmental variables
    secret: process.env.SESSION_SECRET,
    // should we resave our session variables if nothing is changed
    resave: false,
    // do you want to save an empty value if there is no value
    saveUninitialized: false,
  })
);
```

Odin project version: `app.use(session({ secret: 'cats', resave: false, saveUninitialized: true }));`

3. setup passport:

```js
app.use(passport.initialize());
app.use(passport.session());
```

4. setup LocalStrategy

```js
// usernameField is an option to change username from default 'username'
// we are using a function authenticateUser for cleaner code
// authenticate user is returning a done(err, <userdata>, <message>)
passport.use(
  new LocalStrategy({{ usernameField: 'email' }},(email, password, done) => {
    // try to rewrite it to be an async function instead of a callback!!!
    User.findOne({ email: email }, async (err, user) => {
      if (err) {
        return done(err);
      }
      if (!user) {
        return done(null, false, { message: 'Incorrect email' });
      }
      // compare encrypted password with one passed by the user (user.password)
      const match = await bcrypt.compare(password, user.password);
      if (match) {
        return done(null, user);
      } else {
        return done(null, false, { message: 'Incorrect password' });
      }
    });
  })
);
```

5. setup `serializeUser` and `deserializeUser`

```js
// TO: make sure user is logged in, stay logged in we create a cookie which is stored in user's browser.

// These next two functions define what bit of information passport is looking for when it creates and then decodes the cookie.

passport.serializeUser(function (user, done) {
  done(null, user.id);
});

passport.deserializeUser(function (id, done) {
  User.findById(id, function (err, user) {
    done(err, user);
  });
});
```

6. Setup login

```js
app.post(
  '/log-in',
  passport.authenticate('local', {
    successRedirect: '/',
    failureRedirect: '/',
  })

// authenticate() middleware looks at the request body for parameters named username and password and then runs the LocalStrategy function.
// Then it creates a session cookie that gets stored in the users browser
// We can access that cookie in future requests and we have information about login
// It redirects based if the login is success or not
// Cookies are comming with req object
```

7. Setup Logout

Can be also on get - if you want to use delete use `method-override` module

```js
app.delete('/logout', (req, res, next) => {
  // log out is something that password registers for us
  // logOut takes a callback
  req.logOut((err) => {
    if (err) {
      return next(err);
    }
  });
  res.redirect('/login');
});
```

7. OPTIONAL (Functions to check is user is Authenticated, and based of that we can redirect him):

```js
function checkAuthenticated(req, res, next) {
  if (req.isAuthenticated()) {
    return next();
  } else {
    res.redirect('/login');
  }
}

function checkNotAuthenticated(req, res, next) {
  if (req.isAuthenticated()) {
    res.redirect('/');
  } else {
    next();
  }
}
```

8. OPTIONAL (simplify how we access the user):

```js
// this function simplifies how we access user:
app.use((req, res, next) => {
  res.locals.currentUser = req.user;
  // insert this code between where you instantiate the passport and before you render your views
  // you will have assess to currentUser in all of your views without passing it into all of the controllers
  next();
});
```

## JWT

1. Require `const jwt = require('jsonwebtoken`)`
2. Generate token after successful login:

```js
app.post('/api/login', (req, res) => {
  // Mock user
  const user = {
    id: 1,
    username: 'brad',
    email: 'brand@gmail.com',
  };
  // first parameter is user, next one is secret key (can be in .etv), third one is callback or options and then callback
  jwt.sign({ user: user }, 'secretkey', { expiresIn: '30s' }, (err, token) => {
    res.json({
      token: token,
      headers: req.headers,
    });
  });
});
```

We need to save that token in local storage and use it when accessing diffrent routes

3. create a verify token function:

Traversy version:

```js
// verify token
function verifyToken(req, res, next) {
  console.log(req.headers);
  // Get auth header value
  // when we send our token we want to send in the header
  const bearerHeader = req.headers['authorization'];
  // check if bearer is NOT undefined
  if (typeof bearerHeader !== 'undefined') {
    // Split at the space (Bearer <access_token>)
    const bearer = bearerHeader.split(' ');
    // get token from array
    const bearerToken = bearer[1];
    // Set the token
    req.token = bearerToken;
    // Next middleware
    next();
  } else {
    // forbidden
    res.sendStatus(403);
  }
}
```

WebDevSimplified version:

```js
function authenticateToken(req, res, next) {
  const authHeader = req.headers['authorization'];
  // check if authHeader is undefined if not return authHeader.split(' ')[1]
  const token = authHeader && authHeader.split(' ')[1];
  // if undefined or null return error
  if (token == null) return res.sendStatus(401);

  jwt.verify(token, process.env.ACCESS_TOKEN_SECRET, (err, user) => {
    console.log(err);
    if (err) return res.sendStatus(403);
    // else if positive save user (authenticationData) as req.user (similar how it is happening in the session)
    req.user = user;
    next();
  });
}

// LATER YOU CAN EASILY USE THAT AS A MIDDLEWARE

app.get('/posts', authenticateToken, (req, res) => {
  // we will get the posts that are made by logged in person
  res.json(posts.filter((post) => post.username === req.user.name));
});
```

4. check the token when accessing to routes that need veryfying.

```js
// this is a protected route
// we will pass middleware to verify token

// let's try to make a request to post which has verifyToken,
// if we don't send a token we should get forbidden
app.post('/api/posts', verifyToken, (req, res) => {
  // token is now in request
  jwt.verify(req.token, 'secretkey', (err, authData) => {
    if (err) {
      res.sendStatus(403);
    } else {
      res.json({
        message: 'Post created...',
        authData,
      });
      /*   "authData": {
    "user": {
      "id": 1,
      "username": "brad",
      "email": "brand@gmail.com"
    },
    "iat": 1666020681,
    "exp": 1666020711
  }
} */
    }
  });
});
```

# A PRACTICAL GUIDE FOR JWT AUTHENTICATION USING NODE.JS AND EXPRESS

https://laptrinhx.com/a-practical-guide-for-jwt-authentication-using-node-js-and-express-917791379/

## Stateless solution for authentication

## Introduction

Traditional authentication strategy makes use of sessions and cookies, but scaling those solutions is very difficult - as some kind of state is maintained by the server.
JWT is stateless and stateless apps are easy to scale.

## Advantages of JWT

- Stateless authentication solution
- Very popular
- Easy to verify
- can be more trustworthy than cookies and sessions
- authentication can be outsorced

## What is JWT?

JSON Web Token, composed of:

- header
- payload
- signature

Tak to sie zmienia w node.js

```js
Buffer.from(
  JSON.stringify({
    alg: 'HS256',
    typ: 'JWT',
  })
)
  .toString('base64')
  .replace(/=/g, '')
  .replace(/\+/g, '-')
  .replace(/\//g, '_');
```

## Head:

```js
{
  "alg": "HS256",
  "typ": "JWT"
}
```

## Body:

```js
{
  "sub": "90129920",
  "uuid": "sfgdsrfg434fdt535fg",
  "iat": 1516239022,
  "exp": 1545926973,
}
```

Time is in seconds, following the 1 January 1970. Also converted to Base64URL.

## Signature

Used to verify if iit is valid and authentic

This is how it is created:

```js
signature = sha256(base64URL(head) + '.' + base64URL(body));
```

body and the head encrypted using any hashing algorithm — for example, SHA-256.

## Using JWT with Node.js

If you don't want to write your own custom JWT solution, you can always use an npm module for it.
