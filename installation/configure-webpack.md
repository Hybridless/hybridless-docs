# Configure Webpack

Optionally, **if you are using Javascript \(ES6&gt;\)** at the function level \(your code\) you probably will want to have webpack enabled so everything is compiled before deploying it. Luckily all the dependency management for webpack, babel-core and loader, and serverless integration is done for you, except two little steps that allow you to have full control over your webpack configuration, instead of using embedded webpack configurations as commonly see on spa react apps.

Basically, the idea is to create an entry on your **serverless.yml** file pointing the webpack configuration to your `webpack.config.js` and then create the webpack configuration file. This configuration file will have entry functions and externals exported by the hybridless framework, indicating what functions are required to be compiled and how.

Add the custom section to your **serverless.yml** file:

{% tabs %}
{% tab title="Partial" %}
{% code title="serverless.yml" %}
```yaml
custom:
  webpack: #Please, https://github.com/serverless-heaven/serverless-webpack#configure for more webpack options
    webpackConfig: ./src/webpack.config.js
    includeModules: true
```
{% endcode %}
{% endtab %}

{% tab title="Complete File" %}
```
service: example
plugins:
  - '@hybridless/hybridless'
provider:
  name: aws
  runtime: nodejs12.x
  stage: ${opt:stage, 'dev'}
  region: ${opt:region, 'ca-central-1'}
  memorySize: 512
  timeout: 30
  environment: ${file(env.yml):${self:custom.stage}}
custom:
  stage: ${self:provider.stage}
  webpack:
    webpackConfig: ./src/webpack.config.js
    includeModules: true
```
{% endtab %}
{% endtabs %}

Now, the most important is creating the webpack config file. This can be on any path absolute to your project and needs to be in javascript format so you can execute the hybridless static interface to get the entry point functions and externals required to properly compile your code.

```yaml
const hybridless = require('@hybridless/hybridless');
// const copyWebpackPlugin = require('copy-webpack-plugin');
module.exports = {
  entry: hybridless.getWebpackEntries(),
  target: "node",
  // Generate sourcemaps for proper error messages
  devtool: 'source-map',
  // Since 'aws-sdk' is not compatible with webpack,
  // we exclude all node dependencies
  externals: [ hybridless.getWebpackExternals() ],
  mode: hybridless.isWebpackLocal() ? "development" : "production",
  optimization: {
    // We do not want to minimize our code.
    minimize: false
  },
  performance: {
    // Turn off size warnings for entry points
    hints: false
  },
  // Run babel on all .js files and skip those in node_modules
  module: {
    rules: [
      {
        test: /\.js$/,
        loader: "babel-loader",
        include: __dirname,
        exclude: /node_modules/
      }
    ]
  },
  node: { __dirname: false },
  plugins: [
    // new copyWebpackPlugin([
    //   { from: 'someresources', to: 'resources' },
    // ]),
  ],
};
```

{% hint style="warning" %}
Theoretically, you can customize your webpack configuration as you will, except by not removing the entries and externals calls for the hybridless framework.
{% endhint %}

It's relevant to say that all this webpack job is done by levering the [webpack](https://github.com/webpack/webpack), [babel](https://babeljs.io/) and [serverless-webpack](https://github.com/serverless-heaven/serverless-webpack#readme) plugin, so any additional configuration or problem with this specifically, might be found on these projects pages. Also, another important point is that all the management of these dependencies and setup is handled for you, so try to not duplicate the `serverless-webpack` setup on your serverless.yml file, for example. This could lead to undocumented issues.

