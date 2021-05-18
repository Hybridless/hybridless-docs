# Define your function

After the initial setup, you are now ready to start your hybridless functions definitions. Here you will see **key** differences between the common serverless definition and how powerful this framework can be. 

Inside your **serverless.yml** on need to define the top-level `hybridless` key and define your functions as below.

{% code title="serverless.yml" %}
```text
.....
hybridless:
  functions:
    ClusterName:
      handler: src/handler.handler
      memory: 512
      timeout: 60
      events:
        - eventType: httpd
          runtime: nodejs13
          concurrency: 2
          routes:
            - method: ANY
              path: "*"
```
{% endcode %}

Simple as that we would have a definition capable of running an application load balancer, with an ECS cluster with 2 tasks inside a`nodejs13`runtime as we would be running on lambda by just deploying your application as you would be doing on serverless. \(Can't wait to deploy? Go to [deployment section](../deployments/build.md)\)

And because we use a function-first approach and the deployment/resource strategy is defined at the event level, bare minimum would be required to switch this function lambda \(serverless\) or even add a specific route on lambda \(by using a different endpoint\), by adding another event of type `lambda`

```text
.....
hybridless:
  functions:
    ClusterName:
      handler: src/handler.handler
      memory: 512
      timeout: 60
      events:
        - eventType: httpd
          runtime: nodejs13
          concurrency: 2
          routes:
            - method: ANY
              path: "*"
        - eventType: lambda
          runtime: nodejs12.x
          protocol: http
          routes:
            - method: GET
              path: "/info"
```

{% hint style="success" %}
For the lack of simplicity we are using`httpd`tasks behind an application load balancer \(ALB\) and lambda behind the API Gateway, but theoretically we achieve full hybrid failover APIs by using protocol`httpLoadBalancer`or using DNS technics. 
{% endhint %}



