# webpack-node-test-runner

Simple CLI that helps run your tests in node after bundling with webpack, and then rerun test files dependent on the files that changed.

## setup

 - Use [`require.context`](https://webpack.github.io/docs/api-in-modules.html#require-context) to load your test files from a single entry file
 - Define your node modules as [`externals`](https://webpack.github.io/docs/configuration.html#externals)
 - Within your entry file, check for the `__webpack_ids_to_test__` variable. When using watch, this array will include module ids for the tests that need to be re-run.

    ```js
    const context = require.context('./', true, /\/__tests__\//)
    if (global.__webpack_ids_to_test__) {
      // only require the necessary components
      global.__webpack_ids_to_test__.forEach(id => __webpack_require__(id))
    } else {
      // require all includes modules
      context.keys().forEach(key => context(key))
    }
    ```

## run

Once setup use `webpack-node-test-runner` from the command line:

```sh
webpack-node-test-runner --config ./webpack_config/test --watch -- --reporter dot
```

Use `webpack-node-test-runner --help` to see all options. Options defined after the `--` are sent to mocha, so things like `--reporter` and `--ui` should be defined here.

## inspiration

This project, especially the feature to only re-run certain tests, was inspired by [webpack-mocha](https://www.npmjs.com/package/mocha-webpack). I had intended to add support for `devtool: "eval"` to that project but found the approach different from what I had in mind, and it was simple enough to re-write.
