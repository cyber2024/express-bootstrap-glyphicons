I had trouble using bootstrap's glyphicons with an express app... they simply weren't being loaded, so here is the solution.
It's pretty basic, it just works.

1) $express -> create your base express app
2) $npm install -> install dependencies from generated express package.json
3) Test that your server is running by starting your server then opening your browser and pointing it at your server
  $npm start
  open your browser, navigate to http://localhost:3000
  This should show an Express welcome page.
3) Download bootstap v3.3.6 http://getbootstrap.com/getting-started/#download
4) extract folder (js, css, fonts) into public/bootstrap/
  public
          --- bootstrap --- js
                        --- css
                        ---fonts
          --- images
          --- javascripts
          --- stylesheets

5) add link to bootstrap.css in layout.jade
  layout.jade -> add the following line
  link(rel='stylesheet', href='bootstrap/css/bootstrap.css')
6) add some sweet bootstrap style to your index.jade file
  index.jade -> modify block content as follows

  block content
    h1= title
    p Welcome to #{title}
    div(class="jumbotron")

7) test that bootstrap is being loaded by heading back to http://localhost:3000
    This should show the same Express welcome page but with a jumbotron below it... basically a grey box
    If you see this, then bootstrap is working.
    Additionally you could simply press Ctrl+Shift+J in Chrome (windows), click on the 'Source' tab and check which source files have been loaded. bootstrap.css should be listed there.

8) Add a glyphicon into your index.jade block content
    block content
      h1= title
      p Welcome to #{title}
      div(class="jumbotron")
        span(class="glyphicon glyphicon-edit") glyphicon-edit

    Open your browser and point at http://localhost:3000
    CHECK OUT YOUR GLYPHICON IN ALL ITS GLORY!
    ...but we arent finished. What if you are using Grunt or something that is relocating your fonts, or if you want a different setup?

8) Now bootstrap.css has the following code which points toward the glyphicon assets

    @font-face {
      font-family: 'Glyphicons Halflings';

      src: url('../fonts/glyphicons-halflings-regular.eot');
      src: url('../fonts/glyphicons-halflings-regular.eot?#iefix') format('embedded-opentype'), url('../fonts/glyphicons-halflings-regular.woff2') format('woff2'), url('../fonts/glyphicons-halflings-regular.woff') format('woff'), url('../fonts/glyphicons-halflings-regular.ttf') format('truetype'), url('../fonts/glyphicons-halflings-regular.svg#glyphicons_halflingsregular') format('svg');
    }
    provided that your bootstrap folders (css, js, fonts) are located in the same place relative to each other as they were in the bootstrap.zip file, the hardcoded paths will work.

    If you want to change your /font/ location, you need to do two things:
    you need to understand how bootstrap is finding it...
    src: url('../fonts/glyphicons-halflings-regular.eot');
    this means that from the bootstrap.css location (public/bootstrap/css), go up one folder (public/bootstrap), then go into fonts (public/bootstrap/fonts) and look there
    So you can either modify the relative location address in the entire @font-face  At-rule to find your file
    Rather than that, you would best modify your @font-face to only look for the '/fonts/' folder
    src: url('/fonts/glyphicons-halflings-regular.eot'); //here we have removed the ..
    Now this will not work immediately, because express doesnt know where /fonts/ is, so in app.js, declare the font location like so:
    app.js
    ...
    app.use('/fonts/',express.static(path.join(__dirname, 'public/bootstrap/someotherlocation/fonts')));
    ...
    Now glyphicons should work in any location.
    Restart your server and navigate to your page... behold, a glyphtastic affair.

    This will now also work if you are downloading your bootstrap.css from a CDN
    See for yourself by changing your bootstrap.css link in layout.jade to the following
    layout.jade...
    link(rel='stylesheet', href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css")

    JOB DONE!
