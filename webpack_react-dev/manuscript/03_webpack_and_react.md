# Webpack and React

Combined with Webpack, React becomes a joy to work with. Even though you can use React with other build tools, Webpack is a good fit and quite straightforward to set up. In this chapter, we'll expand our configuration. After that, we have a good starting point for developing our application further.

T> Common editors (Sublime Text, Visual Studio Code, vim, emacs, Atom and such) have good support for React. Even IDEs, such as [WebStorm](https://www.jetbrains.com/webstorm/), support it up to an extent. [Nuclide](http://nuclide.io/), an Atom based IDE, has been developed with React in mind.

## What is React?

![React](images/react_header.png)

Facebook's [React](https://facebook.github.io/react/) has changed the way we think about front-end development. Also, thanks to [React Native](https://facebook.github.io/react-native/) the approach isn't limited just to web. Although simple to learn, React provides plenty of power.

React isn't a framework like Angular.js or Ember. Frameworks tend to provide a lot of solutions out of the box. With React you will have to assemble your application from separate libraries. Both approaches have their merits. Frameworks may be faster to pick up, but they can become harder to work with as you hit their boundaries. In a library based approach you have more flexibility, but also responsibility.

React introduced a concept known as virtual DOM to web developers. React maintains a DOM of its own unlike all the libraries and frameworks before it. As changes are made to virtual DOM, React will batch the changes to the actual DOM as it sees best.

T> Libraries, such as [Matt-Esch/virtual-dom](https://github.com/Matt-Esch/virtual-dom) or [paldepind/snabbdom](https://github.com/paldepind/snabbdom), focus entirely on Virtual DOM. If you are interested in the theory, check these out.

### JSX and Virtual DOM

React provides a [high level API](https://facebook.github.io/react/docs/top-level-api.html) for generating virtual DOM. Generating complex structures using the API becomes cumbersome fast. Thus people usually don't write it by hand. Instead, they use some intermediate format that is converted into it. Facebook's [JSX](https://facebook.github.io/jsx/) is one popular format.

JSX is a superset of JavaScript that allows you to mix XMLish syntax with JavaScript. Consider the example below:

```javascript
const Names = () => {
  const names = ['John', 'Jill', 'Jack'];

  return (
    <div>
      <h2>Names</h2>

      {/* This is a list of names */}
      <ul className="names">{
        names.map(name =>
          <li className="name">{name}</li>
        )
      }</ul>
    </div>
  );
};
```

If you haven't seen JSX before it will likely look strange. It isn't uncommon to experience "JSX shock" until you start to understand it. After that, it all makes sense.

Cory House goes into more detail [about the shock](https://medium.com/@housecor/react-s-jsx-the-other-side-of-the-coin-2ace7ab62b98). Briefly summarized, JSX gives us a level of validation we haven't encountered earlier. It takes a while to grasp, but once you get it, it's hard to go back.

T> Note that `render()` [must return a single node](https://facebook.github.io/react/tips/maximum-number-of-jsx-root-nodes.html). Returning multiple won't work!

T> [HyperScript](https://github.com/dominictarr/hyperscript) can be an interesting alternative to JSX. It provides a JavaScript based API and as such is a little closer to the metal. If you are interested, you can use the syntax with React through [hyperscript-helpers](https://www.npmjs.com/package/hyperscript-helpers).

### JSX vs. HTML

In JSX we are mixing something that looks a bit like HTML with JavaScript. Note how we treat attributes. Instead of using `class` as we would in vanilla HTML, we use `className`, which is the DOM equivalent. Even though JSX will feel a little weird to use at first, it will become second nature over time.

The developers of React have decoupled themselves from the limitations of the DOM. As a result, React is highly performant. This comes with a cost, though. The library isn't as small as you might expect. You can expect bundle sizes for small applications to be around 150-200k, React included. That is considerably less when gzipped over the wire, but it's still something.

T> Solutions such as [preact](https://developit.github.io/preact/) and [react-lite](https://github.com/Lucifier129/react-lite) allow you to reach far smaller bundle sizes while sacrificing some functionality. If you are size conscious, consider checking out these solutions.

T> The interesting side benefit of React's approach is that it doesn't depend on the DOM. In fact, React can use other targets, such as [mobile](https://facebook.github.io/react-native/), [canvas](https://github.com/Flipboard/react-canvas), or [terminal](https://github.com/Yomguithereal/react-blessed). The DOM just happens to be the most relevant one for web developers.

T> There is a semantic difference between React components, such as the one above, and React elements. In the example each of those JSX nodes would be converted into one. In short, components can have state whereas elements are simpler by nature. They are just pure objects. Dan Abramov goes into further detail in a [blog post](https://facebook.github.io/react/blog/2015/12/18/react-components-elements-and-instances.html) of his.

### Better with Friends

React isn't the smallest library out there. It does manage to solve fundamental problems, though. It is a pleasure to develop thanks to its relative simplicity and a powerful API. You will need to complement it with a set of tools, but you can pick these based on actual need. It's far from a "one size fits all" type of solution, which frameworks tend to be.

The approach used by React allowed Facebook to develop React Native on top of the same ideas. This time instead of the DOM, we are operating on mobile platform rendering. React Native provides abstraction over components and a layout system. It provides you the setup you already know from the web. This makes it a good gateway for web developers wanting to go mobile.

## Babel

![Babel](images/babel.png)

[Babel](https://babeljs.io/) has made a big impact on the community. It allows us to use features from the future of JavaScript. It will transform your futuristic code to a format browsers understand. You can even use it to develop your own language features. Babel's built-in JSX support will come in handy here.

Babel provides support for certain [experimental features](https://babeljs.io/docs/usage/experimental/) from ES7 beyond standard ES6. Some of these might make it to the core language while some might be dropped altogether. The language proposals have been categorized within stages:

* **Stage 0** - Strawman
* **Stage 1** - Proposal
* **Stage 2** - Draft
* **Stage 3** - Candidate
* **Stage 4** - Finished

I would be very careful with **stage 0** features. The problem is that if the feature changes or gets removed you will end up with broken code and will need to rewrite it. In smaller experimental projects it may be worth the risk, though.

T> You can [try out Babel online](https://babeljs.io/repl/) to see what kind of code it generates.

### Configuring `babel-loader`

You can use Babel with Webpack easily through [babel-loader](https://www.npmjs.com/package/babel-loader). It takes our ES6 module definition based code and turn it into ES5 bundles. Install *babel-loader* with

```bash
npm i babel-loader babel-core --save-dev
```

*babel-core* contains the core logic of Babel so we need to install that as well.

To make this work, we need to add a loader declaration for `babel-loader` to the *loaders* section of the configuration. It matches against both `.js` and `.jsx` using a regular expression (`/\.jsx?$/`).

To keep everything performant we should restrict the loader to operate within *./app* directory. This way it won't traverse `node_modules`. An alternative would be to set up an `exclude` rule against `node_modules` explicitly. I find it more useful to `include` instead as that's more explicit. You never know what files might be in the structure after all.

Here's the relevant configuration we need to make Babel work:

**webpack.config.js**

```javascript
...

const common = {
  entry: {
    app: PATHS.app
  },
leanpub-start-insert
  // Add resolve.extensions.
  // '' is needed to allow imports without an extension.
  // Note the .'s before extensions as it will fail to match without!!!
  resolve: {
    extensions: ['', '.js', '.jsx']
  },
leanpub-end-insert
  output: {
    path: PATHS.build,
    filename: 'bundle.js'
  },
  module: {
    loaders: [
      {
        test: /\.css$/,
        loaders: ['style', 'css'],
        include: PATHS.app
      }
leanpub-start-insert
      },
      // Set up jsx. This accepts js too thanks to RegExp
      {
        test: /\.jsx?$/,
        // Enable caching for improved performance during development
        // It uses default OS directory by default. If you need something
        // more custom, pass a path to it. I.e., babel?cacheDirectory=<path>
        loaders: ['babel?cacheDirectory'],
        // Parse only app files! Without this it will go through entire project.
        // In addition to being slow, that will most likely result in an error.
        include: PATHS.app
      }
leanpub-end-insert
    ]
  }
};

...
```

Note that `resolve.extensions` setting will allow you to refer to JSX files without an extension. I'll be using the extension for clarity, but you can omit it if you want.

T> As `resolve.extensions` gets evaluated from left to right, we can use it to control which code gets loaded for given configuration. For instance, you could have `.web.js` to define web specific parts and then have something like `['', '.web.js', '.js', '.jsx']`. If a "web" version of the file is found, Webpack would use that instead of the default.

### Setting Up *.babelrc*

Also, we are going to need a [.babelrc](https://babeljs.io/docs/usage/babelrc/). You could pass Babel settings through Webpack (i.e., `babel?presets[]=react,presets[]=es2015`), but then it would be just for Webpack only. That's why we are going to push our Babel settings to this specific dotfile. The same idea applies for other tools, such as ESLint.

Babel 6 relies on *plugins*. There are two types of plugins: syntax and transform. The former allow Babel to parse additional syntax whereas latter apply transformations. This way the code that is using future syntax can get transformed back to JavaScript older environments can understand.

To make it easier to consume plugins, Babel supports the concept of *presets*. Each preset comes with a set of plugins so you don't have to wire them up separately. In this case we'll be relying on ES2015 and React presets:

```bash
npm i babel-preset-es2015 babel-preset-react --save-dev
```

T> Instead of typing it all out, we could use brace expansion. Example: `npm i babel-preset-{es2015,react} -D`. `-D` equals `--save-dev` as you might remember.

In addition, we'll be enabling a couple of custom features to make the project more convenient to develop:

* [Property initializers](https://github.com/jeffmo/es-class-static-properties-and-fields) - Example: `renderNote = (note) => {`. This binds the `renderNote` method to instances automatically. The feature makes more sense as we get to use it.
* [Decorators](https://github.com/wycats/javascript-decorators) - Example: `@DragDropContext(HTML5Backend)`. These annotations allow us to attach functionality to classes and their methods.
* [Object rest/spread](https://github.com/sebmarkbage/ecmascript-rest-spread) - Example: ``const {a, b, ...props} = this.props`. This syntax allows us to easily extract specific properties from an object.

In order to make it easier to set up the features, I created [a specific preset](https://github.com/survivejs/babel-preset-survivejs-kanban) containing them. It also contains [babel-plugin-transform-object-assign](https://www.npmjs.com/package/babel-plugin-transform-object-assign) and [babel-plugin-array-includes](https://www.npmjs.com/package/babel-plugin-array-includes) plugins. The former allows us to use `Object.assign` while the latter provides `Array.includes` without having to worry about shimming these for older environments.

T> An alternative way to deal with `Object.assign` would be to consume the functionality through a ponyfill, such as [object-assign](https://www.npmjs.com/package/object-assign), or use a polyfill like [object.assign](https://www.npmjs.com/package/object.assign). Ponyfills don't override native methods and can be considered a safer option. Polyfills do exactly that so use them with care.

A preset is simply a npm module exporting Babel configuration. Maintaining presets like this can be useful especially if you want to share the same set of functionality across multiple projects. Get the preset installed:

```bash
npm i babel-preset-survivejs-kanban --save-dev
```

Next we need to set up a *.babelrc* file to make this all work:

**.babelrc**

```json
{
  "presets": [
    "es2015",
    "react",
    "survivejs-kanban"
  ]
}
```

Babel provides stage specific presets. It is clearer to rely directly on any custom features you might want to use. This documents your project well and keeps it maintainable. You could even drop *babel-preset-es2015* and enable the features you need one by one. There are other possible [.babelrc options](https://babeljs.io/docs/usage/options/) beyond the ones covered here.

T> If you don't like to maintain a *.babelrc* file, another alternative is to write the configuration below `babel` field at *package.json*. Babel will pick it up from there.

## Defining Your Own Babel Presets

A Babel preset is simply a Node.js package that imports other presets and plugins. The idea is that the preset will pull the dependencies it needs and then expose them through standard Node.js interface. For example, we could define a preset like this:

**index.js**

```javascript
module.exports = {
  plugins: [
    require('babel-plugin-syntax-class-properties'),
    require('babel-plugin-syntax-decorators'),
    require('babel-plugin-syntax-object-rest-spread'),

    // You can pass parameters using an array syntax
    [
      require('babel-plugin-transform-regenerator'),
      {
        async: false,
        asyncGenerators: false
      }
    ]
  ]
};
```

In this case we're pulling specific plugins to our preset. You could pull the plugins directly to your *.babelrc* through the `plugins` field. That can be handy if you happen to need just one or two for some reason. When the configuration begins to grow, consider extracting it to a preset. You could also define `presets` field here if you want to bring other presets to your project through your preset.

Assuming we named our package as *babel-preset-survivejs-kanban*, we could then install it to our project as above and connect it with Babel configuration. Note the *babel-preset* prefix. The great advantage of developing a package like this is that it allows us to maintain shared presets across multiple, similar projects.

The *Authoring Packages* chapter goes into greater detail when it comes to npm and dealing with packages. To make it easier for other people to find your preset, consider including `babel-preset` to your package keywords.

### Using Babel for Webpack Configuration

It is possible to use Babel transpiled features in your Webpack configuration file. Simply rename *webpack.config.js* to *webpack.config.babel.js* and Webpack will pick it up provided Babel has been set up in your project. It will respect the contents of *.babelrc*.

For this to work, you will need to have [babel-register](https://www.npmjs.com/package/babel-register) installed to your project. Webpack relies internally on [interpret](https://www.npmjs.com/package/interpret) to make this work.

## Alternative Loader Declarations

Webpack's loader declaration is somewhat flexible. To give you a better idea, consider the following examples. The first one shows how to pass parameters to a loader through a query string:

```javascript
{
  test: /\.jsx?$/,
  loaders: [
    'babel?cacheDirectory,presets[]=react,presets[]=es2015,presets[]=survivejs-kanban'
  ],
  include: PATHS.app
}
```

Given passing a query string like this isn't particularly readable, another way is to use the combination of `loader` and `query` fields:

```javascript
{
  test: /\.jsx?$/,
  loader: 'babel',
  query: {
    cacheDirectory: true,
    presets: ['react', 'es2015', 'survivejs-kanban']
  },
  include: PATHS.app
}
```

This approach becomes problematic with multiple loaders since it's limited just to one loader at a time. If you want to use this format with multiple, you need separate declarations.

It's a good idea to keep in mind that Webpack `loaders` are always evaluated from right to left and from bottom to top (separate definitions). The following two declarations are equal based on this rule:

```javascript
{
    test: /\.css$/,
    loaders: ['style', 'css'],
},
```

```javascript
{
    test: /\.css$/,
    loaders: ['style'],
},
{
    test: /\.css$/,
    loaders: ['css'],
},
```

The `loaders` of the latter definition could be rewritten in the query format discussed above after performing a split like this.

Another way to deal with query parameters would be to rely on Node.js [querystring](https://nodejs.org/api/querystring.html) module and stringify structures through it so they can be passed through a `loaders` definition.

## Developing the First React View

First, we should define the `App`. This will be the core of our application. It represents the high level view of our app and works as an entry point. Later on it will orchestrate the entire execution. We can get started by using React's class based component definition syntax:

**app/components/App.jsx**

```javascript
import React from 'react';
import Note from './Note.jsx';

export default class App extends React.Component {
  render() {
    return <Note />;
  }
}
```

T> You can import portions from `react` using the syntax `import React, {Component} from 'react';`. Then you can do `class App extends Component`. It is important that you import `React` as well because that JSX will get converted to `React.createElement` calls. I prefer `import React from 'react'` simply because it's easier to grep for `React.Component` than `Component` given it's more unique.

T> It may be worth your while to install [React Developer Tools](https://github.com/facebook/react-devtools) extension to your browser. Currently, Chrome and Firefox are supported. This will make it easier to understand what's going on while developing. Note that the developer tools won't work if you are using the iframe mode (`/webpack-dev-server/`) of *webpack-dev-server*!

### Setting Up `Note`

We also need to define the `Note` component. In this case, we will just want to show some text like `Learn Webpack`. `Hello world` would work if you are into clichés. Given the component is so simple, we can use React's function based component definition:

**app/components/Note.jsx**

```javascript
import React from 'react';

export default () => <div>Learn Webpack</div>;
```

Even though we aren't referring to React directly through code here, it is good to remember that the JSX will get transformed into calls going through it. Hence if you remove the `import` statement, the code will break. Babel plugin known as [babel-plugin-react-require](https://www.npmjs.com/package/babel-plugin-react-require) is able to generate the `imports` for you automatically if you prefer to avoid the imports.

T> Note that we're using the *jsx* extension here. It helps us to tell modules using JSX syntax apart from regular ones. It is not absolutely necessary, but it is a good convention to have.

T> Just returning `Learn Webpack` from the component won't work. You will have to wrap it like this. Sometimes it can be convenient just to return `null` in case you don't want to return anything.

### Rendering Through *index.jsx*

To make everything work, we'll need to adjust our `index.js` to render the component. Note that I've renamed it as `index.jsx` given we have JSX content there. We will render the content through a package known as *react-dom*. Given we're dealing with the browser here, it's the right choice:

**app/index.jsx**

```javascript
import './main.css';

import React from 'react';
import ReactDOM from 'react-dom';
import App from './components/App.jsx';

ReactDOM.render(<App />, document.getElementById('app'));
```

T> If you aren't using *npm-install-webpack-plugin*, remember to install *react* and *react-dom* to your project through `npm i react react-dom -S`.

If you are running the development server, you should see something familiar at **localhost:8080**:

![Hello React](images/react_01.png)

In case you try to modify your React components, you can see Webpack forces a full refresh. This is something we are going to fix next by enabling hot loading.

Before moving on, this is a good time to get rid of the old `component.js` file in the `app` root directory. We won't be needing that anymore.

T> If you aren't seeing the correct result, make sure your *index.html* has `<div id="app"></div>` within its `body`. Alternatively you can create the element into which to render through the DOM API itself. I prefer to handle this on template level, though.

W> Avoid rendering directly to `document.body`. This can cause strange problems when relying on it and React will give you a warning about it.

## Activating Hot Loading for Development

Note that every time you perform a modification, the browser updates with a flash. That's unfortunate because this means our application loses state. It doesn't matter yet, but as we keep on expanding the application this will become painful. It is annoying to manipulate the user interface back to the state in which it was to test something.

We have already implemented the development server side setup for this. The problem is that we are missing a part that allows the client portion to catch the changes and patch the code. Some setup is needed in order to add it to our project.

[babel-plugin-react-transform](https://github.com/gaearon/babel-plugin-react-transform) allows us to instrument React components in various ways. Hot loading is one of these. It can be enabled through [react-transform-hmr](https://github.com/gaearon/react-transform-hmr).

*react-transform-hmr* will swap React components one by one as they change without forcing a full refresh. Given it just replaces methods, it won't catch every possible change. This includes changes made to class constructors. There will be times when you will need to force a refresh, but it will work most of the time.

A Babel preset known as [babel-preset-react-hmre](https://www.npmjs.com/package/babel-preset-react-hmre) will keep our setup simple. It comes with reasonable defaults and cuts down the amount of configuration you need to maintain. Install it through:

```bash
npm i babel-preset-react-hmre --save-dev
```

We also need to make Babel aware of HMR. First, we should pass the target environment to Babel through our Webpack configuration. This allows us to control environment specific functionality through *.babelrc*. In this case we want to enable HMR just for development. If you wanted to enable some specific plugins for a production build, you would use the same idea.

An easy way to control *.babelrc* is to set `BABEL_ENV` environment variable as npm lifecycle event. This gives us a predictable mapping between *package.json* and *.babelrc*:

**webpack.config.js**

```javascript
...

leanpub-start-insert
process.env.BABEL_ENV = TARGET;
leanpub-end-insert

const common = {
  ...
};

...
```

In addition we need to expand our Babel configuration to include the plugin we need during development. This is where that `BABEL_ENV` comes in. Babel determines the value of `env` like this:

1. Use the value of `BABEL_ENV` if set.
2. Use the value of `NODE_ENV` if set.
3. Default to `development`.

To connect `BABEL_ENV='start'` with Babel, configure as follows:

**.babelrc**

```json
{
  "presets": [
    "es2015",
    "react",
    "survivejs-kanban"
leanpub-start-delete
  ]
leanpub-end-delete
leanpub-start-insert
  ],
  "env": {
    "start": {
      "presets": [
        "react-hmre"
      ]
    }
  }
leanpub-end-insert
}
```

Try executing `npm start` again and modifying the component. Note what doesn't happen this time. There's no flash! It might take a while to sink in, but in practice, this is a powerful feature. Small things like this add up and make you more productive.

W> Note that sourcemaps won't get updated in [Chrome](https://code.google.com/p/chromium/issues/detail?id=492902) and Firefox due to browser level bugs! This may change in the future as the browsers get patched, though.

## React Component Styles

Outside of ES6 classes, React allows you to construct components using `React.createClass()` and functions. `React.createClass()` was the original way to create components and it is still in use. The approaches aren't equivalent by default.

When you use `React.createClass` it is possible to inject functionality using mixins. Mixins aren't available in ES6 by default. Yet, you can use a helper, such as [react-mixin](https://github.com/brigand/react-mixin), to provide some capabilities. In later chapters we will go through various alternative approaches. They allow you to reach roughly equivalent results as you can achieve with mixins. Often a decorator is all you need.

Also, ES6 class based components won't bind their methods to the `this` context by default. This is the reason why it can be a good practice to bind the context in the component constructor. Another way to solve the problem is to use property initializers. We'll be using that approach as it cuts down the amount of code nicely and makes it easier to follow what's going on.

The class based approach decreases the amount of concepts you have to worry about. `constructor` helps to keep things simpler than in the `React.createClass` based approach. There you need to define separate methods to achieve the same result.

## Conclusion

You should understand how to set up React with Webpack now. Hot loading is one of those features that sets Webpack apart. Now that we have a good development environment, we can focus on React development. In the next chapter, you will see how to implement a little note-taking application. That will be improved in the subsequent chapters into a full blown Kanban board.
