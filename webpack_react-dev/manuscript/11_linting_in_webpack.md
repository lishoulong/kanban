# Linting in Webpack

Nothing is easier than making mistakes when coding in JavaScript. Linting is one of those techniques that can help you to make less mistakes. You can spot issues before they become actual problems.

Better yet, modern editors and IDEs offer strong support for popular tools. This means you can spot possible issues as you are developing. Despite this, it is a good idea to set them up with Webpack. That allows you to cancel a production build that might not be up to your standards for example.

## Brief History of Linting in JavaScript

The linter that started it it all for JavaScript is Douglas Crockford's [JSLint](http://www.jslint.com/). It is opinionated like the man himself. The next step in evolution was [JSHint](http://jshint.com/). It took the opinionated edge out of JSLint and allowed for more customization. [ESLint](http://eslint.org/) is the newest tool in vogue.

ESLint goes to the next level as it allows you to implement custom rules, parsers, and reporters. ESLint works with Babel and JSX syntax making it ideal for React projects. The project rules have been documented well and you have full control over their severity. These features alone make it a powerful tool.

Besides linting for issues, it can be useful to manage the code style on some level. Nothing is more annoying than having to work with source code that has mixed tabs and spaces. Stylistically consistent code reads better and is easier to work with.

[JSCS](http://jscs.info/) makes it possible to define a style guide for JavaScript code. It is easy to integrate into your project through Webpack, although ESLint implements a large part of its functionality.

## Webpack and JSHint

Interestingly, no JSLint loader seems to exist for Webpack yet. Fortunately, there's one for JSHint. You could set it up on a legacy project easily. Install [jshint-loader](https://www.npmjs.com/package/jshint-loader) to your project first:

```bash
npm i jshint jshint-loader --save-dev
```

In addition, you will need a little bit of configuration:

```javascript
var common = {
  ...
  module: {
    preLoaders: [
      {
        test: /\.jsx?$/,
        loaders: ['jshint'],
        // define an include so we check just the files we need
        include: PATHS.app
      }
    ]
  },
};
```

`preLoaders` section of the configuration gets executed before `loaders`. If linting fails, you'll know about it first. There's a third section, `postLoaders`, that gets executed after `loaders`. You could include code coverage checking there during testing, for instance.

JSHint will look into specific rules to apply from `.jshintrc`. You can also define custom settings within a `jshint` object at your Webpack configuration. Exact configuration options have been covered at [the JSHint documentation](http://jshint.com/docs/) in detail. `.jshintrc` could look like this:

**.jshintrc**

```json
{
  "browser": true,
  "camelcase": false,
  "esnext": true,
  "indent": 2,
  "latedef": false,
  "newcap": true,
  "quotmark": "double"
}
```

This tells JSHint we're operating within browser environment, don't care about linting for camelcase naming, want to use double quotes everywhere and so on.

If you try running JSHint on our project, you will get a lot of output. It's not the ideal solution for React projects. ESLint will be more useful so we'll be setting it up next for better insights. Remember to remove JSHint configuration before proceeding further.

## Setting Up ESLint

![ESLint](images/eslint.png)

[ESLint](http://eslint.org/) is a recent linting solution for JavaScript. It builds on top of ideas presented by JSLint and JSHint. More importantly it allows you to develop custom rules. As a result, a nice set of rules have been developed for React in the form of [eslint-plugin-react](https://www.npmjs.com/package/eslint-plugin-react).

T> Since *v1.4.0* ESLint supports a feature known as [autofixing](http://eslint.org/blog/2015/09/eslint-v1.4.0-released/). It allows you to perform certain rule fixes automatically. To activate it, pass the flag `--fix` to the tool.

### Connecting ESlint with *package.json*

In order to integrate ESLint with our project, we'll need to do a couple of little tweaks. First we'll need to execute

```bash
npm i eslint eslint-plugin-react --save-dev
```

This will add ESLint and the plugin we want to use as our project development dependencies. Next, we'll need to do some configuration:

**package.json**

```json
"scripts": {
  ...
  "lint": "eslint . --ext .js --ext .jsx"
}
...
```

This will trigger ESLint against all JS and JSX files of our project. That will lint a bit too much. Set up *.eslintignore* to the project root like this to skip *build/*:

**.eslintignore**

```bash
build/
```

T> ESLint supports custom formatters through `--format` parameter. [eslint-friendly-formatter](https://www.npmjs.com/package/eslint-friendly-formatter) is an example of a formatter that provides terminal friendly output. This way you can jump conveniently straight to the warnings and errors from there.

### Connecting ESLint with Babel

Next, we need to activate ES6 parsing, a couple React specific rules and set up a few of our own. You can adjust these to your liking. For details see the official [ESLint rules documentation](http://eslint.org/docs/rules/). Note that we are extending the recommended set of rules through the `extends` field:

**.eslintrc**

```json
{
  "extends": "eslint:recommended",
  "parserOptions": {
    "ecmaVersion": 6,
    "ecmaFeatures": {
      "jsx": true
    },
    "sourceType": "module"
  },
  "env": {
    "browser": true,
    "node": true
  },
  "plugins": [
    "react"
  ],
  "rules": {
    "no-console": 0,
    "new-cap": 0,
    "strict": 0,
    "no-underscore-dangle": 0,
    "no-use-before-define": 0,
    "eol-last": 0,
    "quotes": [2, "single"],
    "jsx-quotes": 1,
    "react/jsx-no-undef": 1,
    "react/jsx-uses-react": 1,
    "react/jsx-uses-vars": 1
  }
}
```

T> ESLint supports ES6 features through configuration. You will have to specify the features to use through the [ecmaFeatures](http://eslint.org/docs/user-guide/configuring.html#specifying-language-options) property.

T> In case you want to lint against custom language features, use [babel-eslint](https://www.npmjs.com/package/babel-eslint). After installing, you can make ESLint aware of it by setting `"parser": "babel-eslint"`. It should be safe to drop `parserOptions` section of *.babelrc* assuming custom parser is set.

The severity of an individual rule is defined by a number as follows:

* 0 - The rule has been disabled.
* 1 - The rule will emit a warning.
* 2 - The rule will emit an error.

Some rules, such as `quotes`, accept an array instead. This allows you to pass extra parameters to them. Refer to the rule's documentation for specifics.

The `react/` rules listed above are just a small subset of all available rules. Pick rules from [eslint-plugin-react](https://www.npmjs.com/package/eslint-plugin-react) as needed.

T> Note that you can write ESLint configuration directly to *package.json*. Set up a `eslintConfig` field, and write your declarations below it.

T> It is possible to generate a sample `.eslintrc` using `eslint --init` (or `node_modules/.bin/eslint --init` for local install). This can be useful on new projects.

### Dealing with `ELIFECYCLE` Error

In case the linting process fails, `npm` will give you a nasty looking `ELIFECYCLE` error. A good way to achieve a tidier output is to invoke `npm run lint --silent`. That will hide the `ELIFECYCLE` bit. You can define an alias for this purpose. In Unix you would do `alias run='npm run --silent'` and then `run <script>`.

Alternatively, you could pipe output to `true` like this:

**package.json**

```json
"scripts": {
  ...
  "lint": "eslint . --ext .js --ext .jsx || true"
}
...
```

The problem with this approach is that if you invoke `lint` through some other command, it will pass even if there are failures. If you have another script that does something like `npm run lint && npm run build`, it will build regardless of the output of the first command!

### Connecting ESLint with Webpack

We can make Webpack emit ESLint messages for us by using [eslint-loader](https://www.npmjs.com/package/eslint-loader). As the first step execute

```bash
npm i eslint-loader --save-dev
```

W> Note that `eslint-loader` will use a globally installed version of ESLint unless you have one included with the project itself! Make sure you have ESLint as a development dependency to avoid strange behavior.

Next, we need to tweak our development configuration to include it. Add the following section to it:

**webpack.config.js**

```javascript
var common = {
  ...
  module: {
    preLoaders: [
      {
        test: /\.jsx?$/,
        loaders: ['eslint'],
        include: PATHS.app
      }
    ]
  },
};
```

We are including the configuration to `common` so that linting always gets performed. This way you can make sure your production build passes your rules while making sure you benefit from linting during development.

If you execute `npm start` now and break some linting rule while developing, you should see that in the terminal output. The same should happen when you build the project.

## Customizing ESLint

Even though you can get very far with vanilla ESLint, there are several techniques you should be aware of. For instance, sometimes you might want to skip some particular rules per file. You might even want to implement rules of your own. We'll cover these cases briefly next.

### Skipping ESLint Rules

Sometimes, you'll want to skip certain rules per file or per line. This can be useful when you happen to have some exceptional case in your code where some particular rule doesn't make sense. As usual, exception confirms the rule. Consider the following examples:

```javascript
// everything
/* eslint-disable */
...
/* eslint-enable */
```

```javascript
// specific rule
/* eslint-disable no-unused-vars */
...
/* eslint-enable no-unused-vars */
```

```javascript
// tweaking a rule
/* eslint no-comma-dangle:1 */
```

```javascript
// disable rule per line
alert('foo'); // eslint-disable-line no-alert
```

Note that the rule specific examples assume you have the rules in your configuration in the first place! You cannot specify new rules here. Instead, you can modify the behavior of existing rules.

### Setting Environment

Sometimes, you may want to run ESLint in a specific environment, such as Node.js or Mocha. These environments have certain conventions of their own. For instance, Mocha relies on custom keywords (e.g., `describe`, `it`) and it's good if the linter doesn't choke on those.

ESLint provides two ways to deal with this: local and global. If you want to set it per file, you can use a declaration at the beginning of a file:

```javascript
/*eslint-env node, mocha */
```

Global configuration is possible as well. In this case, you can use `env` key like this:

**.eslintrc**

```json
{
  "env": {
    "browser": true,
    "node": true,
    "mocha": true
  },
  ...
}
```

### Writing Your Own Rules

ESLint rules rely on Abstract Syntax Tree (AST) definition of JavaScript. It is a data structure that describes JavaScript code after it has been lexically analyzed. There are tools, such as [recast](https://github.com/benjamn/recast), that allow you to perform transformations on JavaScript code by using AST transformations. The idea is that you match some structure, then transform it somehow and convert AST back to JavaScript.

To get a better idea of how AST works and what it looks like, you can check [Esprima online JavaScript AST visualization](http://esprima.org/demo/parse.html) or [AST Explorer by Felix Kling](http://astexplorer.net/). Alternatively you can install `recast` and examine the output it gives. That is the structure we'll be working with for ESLint rules.

T> [Codemod](https://github.com/facebook/codemod) allows you to perform large scale changes to your codebase through AST based transformations.

In ESLint's case we just want to check the structure and report in case something is wrong. Getting a simple rule done is surprisingly simple:

1. Set up a new project named `eslint-plugin-custom`. You can replace `custom` with whatever you want. ESLint follows this naming convention.
2. Execute `npm init -y` to create a dummy *package.json*
3. Set up `index.js` in the project root with content like this:

**eslint-plugin-custom/index.js**

```javascript
module.exports = {
  rules: {
    demo: function(context) {
      return {
        Identifier: function(node) {
          context.report(node, 'This is unexpected!');
        }
      };
    }
  }
};
```

In this case, we just report for every identifier found. In practice, you'll likely want to do something more complex than this, but this is a good starting point.

Next, you need to execute `npm link` within `eslint-plugin-custom`. This will make your plugin visible within your system. `npm link` allows you to easily consume a development version of a library you are developing. To reverse the link you can execute `npm unlink` when you feel like it.

T> If you want to do something serious, you should point to your plugin through *package.json*.

We need to alter our project configuration to make it find the plugin and the rule within.

**.eslintrc**

```json
{
  ...
  "plugins": [
leanpub-start-delete
    "react",
leanpub-end-delete
leanpub-start-insert
    "react",
    "custom"
leanpub-end-insert
  ],
  "rules": {
leanpub-start-insert
    "custom/demo": 1,
leanpub-end-insert
    ...
  }
}
```

If you invoke ESLint now, you should see a bunch of warnings. Mission accomplished!

Of course the rule doesn't do anything useful yet. To move forward, I recommend checking out the official documentation about [plugins](http://eslint.org/docs/developer-guide/working-with-plugins.html) and [rules](http://eslint.org/docs/developer-guide/working-with-rules.html).

You can also check out some of the existing rules and plugins for inspiration to see how they achieve certain things. ESLint allows you to [extend these rulesets](http://eslint.org/docs/user-guide/configuring.html#extending-configuration-files) through `extends` property. It accepts either a path to it (`"extends": "./node_modules/coding-standard/.eslintrc"`) or an array of paths. The entries are applied in the given order and later ones override the former.

### ESLint Resources

Besides the official documentation available at [eslint.org](http://eslint.org/), you should check out the following blog posts:

* [Lint Like It's 2015](https://medium.com/@dan_abramov/lint-like-it-s-2015-6987d44c5b48) - This post by Dan Abramov shows how to get ESLint to work well with Sublime Text.
* [Detect Problems in JavaScript Automatically with ESLint](http://davidwalsh.name/eslint) - A good tutorial on the topic.
* [Understanding the Real Advantages of Using ESLint](http://rangle.io/blog/understanding-the-real-advantages-of-using-eslint/) - Evan Schultz's post digs into details.
* [eslint-plugin-smells](https://github.com/elijahmanor/eslint-plugin-smells) - This plugin by Elijah Manor allows you to lint against various JavaScript smells. Recommended.

If you just want some starting point, you can pick one of [eslint-config- packages](https://www.npmjs.com/search?q=eslint-config) or go with the [standard](https://www.npmjs.com/package/standard) style. By the looks of it, `standard` has [some issues with JSX](https://github.com/feross/standard/issues/138) so be careful with that.

## Linting CSS

[stylelint](http://stylelint.io/) allows us to lint CSS. It can be used with Webpack through [postcss-loader](https://www.npmjs.com/package/postcss-loader).

```bash
npm i stylelint postcss-loader --save-dev
```

Next, we'll need to integrate it with our configuration:

**webpack.config.js**

```javascript
...
var stylelint = require('stylelint');

...

var common = {
  ...
  module: {
    preLoaders: [
      {
        test: /\.css$/,
        loaders: ['postcss'],
        include: PATHS.app
      },
      ...
    ],
    ...
  },
  postcss: function () {
    return [stylelint({
      rules: {
        'color-hex-case': 'lower'
      }
    })];
  },
  ...
}
```

If you define a CSS rule, such as `background-color: #EFEFEF;`, you should see a warning at your terminal. See stylelint documentation for a full list of rules. npm lists [possible stylelint rulesets](https://www.npmjs.com/search?q=stylelint-config). You consume them as your project dependency like this:

```javascript
var configSuitcss = require('stylelint-config-suitcss');

...

stylelint(configSuitcss)
```

Given stylelint is still under development, there's no CLI tool available yet. `.stylelintrc` type functionality is planned.

## Checking JavaScript Style with JSCS

![JSCS](images/jscs.png)

Especially in a team environment, it can be annoying if one guy uses tabs and another uses spaces. There can also be discrepancies between space usage. Some like to use two spaces, and some like four for indentation. In short, it can get pretty messy without any discipline. To solve this issue, JSCS allows you to define a style guide for your project.

T> Just like ESLint, also JSCS has autofixing capabilities. To fix certain issues, you can invoke `jscs --fix` and it will modify your code.

JSCS can be installed through

```bash
npm i jscs jscs-loader --save-dev
```

[jscs-loader](https://github.com/unindented/jscs-loader) provides Webpack hooks to the tool. Integration is similar as in the case of ESLint. You would define a `.jscsrc` with your style guide rules and use configuration like this:

```javascript
module: {
  preLoaders: [
    {
      test: /\.jsx?$/,
      loaders: ['eslint', 'jscs'],
      include: PATHS.app
    }
  ]
}
```

Here's a sample configuration:

**.jscsrc**

```json
{
  "esnext": true,
  "preset": "google",

  "fileExtensions": [".js", ".jsx"],

  "requireCurlyBraces": true,
  "requireParenthesesAroundIIFE": true,

  "maximumLineLength": 120,
  "validateLineBreaks": "LF",
  "validateIndentation": 2,

  "disallowKeywords": ["with"],
  "disallowSpacesInsideObjectBrackets": null,
  "disallowImplicitTypeConversion": ["string"],

  "safeContextKeyword": "that",

  "validateQuoteMarks": {
    "mark": "'",
    "escape": true,
    "ignoreJSX": true
  },

  "excludeFiles": [
    "dist/**",
    "node_modules/**"
  ]
}
```

JSCS supports *package.json* based configuration through `jscsConfig` field.

T> ESLint implements a large part of the functionality provided by JSCS. It is possible you can skip JSCS altogether provided you configure ESLint correctly. There's a large amount of presets available for both.

## EditorConfig

[EditorConfig](http://editorconfig.org/) allows you to maintain a consistent coding style across different IDEs and editors. Some even come with built-in support. For others, you have to install a separate plugin. In addition to this you'll need to set up a `.editorconfig` file like this:

**.editorconfig**

```yaml
root = true

# General settings for whole project
[*]
indent_style = space
indent_size = 4

end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

# Format specific overrides
[*.md]
trim_trailing_whitespace = false

[app/**.js]
indent_style = space
indent_size = 2
```

## Conclusion

In this chapter, you learned how to lint your code using Webpack in various ways. It is one of those techniques that yields benefits over the long term. You can fix possible problems before they become actual issues.
