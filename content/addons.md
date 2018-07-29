# Addons

The modern web developer has an incredible selection of open source code that they can use through the npm ecosystem, plus great tools for writing their own modules. Ember developers are free to use regular npm packages in their apps, but there are also thousands of packages that are made specifically for the Ember ecosystem. We call such packages "addons."

Addons are often JavaScript code, reusable UI components, compiling tools, deployment pipelines, templates, stylesheets, and more. Think of addons as node.js libraries with superpowers. In addition to the usual functionality of an npm package, addons can also help with generating new files, preprocessing, file fingerprinting, and more.

In this guide, we'll cover:
- Finding and using community addons
- Addon file structure
- Writing your addon within an app
- Writing an addon that can be shared
- Turning a regular npm package into an addon
- Testing addons
- Managing assets
- and more

## Finding and using community addons

[Ember Observer](https://www.emberobserver.com/) is the definitive way to explore community addons. Although addons can be found by [searching the npm repository directly](https://www.npmjs.com/search?q=ember), Ember Observer has ranked lists of the most popular addons and ratings to help developers choose between them. Most are made to drop right into your app with zero configuration. Many addons are backwards-compatible to earlier versions of Ember too!

To install an addon, use:

```bash
ember install <addon-name>
```

To be safe, it's a good idea to restart the local server after installing new dependencies, and especially before trying to debug an addon that isn't working.

The `ember install` command is similar to the `npm install` you might already be familiar with. It creates an entry in the app's `package.json` and downloads the addon and its dependencies into a `node_modules` directory. However, `ember install` does even more than `npm`. Some addons create new files or make modifications to your app when they are installed via `ember install`.

### Choosing an addon

The [top 100 list](https://www.emberobserver.com/lists/top-addons) is an important first stop for new developers. Many Ember users have a personal set list of addons that they include in all their apps, and this is the best way to find out what those addons might be. Some addons are totally unique to Ember, while others exist to make it easier to use features of regular npm packages within an app.

For example, these community-authored addons bring in familiar functionality from regular npm packages:

- Stylesheet tooling like [ember-cli-sass](https://www.emberobserver.com/addons/ember-cli-sass), which provides [SASS](https://sass-lang.com/) as an alternative to plain CSS
- JavaScript utilities like [ember-moment](https://www.emberobserver.com/addons/ember-moment), which offers some Ember conveniences to the base [moment library](https://www.npmjs.com/package/moment)
- Full UI frameworks and design kits like [ember-bootstrap](https://www.emberobserver.com/addons/ember-bootstrap), [semantic-ui-ember](https://www.emberobserver.com/addons/semantic-ui-ember), and [ember-paper](https://www.emberobserver.com/addons/ember-paper). These offer easier, more reliable, more performant functionality than just using the npm packages directly.

Here are just a few examples of popular community-maintained addons unique to Ember, in no particular order:

- A wealth of UI component libraries, like [ember-power-select]() and [ember-basic-dropdown](https://www.emberobserver.com/addons/ember-basic-dropdown)
- Tools to automate deployment, like [ember-cli-deploy](https://www.emberobserver.com/categories/deployment) and its own ecosystem of plugins
- Testing tools like [ember-test-selectors](https://www.emberobserver.com/addons/ember-test-selectors), to make DOM assertions in testing easier and clearer, and [ember-a11y-testing](https://www.emberobserver.com/addons/ember-a11y-testing) to check for accessibility
- Authentication libraries and plugins, like [torii](https://www.emberobserver.com/addons/torii) and [ember-simple-auth](https://www.emberobserver.com/addons/ember-simple-auth), and [ember-oauth2](https://www.emberobserver.com/addons/ember-oauth2)
- Async and state management tools like [ember-concurrency](https://www.emberobserver.com/addons/ember-concurrency) and [ember-lifeline](https://www.emberobserver.com/addons/ember-lifeline). Don't let a user's impatient clicks kick off 100 API requests.
- [liquid-fire](https://www.emberobserver.com/addons/liquid-fire), for animating things like route transitions to provide a smooth, native-app-like experience.
- and so many more!

Open Source projects like these addons rely on community members helping out. Some addons are sponsored by companies, but many are maintained on 100% volunteer time. If something doesn't work the way you expect, could be better documented, has a bug, or could be added as a new feature, please speak up and pitch in!

## Writing an addon

Writing an addon is a great way to organize code, share it with others, or get the foundational knowledge to contribute to Open Source addons. By separating features into an addon instead of leaving it as an in-app component, more developers can work in parallel and breaking changes can be managed independently. Maintainability goes up, and testing becomes easier.

Since the Ember community has so many addons, one of the best ways to learn more advanced addon development is to study existing addons. If we get stuck or need to see some examples in action, [Ember Observer's code search](https://www.emberobserver.com/code-search) can be very helpful.

Although an addon looks and feels a lot like an Ember app, it is important to work in small steps and validate that each piece is working before writing more code. Developers who are very comfortable with Ember apps might otherwise make a lot of changes and walk into some common pitfalls that can be hard to debug in unison.

### Generating the addon files

Use the ember-cli to create the file structure for the addon. Run this command in a fresh directory, not inside an existing Ember app:

```bash
ember addon <addon-name> [options]
```

The result is the creation of a directory called `<addon-name>`, which has many files and looks a bit like an Ember app. We won't need to use all the files to make a useful addon. By convention, _most_ Ember addons start with `ember` in the name, like `ember-basic-dropdown`. This will help other developers find our addon.

### Addon file structure

In some ways, an addon is like a mini Ember app. It has a very similar file structure, uses a lot of the same API methods, and can do most things that components are able to do. 

Let's take a look a some of the most important files and folders in an addon, and how they are different from what we would find in an app. 

#### `addon/` 

This directory can hold many of the same subdirectories and files that an Ember app would, like `/components/` and `/templates/`. For developers who are making components, most of the work will happen here.

#### `app/` 

The `app` directory plays an important role to help an Ember app automatically discover the components exported by an addon.
The default way to make a component is to put the implementation in `addon/`, which allows developers to import and extend the addon component. However, Ember apps always look for components within the `app` namespace, so we must re-export our components from `app/`.

Fortunately, when we run `ember generate component my-component-name` in an addon project, the CLI takes care of all this re-exporting business. It creates the necessary files and code for us. Addon authors don't usually need to think about the `app` directory or do any work in it.

#### `tests/dummy/`
This directory contains a full Ember app for addon testing purposes. During tests, we can check to make sure that the addon works or looks as expected when it is used in an app. Many addon developers use the dummy app to hold their documentation site's content as well.

#### `package.json` 

If we want other people to be able to use our addon, we need to specify a name, license, version, the repository url, and description. For an addon to show up on [https://emberobserver.com](Ember Observer), it must have `keywords: ["ember-addon"]` and a repository URL.

#### `config/ember-try.js`

This is a place to configure which versions of Ember that the test suite should check for compatibility. See the [ember-try](https://github.com/ember-cli/ember-try) repository on GitHub for more information.


## Creating a reusable UI component addon

To create a UI component template that can be shared between apps, the process is a lot like creating a normal app component:

```bash
ember generate component <addon-name>
```

However, in the context of an addon, this creates more files than we would see in an app:

```
  create addon/components/<addon-name>.js
  create addon/templates/components/<addon-name>.hbs
  create tests/integration/components/<addon-name>-test.js
  create app/components/<addon-name>.js

```

Some files go in the `app` directory, while others go into the `addon` directory. We'll start by looking at the addon directory. Whatever we put in the `<addon-name>.hbs` file is what could be used immediately in an app, and will be referenced in templates as `{{addon-name}}`. Later on in this guide, we will cover how to export multiple templates with different names from a single addon project.

Let's say that our addon should wrap some content in a button tag. The addon template should look like this:

```hbs
<!-- addon/templates/components/<addon-name>.hbs -->

<button>{{buttonLabel}}</button>
```

Our goal is to be able to pass the `buttonName` value to the addon, just like we'd pass it to a normal component within an app:

```hbs
<!-- This is a handlebars file in the app using the addon -->

{{addon-name buttonLabel="Register"}}
```

### Trying out the addon template in an app

There are several options to see the addon in action. We could use `npm link` or `yarn link` to try it out locally or publish the addon online. We'll use `link` while we are still developing and testing. 

1. Since our addon uses a template, we need the template precompiler to be a `dependency` and not a `devDependency`. In the addon's `package.json`, move the entry for `ember-cli-htmlbars` into the `dependencies` listing. If this step is missed, there is a clear error message when we try to start the app that uses our addon.
2. From within the addon directory, `yarn install` or `npm install`
3. From within the main addon directory, run the command `yarn link` or `npm link`
4. In the Ember app that should use the addon, do `yarn link <addon-name>` or `npm link <addon-name>`.
5. In the Ember app's `package.json`, add an entry for your addon, like `"addon-name": "*"`. The `*` means that it will include all version numbers of our addon.
6. Run `yarn install` or `npm install`
7. Add a reference to your addon somewhere in an app template, like `{{addon-name buttonLabel="Register"}}`
8. Run a local server with `ember serve`

We should now see our addon in action!

**Having problems?**
Check to make sure that your `package.json` is valid, looking for missing commas or trailing commas. "Template precompiler" errors mean that we forgot Step One. `404 not found` means we forgot to `yarn` or `npm install`. Other errors are likely due to file naming problems. For example, trying to rename an addon or component after it has been created is prone to mistakes. And of course, we need to make sure we saved all the files that we changed along the way. (The author of this guide made every single mistake in this list while writing it.)

### Making a UI component available in block form

In an Ember app, components can be used in ["simple" or "block" form](https://guides.emberjs.com/release/components/wrapping-content-in-a-component/). Addon templates have the same capabilities. Simple form is useful when the app developer should providing data objects or configuration values to the addon, while the block form is most useful when the developer should also be able to pass in some of their own templating, content, and interactivity.

In an Ember app, a block style component uses the `{{yield}}` helper as a placeholder for where the passed-in content will go. It is the same in an Ember addon. 

Let's change our button addon we made earlier so that developers can pass in their own handlebars content by using the `{{yield}}` helper:

```hbs
<!-- addon/templates/components/<addon-name>.hbs -->

<button>{{yield}}</button>
```

Now, an app can use the addon with their own content inside:

```hbs
<!-- This is a handlebars file in the app using the addon -->

{{#addon-name}}
  Register <img href="./images/some-cute-icon.png" alt="">
{{/addon-name}}
```

Whatever goes inside the block form addon will show up where the `{{yield}}` was. This is the markdown that renders in the app:

```html
<!-- markdown rendered by running the app -->

<button>
  Register <img href="./images/some-cute-icon.png" alt="">
</button>
```

### Styling a UI component addon

Addon developers have many options for handling styles within their addons. For example, we could stick to plain old CSS, or use a preprocessor like Less or Sass. Most seasoned addon authors prefer SASS/SCSS. We could automatically style the UI elements when they are used in an app, or we could let the developer who installed the addon choose which stylesheets to include. Here are a few different approaches. Luckily, the Ember CLI handles most of the work for us and we don't have to worry about the inner workings of asset compilation.

#### Automatically including CSS stylesheets in addons

To automatically include CSS styling for your addon, create a `styles` directory in the `addon` directory, and place your CSS files in it. For example, we could create `addon/styles/our-addon-name.css`.

When our addon is used in an app, these CSS rules will be added to the end of the app's `vendor.css` when it is built or served. The rules will be in the same scope as the rest of the app's css, so name your class selectors wisely! Otherwise they will clash with the styles of other addons or the app's own styling.

For example, writing a CSS rule for `div` is problematic, because it will affect all `div`s in the app, but a rule targeting `.my-app-name div` is probably fine.

Let's add a class to our template and some styles to target the class:

```hbs
<!-- addon/templates/components/<addon-name>.hbs -->

<button class="addon-name-button">{{yield}}</button>
```

```css
/* addon/styles/our-addon-name.css */

.addon-name-button {
  padding: 10px;
}
```

Now any buttons made using our addon will have the `padding: 10px` rule applied.

#### CSS stylesheets that require importing from addons

For some addons, it makes sense to give the developer the option to import the stylesheet we provide, or import no stylesheets at all. Using this approach, we could even offer the developer a few themes to choose from.

We can do this by creating stylesheets in the `app/styles/` directory instead. These stylesheets share a file namespace with the consuming app and all the other addons someone is using, so name them wisely. For example, if we name our stylesheet `addon.css`, that's likely to clash. Just as before, it's important to choose uniquely named targets for the CSS rules so that they don't clash with other addons or the app. 

Let's create `app/styles/our-addon-name.css` and add a rule to it: 

```css
/* addon/styles/our-addon-name.css */

.addon-name-button {
  border: black solid 2px;
}
```

For the stylesheet to be active in the app the addon is used in, the developer for that app must explicitly `import` the stylesheet by name. This must be done at the very top of the app's `app.css` file.

```css
@import 'our-addon-name.css'
/* The app's own app/styles/app.css */
```

If there are any problems getting this to work, one strategy is to build the addon with `ember build` and look inside the `dist` folder. The `dist` folder may be hidden by default in some code editors. The `dist` folder gives clues about what the consuming app sees as the file structure of the addon. See if the stylesheets are in `dist/assets/`. Then, in the Ember app, run `ember build` and look in the `dist` folder. We should see our stylesheets in `dist/assets` of the app too.

#### Using CSS preprocessors for the addon's stylesheets

While this guide focuses on the "out of the box" behavior of addons and the Ember CLI, there are some well-established patterns for handling stylesheets in a way that is scalable and maintainable. A CSS preprocessor like Sass (aka SCSS) allows you to nest style rules, use variables, and do simple mathematical operations.

The best way to learn how to CSS preprocessors in your addon is to consult the documentation for the preprocessor addon of your choice, and study how other addon authors have implemented stylesheets. For example, [ember-styleguide](https://github.com/ember-learn/ember-styleguide/) is a UI component library that was made for the main Ember websites. It uses [ember-cli-sass](https://www.emberobserver.com/addons/ember-cli-sass) to manage styles. You can search [Ember Observer](https://emberobserver.com) for many more examples of styling in action!

### Adding UI functionality with JavaScript

There are two main types of JavaScript functionality that an addon can provide: API methods that developers can use after importing your addon, and interactivity JavaScript that is part of UI components. We'll cover UI use cases first.

Interactivity in an addon can be handled the same way that it is done for an Ember app component. Every addon component template has a corresponding JavaScript file to go with it. For example, an addon can have its own actions, computed properties, and imported dependencies. Developers using the addon can pass in their own actions and attributes.

For more information about building interactivity for your addon, reference the [Ember Guides components section](https://guides.emberjs.com/release/components/defining-a-component/). Remember to test as you work!

### Providing multiple templates in one addon


## Writing a JavaScript utilties addon

Many addons have no UI components in them, or they offer a combination of JavaScript utilities and template helpers. In the regular npm ecosystem, JavaScript utility libraries are some of the most common packages. Although we could write a normal node package, providing an Ember addon to developers has some advantages. The developers don't need to worry about how to import a normal npm package. They can use `ember install our-addon-name` and get going right away.

### Providing public API methods in the addon

After we've created our addon file structure with `ember addon <addon-name>`, we can write some functions that will be available for an app to use. Such functions are commonly referred to as "public API." If the behavior of public API changes, it's convention in the Ember community to follow semver and change the major version of the addon. Semver is a cross-program-language versioning scheme that helps other developers or coworkers know which versions of a library will require them to refactor their apps. Learn more about semver [here](https://semver.org/).

Similar to a normal npm package, the entry point is named `index.js`. The files exported from `addon/index.js` will be available to developers using the addon in their apps.

Let's add some public methods to our addon! Don't forget to `export` your methods.

```js
// addon/index.js

const moreEnthusiasm = function (phrase) {
  return phrase + '!!!';
}

export { moreEnthusiasm }
```

Now, let's use the methods in an app:

```js
// The JavaScript file of some component in an app

import Component from '@ember/component';
import { moreEnthusiasm } from 'ember-addon-name';

export default Component.extend({
  actions: {
    confirmExcitement() {
      console.log(moreEnthusiasm('We made an addon'))
      // prints "We made an addon!!!" to the console
    }
  }
});

```

### Organizing public API code

One common pattern for managing an addon's JavaScript code is to define the methods in many separate files, perhaps grouped into subfolders, import them into `index.js`, and then export them.

For example, an `index.js` file might contain nothing more than imports and exports:

```js
import { moreEnthusiasm, curbedEnthusiasm } from 'our-app-name/utilities/enthusiasm.js'

export { moreEnthusiasm, curbedEnthusiasm };
```

### How to keep learning

This is a very tiny example of what addons can do in terms of providing JavaScript utilties to apps. For more advanced techniques, study other well established addons. Just like there are many ways and reasons to build an Ember app, the same is true for addons!

## Writing an npm package wrapper

<!-- Help wanted! -->
<!-- This section should mention when this is not necessary, and link to helpful resources like ember-auto-import -->

## In-repo addons

If the addon is just meant to be used in a single project, an "in-repo" addon could be created instead. The benefit is that it is lightweight, but there are some major limitations: an in-repo addon can't be shared between apps, versioned independently, or published to npm. 

From within an existing Ember app, create an in-repo addon:

```bash
ember generate in-repo-addon <addon-name> [options]
```

This generates a folder called `lib/<addon-name>` that contains its own `package.json` and an `index.js` file.

The most common use case for an in-repo addon is when there is a chance a component really should be a standalone library instead, but it is not yet clear if it should be broken out from the main app. Another reason is that it can be helpful for enforcing separation of concerns within an app.

## Other kinds of addons

<!-- Is there anything meaningful to say here? -->

## Documenting addons

For other developers to discover and use our addon, we need to teach them how to use it! 

Here are the most common ways that addons provide user-facing documentation:

- A detailed README on the GitHub project
- Creating a documentation website in the addon's dummy app, found in `/tests/dummy/`
- A combination of these two

It can be a lot of work to document an addon, so some seasoned addon contributors created [ember-cli-addon-docs](https://github.com/ember-learn/ember-cli-addon-docs), which provides templates for creating a site that shows off your addon. It's a documentation resource and demo in one. Many addon authors choose to host their documentation sites on [GitHub pages](https://help.github.com/articles/what-is-github-pages/), which is free and built into GitHub.

What about documentation of the code itself? Many JavaScript documentation tools have not caught up to ES6-style modules and classes, so the best bet is to look at how some popular addons handle code comments and find a style that works for you. If your code is well commented, it will help out new contributors and reduce the number of issues that others open.

<!-- Help wanted - any best practices for code comments??? -->

Lastly, be sure to provide a few notes about how others can contribute to the project! These notes commonly go in a `README.md` or `CONTRIBUTING.md` file.

## Testing an addon

<!-- help wanted -->

## Advanced addon configuration

<!-- Help wanted! -->