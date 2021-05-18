---
description: Hybrid function-driven development framework
---

# Hybridless Framework

* ![npm](https://img.shields.io/npm/dy/@hybridless/hybridless) ![npm](https://img.shields.io/npm/v/@hybridless/hybridless) ![npm \(tag\)](https://img.shields.io/npm/v/@hybridless/hybridless/latest) ![Libraries.io dependency status for latest release, scoped npm package](https://img.shields.io/librariesio/release/npm/@hybridless/hybridless)
* ![GitHub commit activity](http://img.shields.io/github/commit-activity/m/hybridless/hybridless)
* ![GitHub last commit](http://img.shields.io/github/last-commit/hybridless/hybridless)

## Hybrid function-driven development framework

Hybridless is a plug-in on top of serverless that allows you to have a hybrid platform defined on your serverless definition that shares the same code base and has minimal difference between the lambda and long-running functions.

It makes lambda functions and ECS tasks equivalents by managing the runtimes, container packing, deployment and configurations, allowing you to have applications that run on lambdas and ECS containers, within the same code base and without any differences. Additionally, it shortcuts SNS, SQS, dynamic streams and lambda containers so everything is managed in a similar manner and the developer can focus on the code.

Hybridless implements the Concept of runtimes/buildpacks to allow developers to quickly jump into code without worrying about how to deploy the code. All the complex application packing, runtimes, cluster configurations, load balancers and security rules can be handled for you.

## Documentation

Human readable documentation will is available at [hybridless.com](https://docs.hybridless.com)

### General Options Documentation

```javascript
  [serverless.yml content]
  ....
  hybridless: 
    //Misc
    tags: {
      owner: Me
      Customer: You
    };
    disableWebpack?: boolean; //if using javascript, plugin will auto install and compile es6 code using webpack. You can optionally disable this.
    functions: { [key: string]: Function } | { [key: string]: Function }[];
      //--> means:
      - ${file(config/httpfuncs.yml)} //which containes multiple clusters or just one
      - ${file(config/asyncfuncs.yml)}
      // -- OR -- 
    functions: 
      ClusterName:
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
        //
        events?: {
          runtime: string; //Please, check runtime matrix based on the eventTypy you are using. If using custom runtimes use container runtime constant
          eventType: 'httpd', 'process', 'schedulerTask', 'lambda', 'lambdaContainer';
          handler?: string; //this, takes precende over function handler - Usefulll for multi-purpose clusters
          enabled?: boolean; //defaults to true
          memory?: number; //defaults to 1024 - takes precedence over OFunction.memory
          role?: string; //event execution role

          /* Container type specific */
          //Only allowed for httpd, process, schedulerTask and lambdaContainer - Please, if using custom docket file, read the how to customize section
          dockerFile?: string; //relative path to the project
          entrypoint?: string; //in case of using container runtimes, you can always make custom entrypoints
          additionalDockerFiles?: [{ from: string, to: string }?];

          /* Task type specific */
          //Only allowed for httpd, process and schdulerTask
            //Service
          ec2LaunchType?: boolean; //defaults to false, if true will laucnh task into EC2
          newRelicKey?: string;//activates new relic on the supported runtimes
          propagateTags?: 'OFF' | 'SERVICE' | 'TASK' | undefined; //defaults to undefined
          placementConstraints?: { expression: string, type: 'distinctInstance' | 'memberOf' }[];
          placementStrategies?: { field: 'string', type: 'binpack' | 'random' | 'spread' }[];
          capacityProviderStrategy?: { base: number, capacityProvider: string, weight: number }[];
            //Task
          concurrency?: number; //defaults to 1
          cpu?: number; //defaults to 512
          logsMultilinePattern?: string; //defaults to '(([a-zA-Z0-9\-]* \[[a-zA-Za-]*\] )|(\[[a-zA-Za -]*\] ))'
            //EC2 specific (when ec2LaunchType is true)
          daemonType?: boolean; //indicates if we shall we can one task on each instance of the cluster

          /* From here on, some keys might be duplicated, but are kept like that to improve the scope */

          /* httpd specific */
          runtime: 'nodejs10' | 'nodejs13' | 'php5' | 'php7' | 'container'
            //ALB listener layer
          routes?: {
              path: string;
              method?: string;
              priority?: number; //defaults to 1
          }[];
          cors?: {
              origin: string;
              headers: string[];
              allowCredentials: boolean;
          }
          hostname?: string | string[];
          limitSourceIPs?: string | string[];
          limitHeaders?: { name: string, value: string | string[] }[]; //optional limit headers on ALB
          port?: number; // HTTPD port (the port exposed on the container image) - If port is not specified, it will use 80 for non SSL and 443 for SSL
          certificateArns?: any[]; //certificateArn - if present it will use HTTPS
          cognitoAuthorizer?: {
              poolDomain: string;
              poolArn: any;
              clientId: string;
          };
            //health check
          healthCheckInterval?: number; //defaults to 15,
          healthCheckTimeout?: number; //defaults to 10
          healthCheckHealthyCount?: number; //defaults to 2
          healthCheckUnhealthyCount?: number; //defaults to 5
          healthCheckRoute?: string; //default will use auto generated health route
          //AS
          autoScale?: {
              min?: number; //default to 1
              max?: number; //default to 1
              metric: string;
              cooldown?: number; //defaults to 30
              cooldownIn?: number; //defaults to cooldown but has priority over it
              cooldownOut?: number; //defaults to cooldown but has priority over it
              targetValue: number;
          }

          /* process specific */
          /* NO ADDITIONAL PROPS FOR PROCESS */

          /* Scheduler Task specific */
          runtime: 'nodejs10' | 'nodejs13' | 'container';
          schedulerRate: string;
          schedulerInput?: string | object;



          /* Lambda type specific */
          //Only allowed for lambda and lambdaContainer
          reservedConcurrency?: number;
          disableTracing?: boolean; //XRay tracing is enabled by default
          protocol: 'http' ,'httpLoadBalancer' ,'dynamostreams' ,'sqs' ,'sns' ,'scheduler' ,'cloudWatch' ,'cloudWatchLogstream' ,'cognito' ,'s3' ,'none';

          /* Lambda specific */
          layers?: string[];

          /* Lambda container specific */
          runtime?: 'nodejs10' | 'nodejs12' | 'nodejs14' | 'container';

          /* Lambda protocols specific */
            //Protocol: cognito
          cognitoUserPoolArn: any;
          cognitoTrigger: string;
            //Protocol: cloudWatchLogstream
          cloudWatchLogGroup: string;
          cloudWatchLogFilter?: string;
            //Protocol: cloudWatch
          cloudWatchEventSource: string;
          cloudWatchDetailType: string;
          cloudWatchDetailState?: string;
          cloudWatchInput?: string | any;
            //Protocol: s3
          s3bucket: string;
          s3event?: string;
          s3bucketExisting?: boolean;
          s3rules?: { [key in ('prefix'|'suffix')]?: string }[];
            //Protocol: dynamostreams
          prototocolArn?: any; 
            //Protocol: scheduler
          schedulerRate?: string; 
          schedulerInput?: string | any; 
            //Protocol: sns
          prototocolArn?: any; 
          filterPolicy?: object;
            //Protocol: sqs
          prototocolArn?: any; 
          queueBatchSize?: number; 
            //Protocol: http (api gateway)
          routes: {
              path: string;
              method?: string;
          }[];
          cors?: {
              origin: string;
              headers: string[];
              allowCredentials: boolean;
          }
          cognitoAuthorizerArn?: any; //assumption
            //Protocol: httpLoadBalancer
          routes: {
              path: string;
              method?: string | string[];
              priority?: number; //default to 1
          }[];
          cors?: {
              origin: string;
              headers: string[];
              allowCredentials: boolean;
          }
          //ALB
          hostname?: string | string[];
          limitSourceIPs?: string | string[];
          limitHeader?: { name: string, value: string | string[] }; //optional limit headers on ALB
          cognitoAuthorizer?: {
              poolDomain: string;
              poolArn: any;
              clientId: string;
          };
          //health check
          healthCheckInterval?: number; //defaults to 15,
          healthCheckTimeout?: number; //defaults to 10
          healthCheckHealthyCount?: number; //defaults to 2
          healthCheckUnhealthyCount?: number; //defaults to 5
          healthCheckRoute?: string; //required to enable health checks
        }[];

}
```

