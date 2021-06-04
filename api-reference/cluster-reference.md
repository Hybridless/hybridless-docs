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
For the lack of simplicity, we are using an inline function definition, but it's highly recommended to **NOT** use this form and use the [import form instead.](plugin-reference.md#functions)
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

* **Description:** When `ecsClusterArn` is specified, you should also specify the ingress security group for that cluster.
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

