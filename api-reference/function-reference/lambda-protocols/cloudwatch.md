# cloudWatch



```javascript
          runtime: string; //Please, check runtime matrix based on the eventTypy you are using. If using custom runtimes use container runtime constant
          eventType: 'httpd', 'process', 'schedulerTask', 'lambda', 'lambdaContainer';
          handler?: string; //this, takes precende over function handler - Usefulll for multi-purpose clusters
          enabled?: boolean; //defaults to true
          memory?: number; //defaults to 1024 - takes precedence over OFunction.memory
          role?: string; //event execution role
          
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
            //Protocol: cloudWatch
          cloudWatchEventSource: string;
          cloudWatchDetailType: string;
          cloudWatchDetailState?: string;
          cloudWatchInput?: string | any;
```
