# Installation

## Requirements

Hybridless is directly coupled with the `serverless` framework and therefore is the only dependecy required for it to work. All other dependecies for javascript as `webpack` and `babel` for example are handled and injected by the plugin during the development build and don't affect the total build/image size.

If you are not familiar with **serverless**, please check this [link](https://www.serverless.com/).



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



### 

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

