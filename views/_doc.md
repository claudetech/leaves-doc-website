## Main functionalities

* HTML templating via [Jade](http://jade-lang.com/) or [EJS](https://github.com/RandomEtc/ejs-locals)
* CSS templating via [Stylus](http://learnboost.github.io/stylus/) or [less](http://lesscss.org/)
* [CoffeeScript](http://coffeescript.org/) compilation
* Project watch and livereload
* Scripts and stylesheets globbing with `**/*.js` like syntax
* Compile error displayed in browser
* Single command deploy to Heroku and GitHub pages, and FTP servers
* Misc: lorem-ipsum generator, easy CDN usage, shell completion, single command upgrade

## Installation
<a id="installation"></a>

To install leaves, run

```sh
$ npm install -g leaves
```

If `npm install` fails, try to use `sudo`.

Then, to install some extra dependencies automatically
and setup the shell completion, run

```sh
$ leaves setup
```

## Getting started
<a id="gettingstarted"></a>

### Start a new project

```sh
$ leaves new project_name
$ cd project_name
$ leaves
```

and start coding!

### Get a project from Git

```sh
$ leaves get https://github.com/me/my_leaves_project.git
$ cd my_leaves_project
$ leaves
```

and start coding!

## Project structure

The app has the following structure:

```
.
├── assets
│   ├── css
│   │   └── main.styl
│   ├── favicon.ico
│   ├── img
│   └── js
│       └── app.coffee
├── bower.json
├── package.json
└── views
    ├── index.jade
    └── layout.jade
```

when running leaves, `views` and `assets` are watched, 
compiled or copied on change and your browser is reloaded.

## Usage
<a id="usage"></a>

For every command, you can pass the `--save-options` flag
to remember the options you just passed for future usage.
`--save-options` defaults to `--save-options=project`, which writes
the `.leavesrc` in your project, you can use `--save-options=global`
if you want this to be global to all your project.

The available commands are listed below.

### Setup
<a id="setup"></a>

You can run the initial setup to have shell completion by running:

```sh
$ leaves setup
```

Only zsh is supported for now, but PR are welcome.

### New
<a id="newproject"></a>

To create a new project, just run:

```sh
$ leaves new PROJECT_NAME [--html=ejs] [--css=less]
```

you can then `cd` in `PROJECT_NAME`.

#### CSS engines

The default engine is [Stylus](http://learnboost.github.io/stylus/).
Add `--css=less` to use [less css](http://lesscss.org/)

#### HTML template engines

The default engine is [Jade](http://jade-lang.com/).
Add `--html=ejs` to use [EJS templates (with layouts)](https://github.com/RandomEtc/ejs-locals)

### Build
<a id="build"></a>

Just run

```sh
$ leaves build [--development]
```

in your project directory.
If you want a development build, without
concatenation and minification, pass the `development` flag.

### Watch
<a id="watch"></a>

To build and watch your files, run

```sh
$ leaves [watch]
```

(which will run `leaves watch`) in your project directory.

### Upgrade
<a id="upgrade"></a>

You can upgrade your leaves installation by running

```sh
$ leaves upgrade
```

This will run `npm update -g leaves` for you and use `sudo` only if needed.

In your working project, you can also run

```sh
$ leaves upgrade -o
```

to get the latest `Gruntfile.coffee` and update your `package.json`
dependencies. Your `Gruntfile.coffee` will be overwritten so be
careful when you use it if you have some changes.
This will also run `npm install` for you to install the latest
dependencies.

### Publish
<a id="publish"></a>

You can publish your website to [Heroku][heroku] or to [GitHub Pages][github-pages].

To publish to Heroku, you only need to have a valid account.
To publish to Github, you need to have a remote pointing to github.com in your project.

```sh
$ leaves publish [--skip-build] [--skip-commit] [--skip-install] [--use-dev] [-p PROVIDER]
```

`PROVIDER` parameter can be `github` , `heroku` or `ftp`. The default is `heroku`.
If you want to use the development build to deploy, pass `--use-dev`.

Your website will then be accessible at http://APP_NAME.herokuapp.com when publishing with Heroku and http://USERNAME.github.io/REPO_NAME. with GitHub Pages.
For heroku, `APP_NAME` will default to the appName in `.leavesrc`.

For FTP, you will be asked for your credentials, which will be
stored in `.leavesrc.local`.

### Install
<a id="install"></a>

Leaves can be used as a wrapper around [bower][bower] and [npm][npm].

```sh
$ leaves install [PACKAGES [-p PROVIDER] [--no-save]]
```

If no package is given, leaves will run `npm install` and `bower install`.

If packages are given, `PROVIDER` can be either `bower` or `npm`. If provider is not given, it will default to `bower`.

New packages are installed using `--save` by default. You can disable this
behavior by passing `--no-save`.

### Get
<a id="get"></a>

Fetch and prepare project for development.

```sh
$ leaves get GIT_REPOSITORY [-p PROTOCOL]
```

`GIT_REPOSITORY` can be anyting that would work with `git clone`,
or for GitHub repositories, the short syntax `user/repo` can be used.
`PROTOCOL` is only relevant when using the short syntax, and can be
`https` (default) or `ssh`.

## Functionalities
<a id="functionalities"></a>

### Easy lorem ipsum generation
<a id="lorem-ipsum"></a>

The function `lorem` is available in all templates.
Just call

```jade
= lorem(10)
```

and you will get 10 words of lorem ipsum.
For other options, see the actual [package documentation][node-lorem-ipsum]

### `script` and `link` globbing
<a id="globbing"></a>

To avoid having to insert all scripts in your layout,
you can use glob syntax.

```jade
html
  head
    script(src="js/**/*.js" glob)
    link(rel="stylesheet" href="css/**/*.css" glob)
```

will become

```html
<html>
<head>
  <script src="js/app.js">
  <script src="js/other.js">
  <script src="css/main.css">
  <script src="css/other.css">
</head>
<body>
</body>
</html>
```

for development build and

```html
<html>
<head>
  <script src="js/application.min.js">
  <script src="css/application.min.css">
</head>
<body>
</body>
</html>
```

for production build, where `js/application.min.js` and
`css/application.min.css` will be concatenated and
minified versions of the globbed files.

#### Files order

If alphabetical order does not fit your need, you can
use glob on single files, it will be inserted only once in the output.

```jade
html
  head
    script(src="js/this-one-is-first.js" glob)
    script(src="js/**/*.js" glob)
    link(rel="stylesheet" href="css/**/*.css" glob)
```

The files will always be concatenated in order of appearance.

Check out [the documentation][node-glob-html] for more details.

### CDN for production
<a id="cdn"></a>

For library such as jQuery, it is common to use a CDN
in production, but it can be better for development to
used a local and non minified version of the library.
You can achieve this easily with the following syntax.

```jade
html
  head
    script(src="compnents/jquery/dist/jquery.js" cdn="//code.jquery.com/jquery-2.1.1.min.js")
    link(rel="stylesheet" href="components/bootstrap/dist/css/bootstrap.css" cdn="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css")
```

Note that `cdn` attribute is not compatible with `glob` and will
result in a compile error.

Check out [the documentation][node-cdnify] for more details.

[generator-static-website]: https://github.com/claudetech/generator-static-website
[github-pages]: https://pages.github.com/
[heroku]: https://www.heroku.com/
[bower]: http://bower.io/
[npm]: https://www.npmjs.org/
[node-lorem-ipsum]: https://github.com/knicklabs/lorem-ipsum.js
[node-cdnify]: https://github.com/claudetech/node-cdnify
[node-glob-html]: https://github.com/claudetech/node-glob-html