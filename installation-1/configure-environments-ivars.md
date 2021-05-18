# Configure Environments Ivars

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

