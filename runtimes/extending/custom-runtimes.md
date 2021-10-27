# Custom Runtimes

Creating **custom runtimes** is a reasonably easy process and the concept applies to most of the event types and usages.

### Event level configuration

There are 4 ivars at the event level that allows and control the customization of the runtime of that event.

* `runtime` - When using the constant `container` on the runtime of the supported event types it enabled full customization and no extra imports or hybridless internal logic.&#x20;
* `dockerFile` - Allows you to specify a custom Dockerfile for each event inside your project directory.&#x20;
* `entrypoint` - Custom image entrypoint in case you are reusing the same base image and are required to modify the entrypoint for that specific function. Do it carefully, especially if using built-in runtimes. Review the runtime Docker image to customize accordily.&#x20;
* `additionalDockerFiles` - Allows mapping of the project or even build-generated files to your docker image build directory.&#x20;

{% hint style="danger" %}
It's important to remember that these mappings are to the **Docker image build directory** so not copying the files from the build directory to the image with `RUN`  command in the **Docker file** will result in these files being discarded and not available at the container.
{% endhint %}

A function with a **custom runtime** event type would be much like this:

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
    - eventType: process
      runtime: container
      dockerFile: /config/CustomDockerFile
      entrypoint: "/bin/abc -d"
      additionalDockerFiles:
        - from: /src
          to: /application
        - from: /config/additionalFilesDir
          to: /includes
        - from: /config/license.json
          to: /license.json
```
{% endcode %}

### Dockerfile

And the Dockerfile you would need to **copy the files specified** on the `additionalDockerFiles` property (event level), decide which base image you will be using and execute your code/binary with the proper flags and parameters coming from `$ENTRYPOINT` or not.

{% code title="/config/CustomDockerFile" %}
```
FROM public.ecr.aws/amazonlinux/amazonlinux:latest

# Copy files
COPY /application /var/task/
COPY /includes /var/lib/
COPY /license.json /bin/abc/config/license.json

# Set the working directory
WORKDIR /var/task

# Run the specified command within the container.
ENTRYPOINT $ENTRYPOINT
```
{% endcode %}

### Environments

Depending on which event type you are trying to customize, there are certain environment variables available at the process execution. For the example we are using, the event type is [`process`](../../api-reference/function-reference/function-type-process.md), so environments available would be the ones [documented on the process runtime](../runtimes-matrix/process-available-runtimes.md#exposed-ivars).&#x20;

### Files and Directories

By default when using runtime `container` no additional files are imported, which is our case. But in case of extending an existing runtime, Hybridless will auto-copy some directories and files to the Docker image build directory, so you can always opt-out and not `COPY` the files in your Dockerfile. This is also [documented on the process runtime](../runtimes-matrix/process-available-runtimes.md#directory-and-files) and on any other event type runtime documentation.
