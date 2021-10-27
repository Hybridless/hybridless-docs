# Build

Build is one of the most important steps of the deployment flow, because we are not just compiling your code, we are doing that but also packing the Runtime and all the additional dependencies into a executable docker image that will be deployed to AWS ECR, and to later be launched and follow the event type core rules.

Bellow is described in detail each step that happens on the build process, before having the actual Docker image that will be responsible for that function in that event-type.

As an optional first step, Hybridless will check if any ECR repository for this specific function is already created, if not, create one on the deployment account in the default provider region.

### Code Compile

Code compilation is only needed and supported for Javascript objects that have `webpack` enabled.

You can customize you webpack config as needed and have Typescript enabled for example or just have ES6 code compiled through babel.&#x20;

### Runtime Packing

Before building the function specific image, all resources needed for that to work are gathered and copied to a tmp folder.&#x20;

* Dockerfile (can be a custom (user provided) or Hybridless built-in image)
* Compile or Raw Code
* Internal Hybridless runtime files (Healthcheck.php, Runtime initialize code)
* Additional files (user defined)

### Image Build

Standard Docker image build process happens from the defined Dockerfile and image is maintained at local registry until is pushed. Files from the `tmp` build folder are wiped out and if not copied to the image view `COPY` statement, they will not be present on the image.

{% hint style="warning" %}
f you are **not** using containerized builds, make sure to wipeout the images from the local registry after building.
{% endhint %}
