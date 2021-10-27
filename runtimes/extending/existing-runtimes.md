# Existing Runtimes

We reviewed the concept of custom runtimes by having the most complex perspective because **extending built-in Hybridless runtimes** is totally the same except you are **given a template** that should be followed and extended for further customization.

You should always rely on the latest documentation of the event type runtime and **pay attention** to the exported environment variables and default directory and files.&#x20;

In this simple example, we are going to extend an [`httpd`](../../api-reference/function-reference/function-type-httpd.md) event type to add `git` and also `ImageMagick` because for some reason our function needs both to execute a certain route business logic.

### Event level configuration

As on the custom runtimes, there are 3 ivars at the event level that allows and control the extension of the runtime of that event. There is fourth ivar, but, usually for extensions, we are not touching on the `entrypoint` property at event level, however, there is always a use case for everything, so free to use if you have confidence on what you doing.

* `runtime` - If you are extending an existing runtime, this means **you are not** using the constant `container` , but yes the event type [supported runtimes](../runtimes-matrix/http-available-runtimes.md#runtimes-for-httpd).&#x20;
* `dockerFile` - Allows you to specify a custom Dockerfile for each event inside your project directory. In our case, we are using the runtime base image provided [here](../runtimes-matrix/http-available-runtimes.md#runtime-images-for-httpd). Theoretically you could also modify the base image as you will but maintaining compatibility to the runtime requirements.
* `additionalDockerFiles` - Allows mapping of the project or even build-generated files to your docker image build directory.&#x20;

{% hint style="danger" %}
It's important to remember that these mappings are to the **Docker image build directory** so not copying the files from the build directory to the image with `RUN`  command in the **Docker file** will result in these files being discarded and not available at the container.
{% endhint %}

Defining the hybridless function and the extended event would be very similar from normal [`httpd`](../../api-reference/function-reference/function-type-httpd.md) definitions but with additional properties and files as stated below.

{% code title="serverless.yml" %}
```
.....
ProcessABC:
  handler: src/handler.handler
  memory: 512
  vpc:
    cidr: 10.0.0.0/16
    subnets:
      - 10.0.0.0/24
      - 10.0.1.0/24
  events:
    - eventType: httpd
      runtime: nodejs13
      dockerFile: /config/CustomDockerFile
      additionalDockerFiles:
        - from: /config/license.json
          to: /license.json
      routes:
        - method: ANY
          path: "*"
```
{% endcode %}

### Dockerfile

For the Dockerfile, when extending a **specific event-type runtime** you should always rely on the latest copy of the Dockerfile for that runtime. We are using the `nodejs13` runtime of the [`httpd`](../../api-reference/function-reference/function-type-httpd.md) event, which is fully documented [here](../runtimes-matrix/http-available-runtimes.md#runtime-images-for-httpd). Using the provided template, we are going to add both of the required dependencies `ImageMagick` and `git` via **yum** and also copy an additional example file demonstrating how to copy additional files to your image. _(You could also be doing that by webpack copy plugin, if the file is located at the code structure)._

{% code title="/config/CustomDockerFile" %}
```
FROM ghcr.io/hybridless/node:13-alpine

# Copy files
COPY /usr/src/app/ /usr/src/app/
COPY proxy.js /usr/src/httpd/proxy.js
COPY /license.json /bin/abc/config/license.json

## CUSTOMIZATION IS HERE
RUN yum install -y ImageMagick git

# Set the working directory
WORKDIR /usr/src/app

# Install nodejs runtime package
RUN cd /usr/src/httpd/ && npm i -S @hybridless/runtime-nodejs-httpd@latest

EXPOSE $PORT

# Run the specified command within the container.
ENTRYPOINT node ../httpd/proxy.js
```
{% endcode %}

### Environments

Depending on which event type you are trying to customize, there are certain environment variables available at the process execution. For the example we are using, the event type is [`httpd`](../../api-reference/function-reference/function-type-httpd.md), so environments available would be the ones [documented on the httpd runtime](../runtimes-matrix/http-available-runtimes.md#exposed-ivars).&#x20;

### Files and Directories

By default when using any existing runtime, additional files may be already imported into docker image build directory. In our example here, [`httpd`](../../api-reference/function-reference/function-type-httpd.md) event type with `nodejsX` runtimes does [have default imports](../runtimes-matrix/http-available-runtimes.md#directory-and-files), so be sure to check them if you are planning to extend any runtime.&#x20;
