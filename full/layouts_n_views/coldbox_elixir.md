# ColdBox Elixir

ColdBox Elixir provides a clean, fluent API for defining basic [Gulp](http://gulpjs.com/) tasks for your ColdBox applications.  This project was forked from the [Laravel Elixir](https://github.com/laravel/elixir) project, so many many thanks for all their hard work and ideas.  Please note that ColdBox does not ship with Elixir. It is an addon library based on Nodejs to help you with your asset pipeline.

## How it works
Elixir supports several common CSS, JavaScript pre-processors, and TestBox runner integrations. By leveraging your familiar `Gulpfile.js` configuration file, you can use method chaining and Elixir will allow you to fluently define your asset pipeline using the ColdBox conventions. 

It works on the premise of two location convetions for your static assets:

* `includes` - Where your css/js will be placed after the pipeline executes
* `resources/assets` - Where all your resource files exist.

For example:

```js
var elixir = require( 'coldbox-elixir' );
elixir( function( mix ) {
	// Look in the 'resources/sass' folder
    mix.sass( 'app.scss' )
    	// Look in the 'resourcess/css` folder
       .styles( 'modules.css' );
       
   // Elixir will then output all assets to ColdBox 'includes' folder by convention.
});
```

If you've ever been confused about how to get started with Gulp and asset compilation, you will love ColdBox Elixir. However, you are not required to use it while developing your application. You are free to use any asset pipeline tool you wish, or even none at all.

> **Tip** : ColdBox Elixir supports EcmaScript6, JSX syntax, LESS, SASS, babel, browserify, vueify, partialify and much more.  So take advantage!


# Installation & Setup

## Installing Node

Before triggering Elixir, you must first ensure that [Node.js](https://nodejs.org/en/) is installed on your machine.

```
node -v
```

## Installing Gulp

Next, you'll want to pull in Gulp as a global NPM package so you have it available for any ColdBox application.

```
npm install --g gulp
```

If you use a version control system, you may wish to run the npm [shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap) to lock your NPM requirements:

```
npm shrinkwrap
```
 
Once you have run this command, feel free to commit the `npm-shrinkwrap.json` into source control.

## Installing ColdBox Elixir

The only remaining step is to install Elixir! If you generate a ColdBox application using any of our [elixir templates](https://github.com/coldbox-templates/) then you will find a `package.json` file in the root of the application already. Think of this like your `box.json` file, except it defines Node dependencies instead of ColdFusion (CFML) dependencies.  A typical example can look like this:


```js
{
  "private": true,
  "devDependencies": {
    "gulp": "^3.9.1"
  },
  "dependencies": {
    "bootstrap-sass": "^3.3.6",
    "coldbox-elixir": "^1.1.0",
    "jquery": "^2.2.3"
  }
}
```

It defines ColdBox Elixir, Bootstrap and jQuery as your dependencies.  You may then install the dependencies it references by running:

```
npm install
```

This will install ColdBox Elixir, Bootstrap and jQuery into the `node_modules` folder in your root.  This folder has been already added to the `.gitignore` file as well, so no need to further ignore it.

> **Note** : If you are developing on a Windows system or you are running your VM on a Windows host system, you may need to run the `npm install` command with the `--no-bin-links` switch enabled: `npm install --no-bin-links`

<br>

> **Tip** : If you are integrating with Vue.js, please see our [Vue.js](https://github.com/ColdBox/elixir/wiki/Vue.js-Integration) section

# Running Elixir

Elixir is built on top of Gulp, so to run your Elixir tasks you only need to run the `gulp` command in your terminal, as you will already have a `Gulpfile.js` in your project root that will resemble the following:

```js
var elixir = require( 'coldbox-elixir' );

/*
 |--------------------------------------------------------------------------
 | Elixir Asset Management
 |--------------------------------------------------------------------------
 |
 | Elixir provides a clean, fluent API for defining some basic Gulp tasks
 | for your ColdBox application. By default, we are compiling the Sass
 | file for our application, as well as publishing vendor resources.
 |
 */

elixir( function( mix ){
	
} );
```


Please note that by adding the `--production` flag to the command will instruct Elixir to minify your CSS and JavaScript files:

```bash
// Run all tasks...
gulp

// Run all tasks and minify all CSS and JavaScript...
gulp --production
```

## Watching Assets For Changes

Since it is inconvenient to run the gulp command on your terminal after every change to your assets, you may use the `gulp watch` command. This command will continue running in your terminal and watch your assets for any changes. When changes occur, new files will automatically be compiled or tested:

```
gulp watch
```


## Further Documentation

ColdBox Elixir is fully documented here: https://github.com/ColdBox/elixir/wiki.  So please head on over there to get a deeper perspective of our asset pipeline manager.