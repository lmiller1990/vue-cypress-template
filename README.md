Cypress Component Testing is here! Quickstart:

1. clone this repo
2. `yarn install`
3. `yarn cypress open-ct`
4. Write some tests!

## Getting Start with Cypress Component Testing (Vue 2/3)

As of Cypress 7.0, the new Cpyress Component Testing Runner is now bundled with the Cypress! It takes inspiration and builds on the learnings from the original component testing implementation, which was hidden behind the `experimentalComponentTesting` flag.

In this blog post we will see how to set up Cypress Component Testing with a Vue CLI project. It supports both Vue 2 and Vue 3, with TypeScript support out of the box.

You can get the source code for the example used in the blog post [here](https://github.com/lmiller1990/vue-cypress-template).

## Creating a new Vue CLI Project

Create a new Vue CLI project to get started. For this example I chose _Vue 2_ and _TypeScript_. 

## Configuring Cypress Component Testing

Once you've got a Vue project, you'll also need to install Cypress and the Webpack Dev Server and Vue adapters. Vue CLI projects are Webpack based; that's why we are installing the relevant Webpack adapter:


```sh
# Vue 2
yarn add cypress @cypress/vue @cypress/webpack-dev-server --dev

# Vue 3
npm install cypress @cypress/vue@next @cypress/webpack-dev-server --dev
```

Component Testing is configured as a [Cypress plugin](https://docs.cypress.io/guides/tooling/plugins-guide). This means you need to create a plugins file. By default this goes in `cypress/plugins/index.js`. 

Next, we need to register the `dev-server:start` event and tell Cypress to use the same Webpack configuration as the Vue CLI project uses.

```js
const { startDevServer } = require('@cypress/webpack-dev-server')
const webpackConfig = require('@vue/cli-service/webpack.config.js')

module.exports = (on, config) => {
  on('dev-server:start', options =>
    startDevServer({
      options,
      webpackConfig
    })
  )

  return config
}
```

Finally, we need to tell Cypress where and how to find our tests. Cypress is heavily configurable via `cypress.json`. My `cypress.json` looks as follows:

```
{
  "component": {
    "componentFolder": "src",
    "testFiles": "**/*.spec.ts"
  }
}
```

All my comoponent are in `src`, and the spec files are always named `*.spec.ts`.

## Writing Some Tests

It is finally time to write some tests. I created `src/components/HelloWorld.spec.ts` to try things out with the following minimal test:

```ts
import { mount } from '@cypress/vue'
import HelloWorld from './HelloWorld.vue'

describe('HelloWorld', () => {
  it('renders a message', () => {
    const msg = 'Hello Cypress Component Testing!'
    mount(HelloWorld, {
      propsData: {
        msg
      }
    })

    cy.get('h1').should('have.text', msg)
  })
})
```

The `mount` function is very similar to the one from [Vue Test Utils](https://vue-test-utils.vuejs.org/). It's actually built on top of Vue Test Utils, so you can use the mounting options you might already be familiar with.

Learn more about how to write assertions with Cypress [in the official docs](https://docs.cypress.io/guides/references/assertions). Get an overview of the `cy` object and how to use it [here](https://docs.cypress.io/guides/core-concepts/introduction-to-cypress#Cypress-Can-Be-Simple-Sometimes).

Finally, open the Component Testing runner:

```sh
yarn cypress open-ct # or npx cypress open-ct
```

Select the spec and watch the test pass!

![Cypress Component Testing Runner Success](https://github.com/lmiller1990/vue-cypress-template/raw/master/img1.png)

Try updating the test and making it fail. Cypress will re-run your test (almost) instantly. This makes for a great red-green-refactor loop.

![Cypress Component Testing Runner Failure](https://github.com/lmiller1990/vue-cypress-template/raw/master/img2.png)

## Vue 3 Usage

Everything works the same with Vue 3. Just make sure you have a Vue 3 project and the correct adapter:

```sh
yarn add @cypress/vue@next --dev
# or
npm install @cypress/vue@next --dev
```

The `mount` function exported by `@cypress/vue` has the same API for both Vue 2 and Vue 3. The mounting options are the same as [Vue Test Utils](https://next.vue-test-utils.vuejs.org/api/#mount), so if you've used Vue Test Utils before, you'll feel right at home. 

## Discussion

Cypress Component Testing is an alternative to a jsdom based testing environment, such as Jest and Vue Test Utils. Cypress Component Testing offers many benefits:

- Runs in a real browser. This means your tests are closer to what your users will be experiencing.
- Visual. You can see exactly what is rendered. No more `console.log(wrapper.html())`.
- Powered by Cypress - the most popular and reliable E2E testing tool out there.

It also doubles as a *design environment*. You can see the component as you develop it, and hot reload give you a near instance feedback loop. It can potentially take the place of not only your Jest based test infrastructure, but your Storybook based design infrastructure as well. 

Cypress Component Testing is still in alpha but is quickly evolving and promises to change the landscape of Component Testing.

## Conclusion

Cypress Component Testing brings everything that is great about Cypress to Component Testing. Since the underlying adapters are built on libraries like Webpack and Vue Test Utils, you don't need to throw away your entire test suite - incrementally migration is more than possible. 

The visual aspect united testing and design in a single tool. My days of grepping a messy console output to figure out what the user will see are over - I can see exactly what the component will look like as my tests run.

You can get the source code for the example used in the blog post [here](https://github.com/lmiller1990/vue-cypress-template).
