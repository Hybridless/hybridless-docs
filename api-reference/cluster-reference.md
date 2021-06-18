# Function Reference

Functions are basically a virtual definition of a cluster of services \(events\), that commonly have the same general purpose or strong similarities, but are triggered differently. ECS clusters can be auto-created if needed or referenced from another ECS physical cluster so you can reuse the same cluster across these virtual clusters and share EC2 instances across services and applications for example.

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
            securityGroupIds: string[] | object;
            subnetIds: string[] | object;
            albSubnetIds?: string[] | object;
          };
          //high order props (can be overwritten at event level)
          timeout?: number;
          handler: string; 
          memory?: number; //defaults to 1024
```

{% hint style="success" %}
For the lack of simplicity, we are using an inline function definition, but it's highly recommended to **NOT** use this form but use the [import form instead.](plugin-reference.md#functions)
{% endhint %}

## Parameters

### `ecsClusterArn`

* **Description:** When specified, it will not create an ECS cluster for the required events and instead, will place all required ECS tasks on the specified cluster. Should always be in companion with `ecsIngressSecGroupId` \(described below\)
* **Value**: ECS Cluster ARN. Intrinsic functions and SSM imports are fully compatible. 
* **Default:** `null` \(ECS cluster will be auto-created and managed by Hybridless\).
* _Optional_ 

    Examples:

```yaml
ecsClusterArn: arn:aws:ecs:us-west-2:123456789012:cluster/default
ecsIngressSecGroupId: ${ssm:/prefix-${self:custom.stage}/ecsIngressSecGroupId} ### ssm form
```



### `ecsIngressSecGroupId`

* **Description:** When`ecsClusterArn`is specified, you should also specify the ingress security group for that cluster.
* **Value:** Ingress Security Group ID.
* **Default:**  `null`
* _Optional_

    Example:

```yaml
ecsIngressSecGroupId: sg-0700eee00ee0ee0I
```



### `enableContainerInsights`

* **Description:** When enabled, it will overwrite the account settings and enable the ECS container insight for the auto-created ECS cluster. If false, it will respect the account settings.
* **Value:** boolean, `true` or `false`
* **Default:** `false` \(does respect the account settings\)
* _Optional_

        Example:

```yaml
enableContainerInsights: true
```



### `albListenerArn`

* **Description:** If specified, the plugin will not create the Application Load Balancer for that specific function and will attach all target groups from each ECS service created to the specified load balancer.
* **Value:** Application Load Balancer \(ALB\) ARN. Intrinsic functions and SSM imports are fully compatible. 
* **Default:** `null` \(ALB cluster will be auto-created and managed by Hybridless\).
* _Optional_

        Example:

```yaml
albListenerArn: arn:aws:elasticloadbalancing:us-east-2:123456789012:listener/app/my-load-balancer/1234567890123456/1234567890123456
albListenerArn: ${ssm:/prefix-${self:custom.stage}/albListenerArn} ### ssm form
albListenerArn: !Ref albListenerArn ### intrinsic function form
```



### `albIsPrivate`

* **Description:** Indicates if the load balancer should be private \(internal\) and only available within the VPC.
* **Value:** boolean, `true` or `false`
* **Default:** `false` \(Load balancer will be publicly available\)
* _Optional_

        Example:

```yaml
albIsPrivate: true
```



### `albAdditionalTimeout`

* **Description:** By default, when an event timeout is specified at function or event level **and** when the plugin is auto-creating the Application Load Balancer, the plugin will add 1 extra second to the load balancer timeout, so we make sure our code can really execute up to the time-limit and the load balancer still accept the response because it has a greater timeout. 
* **Value:** integer, value in **seconds. `10`** will be 10 additional seconds of timeout on the auto-created load balancer from the maximum timeout of the events of the function. 
* **Default:** `1` \(Will add 1 second to the specified or default timeout\)
* _Optional_

        Example:

```yaml
albAdditionalTimeout: 2 #2 seconds
```

{% hint style="danger" %}
You can this value to `0` if you want to disable it. But be aware that time limited executions will fail even on a successfull execution, because the Load balancer will not be able to transmit your request in time.
{% endhint %}



### `vpc`

* **Description:** Indicates if the function events should be contained in a VPC. Two types of VPCs are allowed/accepted, self-managed VPC and plugin-managed VPC.
  *  **Plugin-Managed** **VPC** requires the `cidr` and `subnets` params to be set. VPC will be auto-created by the plugin and shared among all events, ECS-based or Lambda \(serverless\). Currently, there is a limitation on a function with serverless events only, and this configuration is not accepted for this type of VPC. [More here.](https://github.com/Hybridless/hybridless/issues/4)
  * **Self-Managed VPC** required `vpcId` , `securityGroupIds` , `subnetIds` and optionally `albSubnetIds`. All ECS tasks, Lambda functions and Load Balancers will be placed inside the specified VPC.
* **Value:** object. 
  * **Plugin-Managed VPC** Properties
    * `cidr`
    * `subnets`
  * **Self-Managed VPC** Properties
    * `vpcId`
    * `securityGroupIds`
    * `subnetIds`
    * `albSubnetIds` \(optional, overwrites `subnetIds`\)
* **Default:** `null` \(no default configuration\)
* _Optional, **but** **required** for **ECS based event** as `process` , `httpd` and `scheduledTask`._

        Example:

{% tabs %}
{% tab title="Plugin-Managed VPC" %}
```yaml
vpc: 
 cidr: 10.0.0.0/16
 subnets:
   - 10.0.0.0/24
   - 10.0.1.0/24
```
{% endtab %}

{% tab title="Self-Managed VPC" %}
```
vpc:
  vpcId: vpc-1a2b3c4d
  securityGroupIds:
    - sg-51530134
    - sg-51530135
  subnetIds:
    - subnet-0bb1c79de3EXAMPLE
    - subnet-0bb1c79de3EXAMPL2
```
{% endtab %}

{% tab title="Self-Managed VPC \(SSM\)" %}
```
vpc:
  vpcId: ${ssm:/test/VPC_ID}
  securityGroupIds:
    "Fn::Split":
      - ","
      - ${ssm:/test/VPC_PUB_SECGROUPS}
  subnetIds:
    "Fn::Split":
      - ","
      - ${ssm:/test/VPC_PRIV_SUBNETS}
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
**This configuration is required** when any **ECS based event** as `process` , `httpd` and `scheduledTask`is specified on the function.
{% endhint %}



### `vpc.cidr`

* **Description:** Used for **Plugin-Managed VPC** configuration as the primary CIDR of your VPC. Currently, this configuration is not allowed when function events are serverless only. \([\#4](https://github.com/Hybridless/hybridless/issues/4)\)
* **Value:** string. \([CIDR notation](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)\). `10.0.0.0/16`
* **Default:** `null` \(no default configuration\)
* _Optional, but required if specifying`subnets`property. \(Plugin-Managed VPC\)_
* Examples on [`vpc`](cluster-reference.md#vpc) 



### `vpc.subnets`

* **Description:** Used for **Plugin-Managed VPC** configuration as the subnets of your VPC. Currently, this configuration is not allowed when function events are serverless only. \([\#4](https://github.com/Hybridless/hybridless/issues/4)\)
* **Value:** array of strings on CIDR notation. `192.168.0.1/24`
* **Default:** `null` \(no default configuration\)
* _Optional, but required if specifying`cidr`property. \(Plugin-Managed VPC\)_
* Examples on [`vpc`](cluster-reference.md#vpc) 



### `vpc.vpcId`

* **Description:** Used for **Self-Managed VPC** configuration to specify the vpc Id of which vpc the events should be placed in.
* **Value:** vpc Id as a string or object \(intrinsic function\). `vpc-1234567890abcdef0`
* **Default:** `null` \(no default configuration\)
* _Optional, but required if_ using **Self-Managed VPC**_._
* Examples on [`vpc`](cluster-reference.md#vpc) 



### `vpc.securityGroupIds`

* **Description:** Which security groups your tasks and lambdas should use on a **Self-Managed VPC.**
* **Value:** array of security group id strings or any sort of intrinsic function that returns an array of security group ids. `sg-94b3a1f6`
* **Default:** `null` \(no default configuration\)
* _Optional, but required if_ using **Self-Managed VPC**_._
* Examples on [`vpc`](cluster-reference.md#vpc) 



### `vpc.subnetIds`

* **Description:** Which subnets your tasks, lambdas and Load Balancer should be placed in on a **Self-Managed VPC.**
* **Value:** array of subnet id strings or any sort of intrinsic function that returns an array of subnet ids. `subnet-b46032ec`
* **Default:** `null` \(no default configuration\)
* _Optional, but required if_ using **Self-Managed VPC**_._
* Examples on [`vpc`](cluster-reference.md#vpc) 



### `vpc.albSubnetIds`

* **Description:** Which subnets your self created load balancer should be placed in on a **Self-Managed VPC,** takes precende over `subnetIds`.
* **Value:** array of subnet id strings or any sort of intrinsic function that returns an array of subnet ids. `subnet-b46032ec`
* **Default:** `null` \(no default configuration\)
* _Optional_
* Examples on [`vpc`](cluster-reference.md#vpc) 



### `timeout`

* **Description:** Default timeout to be used if no `timeout` is specified on the event level.
* **Value:** Integer, value in seconds. `10`
* **Default:**  `30`
* _Optional_

    Example:

```yaml
timeout: 60
```



### `handler`

* **Description:** Default handler to be used across event if not specified at event level.
* **Value:** String value with the relative path to your file and the function usually on the following notation `src/path/file.handler`
* **Default:**  No default value
* **Required**

    Example:

```yaml
handler: arc/path/file.handler
```



### `memory`

* **Description:** Default memory size your tasks and lambdas functions will have. Event level `memory` property will take precendece over this. Also is using mixed events \(serverless and container\) you might need to specify this configuration per event due size contrains in each event type. 
  * For `lambda` and `lambdaContainer` you should use the [lambda defined memory sizes.](https://docs.aws.amazon.com/lambda/latest/dg/configuration-memory.html)
  * Other type of events, if not placed on EC2, it should respect [fargate memory/cpu constrains](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-cpu-memory-error.html).
* **Value:** Integer, value in MBs. `512`
* **Default:**  Defaults to `1024` MB.  
* _Optional_

    Example:

```yaml
memory: 2048
```



