# Function Reference

Functions ...\[TODO\]

## Definition

```yaml
...serverless.yml content (service, provider, plugins, custom, Resources..)
hybridless:
    functions: 
        FunctionName:
        //ECS cluster
        ecsClusterArn?: any;
        ecsIngressSecGroupId?: string;
        enableContainerInsights?: boolean; //default is respecting account settings
        //ALB
        albListenerArn?: any;
        albIsPrivate?: boolean;
        albAdditionalTimeout?: number; //defaults to 1 second
        //
        vpc?: { //this is a auto created VPC. Only one auto created VPC is allowed per project for now. 
          cidr: string;
          subnets: string[];
        } | { //Optional ivars to dictate if will use existing VPC and subnets specified
          vpcId: string;
          securityGroupIds: string[];
          subnetIds: string[];
          albSubnetIds?: string[];
        };
        //high order props (can be overwritten at event level)
        timeout?: number;
        handler: string; 
        memory?: number; //defaults to 1024
```

{% hint style="info" %}
For the lack of simplicity we are using inline..\[TODO\]
{% endhint %}

## Parameters

### `ecsClusterArn`

* **Description:** Tags will be propagated to all cloud resources that are auto-created and managed by Hybridless \(only\) as ECS tasks, Load Balancer, Lambda functions and others. Tags are useful when multiple application are deployed in one account and you what to differentiate costs, monitor only certain applications or even automate some tasks. 
* **Value**: Array of key value strings or cloud formation intrinsic functions that will return a string.
* **Default:** null \(no tags are associated by default\)
* _Optional_ 

    Example:

```yaml
hybridless:
    tags:
        MyKeyName: MyValue
        ApplicationName: Example
```



### `disableWebpack`

* **Description:** If you are using Javascript under es6 and your code does not require transpilation OR you are doing something apart from the standard enabled webpack integration \([serverless-webpack](https://www.npmjs.com/package/serverless-webpack)\) and want to disable hybridless from transpiling your javascript code through webpack, you can set it to `true` and webpack will be completely off on the deployments.
* **Value:** boolean, `true` or `false`
* **Default:**  `false`
* _Optional_

    Example:

```yaml
hybridless:
    disableWebpack: true
```



### `functions`

* **Description:** Function are basically where you can define your collection of functions and events. Please, refer to [function documentation](cluster-reference.md) for more details. Functions can be defined inline but also imported as shown on example below.
* **Value:** Key valued functions on a single object or multiples to support multiple files import.
* **Required** \(at least one function more be defined\)

        Examples:

{% tabs %}
{% tab title="Import" %}
```yaml
hybridless:
    functions:
        - ${file(config/funcs.yml)}
        - ${file(config/funcs_two.yml)}
```

{% code title="config/funcs.yml Or config/funcs\_two.yml.." %}
```yaml
FirstFunction:
    ....
SecondFunction:
    ....
```
{% endcode %}
{% endtab %}
{% endtabs %}

