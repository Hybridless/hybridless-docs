# Installation

## Requirements

Hybridless is directly coupled with the `serverless` framework and therefore is the only dependecy required for it to work. All other dependecies for javascript as `webpack` and `babel` for example are handled and injected by the plugin during the development build and don't affect the total build/image size.

## Setup

### Minimal Setup

The minimal setup could be done as:

```bash
$ npm i -G serverless
$ serverless #to setup the project (run npm i -G serverless if cmd not found)

###AFTER SETUP

$ cd example
$ npm init -y
$ npm i -D serverless @hybridless/hybridless
```

![Setup should be done as follow](.gitbook/assets/screen-shot-2021-05-17-at-6.06.11-pm.png)

{% hint style="info" %}
 Even if you are not developing on javascript or have any code, deployment is done via javascript and you are required to have the package.json and serverless.yml on the folder you are building \(commonly on the root directory of the project\).
{% endhint %}

Once you have completed the steps above, you should have the following structure.

![](.gitbook/assets/screen-shot-2021-05-17-at-6.09.56-pm.png)

Now, on your **serverless.yml** file, you should include the plugin on the top and everything can be left untouched or erased at the point you have only the following if you want a cleaner file. 

{% code title="serverless.yml" %}
```yaml
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
```
{% endcode %}

### 

### Configuring Environments

As a good practice, we should always create an **env.yml** file at the root of your directory to handle multiple stages environments as follow. If you are not using **env.yml** based on the stages or not using it at all, you **should change** the `serverless.yml line 11` accordingly. 

{% code title="env.yml" %}
```yaml
dev:
    APP_NAME: 'Example dev'
    USE_EC2: false
    TASK_MEM: 256
    SES_EMAIL_FROM: 'dev.example.com'
    ABC_API_KEY: ${ssm:/mytestapi-${self:custom.stage}/ABC_API_KEY}
stage:
    APP_NAME: 'Example stage'
    USE_EC2: false
    TASK_MEM: 256
    SES_EMAIL_FROM: 'stage.example.com'
    ABC_API_KEY: ${ssm:/mytestapi-${self:custom.stage}/ABC_API_KEY}
prod:
    APP_NAME: 'Example'
    USE_EC2: true
    TASK_MEM: 1024
    SES_EMAIL_FROM: 'example.com'
    ABC_API_KEY: ${ssm:/mytestapi-${self:custom.stage}/ABC_API_KEY}
```
{% endcode %}

This approach allows you to have multiple configurations based on your stage need and also to have a bunch of different configurations and constants injected right on your task or lambda and accessible on the process environment ivars. 

Theorically you can live with one environment file per project, but if desired, you can always specify the environment property on the function or even event level to have that specific function in that environments only. This property allows a pure key-value object but would also allow the import syntax below.

```yaml
${file(config/another_env.yml):${self:custom.stage}}
#or just
${file(config/another_env.yml)}
```

As an **important** note, is good to mention that this feature is all provided by the **serverless framework**, the layer on top of hybridless. If you want to know more about serverless environments, please check this [link](https://www.serverless.com/framework/docs/providers/aws/guide/variables#referencing-environment-variables).



### Configuring Webpack \(Optional\)

Optionally, **if you are using Javascript \(ES6&gt;\)** at the function level \(your code\) you probably will want to have webpack enabled so everything is compile before deploying it. Luckly all the dependecy  management for webpack, babel core and loader, and serverless integration is done for you, except two little steps that allows you to have full control over your webpack configuration, instead of using embeeded webpack configurations as commonly see on spa react apps.

Basically, the idea is creating an entry on your **serverless.yml** file pointing the webpack configuration to your `webpack.config.js` and the create the webpack configuration file. This configuration file will have entry functions and externals exported by the hybridless framework, indicating what functions are required to be compiled an how.

Add the custom section to your **serverless.yml** file:

{% code title="serverless.yml" %}
```yaml
custom:
  webpack: #Please, https://github.com/serverless-heaven/serverless-webpack#configure for more webpack options
    webpackConfig: ./src/webpack.config.js
    includeModules: true
```
{% endcode %}

Now, the most important is creating the webpack config file. This can be on any path absolute to your project and needs to be on javascript format so you can execute the hybridless static interface to get the entrypoint functions and externals required to proper compile your code. 

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
Theorically you can customize your webpack configuration as you will, except by not removing the the entries and externals call for the hybridless framework.
{% endhint %}

It's relevant to say that all this webpack job is done by levering the webpack, babel and serverless-webpack plugin, so any additional configuration or problem with this specifically might be found on these projects pages. Also, another important point is that all the management of these dependecies, the setup is handled for you, so try to not duplicate the serverless-webpack setup on your serverless.yml file for example, this could lead to undocumented issues. 

## Examples

Examples are maintained on a separated repository and should be up-to-dated with all the frameworks latest versions and are the quickiest way to get started!

| Example | Link |
| :--- | :--- |
| Minimal |  |
| Webpack |  |
| Hybrid clusters |  |
| Processes |  |
| Containers |  |
| Custom Runtimes |  |
| CloudFormation |  |
|  |  |

   All examples can be found on this [repository](https://github.com/hybridless/examples) on GitHub.

