# Registry Management

For each function event defined on Hybridless an ECR repository is created and auto managed for you. Meaning that images will be pushed, tagged and purged automatically, requiring no human interaction with it.

For each deployment, it will create a new image that is uniquely tagged and pushed to that function event repository. Tagging the images uniquely gives Cloud Formation the ability to rollback to a previous ECS service revision based on that previous tag link, so the usage of `latest` tag has being dropped since the early version of Hybridless in favor to this technic.&#x20;

If you plan or wants to keeps images on the ECR repository and don't want Hybridless to touch it, you should untag that specific image so it won't be wiped.

As an additional constraint, Hybridless will also attach a ECR image life-policy to ask ECR to always keeps the last 100 tagged images. So in case lots of deployment failures happens we will keep that latest deployed version until a successful deployment occur. This is done as a preventive management, and in theory should rarely happen.&#x20;

