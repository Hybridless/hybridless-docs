# Event Type - httpd



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
```
