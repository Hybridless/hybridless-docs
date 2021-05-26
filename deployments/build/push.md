# Push & Deploy

After everything is built successfully Hybridless + Serverless will proceed to push phase in order to have the latest resources, code and images deployed and updated. This phase is where again lots of hard work are leverage for you. 

### Push

First Hybridless will get each images built locally and will push then to the ECR repository of each function. And because **everything** **from now on can fail**, new images are always pushed with unique tags, so even if something fails, existing and deployed functions will no be affected because of the usage of unique tags that are present on the function revisions. \(Some CloudFormation and ECS hidden concepts here\).

{% hint style="success" %}
Hybridless takes care of your ECR repository and will only maintain the latest image. On certain cases, if deployment fails, up to 100 images can be stored before ECR policy are in place. But as soon a successful deployment happens, the tagged images shrink to 1. 🥳 
{% endhint %}

Then, Hybridless and Serverless will transcribe all the required resources defined on the Hybridless, then Serverless and also raw Cloud Formation into one giant Cloud Formation file. This file is where every resource that will be created or updated on the AWS account is defined. Also, it's the key for a smooth and secure deployment because Cloud Formation creates a change set from what needs to be updated and only update that resources. Another key factor for the success of the Cloud Formation on this specific usage, is because the deployments of ECS heavy rely on the Load Balancer health check, so if any of the functions deployed into ECS are failing; Because of the unique tagging technic and the cloud formation ECS service revision we create at every deployment, everything will be reverted to the previous state, including newly created buckets S3 for example.

At the end, Serverless will upload the new CloudFormation template into a S3 bucket \(auto-created by serverless\) in the deployment account and also the required 'artifacts' \(basically lambda code\) that are referenced in the cloud formation.

### Deploy

Serverless will trigger Cloud Formation validate stack to a initial validation of the updated Cloud Formation file generated and pushed on the push phase, to only then and if succeeded trigger a Cloud Formation stack update.

Cloud formation in check what have changed from previous deployment and execute the proper changes. A few important notes on this is that because neither Serverless and Hybridless check your code contents or generates any `shasum` to compare with the previous deployed version, every deployment will issue a new Lambda and ECS task version/revision. 

So in if just code changes happens, or not happens, only ECS tasks/services and Lambda functions will be updated and deployed. But if for instance you have changed the `httpd` function timeout, the Application Load Balancer will also be updated for example.

If any error happens at this stage, [rollback](rollbacks.md) phase will be in.

### Cleanup

After deployment is completed with success, Hybridless will cleanup the ECR repository of each function, to contain only the image being used by the latest deployment.

If this phase is never reach because of deployment failures, ECR will start to accumulate images until 100 failed deployments happens and ECR life-cycle policy takes place by removing the oldest image, or until the next succeeded deployment, which will wipeout everything but the deployed image.



