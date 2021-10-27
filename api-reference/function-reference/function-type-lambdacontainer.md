# Function Type - lambdaContainer



```javascript
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
          protocolArn?: any; 
          protocolArn?: any; 
            //Protocol: scheduler
          schedulerRate?: string; 
          schedulerInput?: string | any; 
            //Protocol: sns
          protocolArn?: any; 
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
```
