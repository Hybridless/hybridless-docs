# Rollbacks

Rollbacks take in place when everything have being pushed to AWS already and Cloud Formation is updating the resources, but something is misconfigured or not working properly by the CF point of view.&#x20;

This can happens for an enormous variety of problems and usually the stack update error printed after rollback is succeed will help on mitigating the issue for the next deployment.

Rollback from Cloud Formation is usually initiated as soon as a resource update fails, so if the failed resource is the first resource being updated, only that will be required to be reverted to its initial state. If it's the last, everything that have been updated needs to be reverted.

Luckily, because everything pushed from Hybridless and Serverless is properly tagged and configured, Cloud formation handles all the rollback logic for us, so all lambda functions are reverted without any hassle and also all the ECS tasks are reverted to it's previous configuration and image.&#x20;

As a big plus, we also have the ability to have all of that within the other Cloud resources defined on our project as S3 buckets, SQS, SNS topics and so on. So if for example we have changed a configuration on a S3 bucket that caused an ECS task health check to fail, everything, including the S3 will be rolled back to the previous working configuration automatically!

