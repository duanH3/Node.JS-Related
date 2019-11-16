# Node-Security-Notes

# TLS over HTTPS

Always use TLS encryption over https:// when working with forms so that the submitted data is encrypted when it’s sent 
across the Internet. If you send form data over http://, it’s sent in plain text and can be visible to anyone eavesdropping 
on those packets as they journey across the Internet.

# Wear Your Helmet

There’s a neat little middleware called helmet that adds some security from HTTP headers. 
It’s best to include right at the top of your middlewares and is super easy to include:

```
// server.js
const helmet = require('helmet')

middlewares = [
  helmet()
  // ...
]
```


# Cross-site Request Forgery (CSRF)

You can protect yourself against cross-site request forgery by generating a unique token when the user is presented with a form 
and then validating that token before the POST data is processed. There’s a middleware to help you out here as well:

```
// server.js
const csrf = require('csurf')

middlewares = [
  // ...
  csrf({ cookie: true })
]

```

In the GET request we generate a token:

```
// routes.js
router.get('/contact', (req, res) => {
  res.render('contact', {
    data: {},
    errors: {},
    csrfToken: req.csrfToken()
  })
})
```

And also in the validation errors response:

```
router.post('/contact', [
  // validations ...
], (req, res) => {
  const errors = validationResult(req)
  if (!errors.isEmpty()) {
    return res.render('contact', {
      data: req.body,
      errors: errors.mapped(),
      csrfToken: req.csrfToken()
    })
  }

  // ...
})
```

Then we just need include the token in a hidden input:
```
<!-- view/contact.ejs -->
<form method="post" action="/contact" novalidate>
  <input type="hidden" name="_csrf" value="<%= csrfToken %>">
  <!-- ... -->
</form>
```

That’s all that’s required.

We don’t need to modify our POST request handler as all POST requests will now require a valid token by the csurf middleware. If a valid CSRF token isn’t provided, a ForbiddenError error will be thrown, which can be handled by the error handler defined at the end of server.js.

You can test this out yourself by editing or removing the token from the form with your browser’s developer tools and submitting.


# Cross-site Scripting (XSS)

You need to take care when displaying user-submitted data in an HTML view as it can open you up to cross-site scripting(XSS). All template languages provide different methods for outputting values. The EJS <%= value %> outputs the HTML escaped value to protect you from XSS, whereas <%- value %> outputs a raw string.

Always use the escaped output <%= value %> when dealing with user submitted values. Only use raw outputs when you’re sure that’s is safe to do so.

# File Uploads

Uploading files in HTML forms is a special case that requires an encoding type of "multipart/form-data". See MDN’s guide to sending form data for more detail about what happens with multipart form submissions.

You’ll need additional middleware to handle multipart uploads. There’s an Express package named multer that we’ll use here:

```
// routes.js
const multer = require('multer')
const upload = multer({ storage: multer.memoryStorage() })

router.post('/contact', upload.single('photo'), [
  // validation ...
], (req, res) => {
  // error handling ...

  if (req.file) {
    console.log('Uploaded: ', req.file)
    // Homework: Upload file to S3
  }

  req.flash('success', 'Thanks for the message! I’ll be in touch :)')
  res.redirect('/')
})
```

This code instructs multer to upload the file in the “photo” field into memory and exposes the File object in req.file which we can inspect or process further.

The last thing we need is to add the enctype attribute and our file input:

```
<form method="post" action="/contact?_csrf=<%= csrfToken %>" novalidate enctype="multipart/form-data">
  <input type="hidden" name="_csrf" value="<%= csrfToken %>">
  <div class="form-field <%= errors.message ? 'form-field-invalid' : '' %>">
    <label for="message">Message</label>
    <textarea class="input" id="message" name="message" rows="4" autofocus><%= data.message %></textarea>
    <% if (errors.message) { %>
      <div class="error"><%= errors.message.msg %></div>
    <% } %>
  </div>
  <div class="form-field <%= errors.email ? 'form-field-invalid' : '' %>">
    <label for="email">Email</label>
    <input class="input" id="email" name="email" type="email" value="<%= data.email %>" />
    <% if (errors.email) { %>
      <div class="error"><%= errors.email.msg %></div>
    <% } %>
  </div>
  <div class="form-field">
    <label for="photo">Photo</label>
    <input class="input" id="photo" name="photo" type="file" />
  </div>
  <div class="form-actions">
    <button class="btn" type="submit">Send</button>
  </div>
</form>
```

Unfortunately, we also needed to include _csrf as a GET param so that the csurf middleware plays ball and doesn’t lose track of our token during multipart submissions.

Try uploading a file, you should see the File objects logged in the console.


# Populating File Inputs

In case of validation errors, we can’t re-populate file inputs like we did for the text inputs. A common approach to solving this problem involves these steps:

  -  uploading the file to a temporary location on the server
  -  showing a thumbnail and filename of the attached file
  -  adding JavaScript to the form to allow people to remove the selected file or upload a new one
  -  moving the file to a permanent location when everything is valid.

Because of the additional complexities of working with multipart and file uploads, they’re often kept in separate forms.

