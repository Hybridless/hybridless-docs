# Extending

Built-in runtimes should suffice for a very wide variety of services and tasks you need to run. But there is always an extra case that we need a different behaviour or even a small additional dependency, and being extensible is one of the core principles of hybridless, so allowing customization of the runtimes by extending existing runtimes or even creating brand new ones are **fully supported** and **encouraged** **when needed**.



## Custom Runtimes

Creating custom runtimes is a reasonably easy process and the concept applies to most of the event types and usages.

### Event level configuration

There are 4 ivars at the event level that allows and control the customization of the runtime of that event.

* `runtime` - When using the constant `container`O on the runtime of the supported event types it enabled full customization and no extra imports or hybridless internal logic. 
* `dockerFile` - Allows you to specify a custom Docker file for each event inside your project directory. 
* `entrypoint` - Custom image entrypoint in case you are reusing the same base image and are required to modify the entrypoint for that specific function. Do it carefully, especially if using built-in runtimes. Review the runtime Docker image to customize accordily. 
* `additionalDockerFiles` - Allows mapping of the project or even build-generated files to your docker image build directory. 

{% hint style="danger" %}
It's important to remember that these mappings are to the **Docker image build directory** so not copying the files from the build directory to the image with `RUN`  command in the **Docker file** will result in these files being discarded and not available at the container.
{% endhint %}

A function with a custom runtime event type would be much like this:

{% code title="serverless.yml" %}
```text
.....
ProcessABC:
  handler: src/handler.handler
  memory: 512
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

And the Docker file would need to **copy the files and specified** on the `additionalDockerFiles` property and may want to include to your image and execute your code/binary with the proper flags and parameters.

```text
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

### Environments

Depending on which event type you are trying to customize, there are certain environment variables available at the process execution. For the example we are using, the event type is [`process`](../api-reference/function-reference/function-type-process.md), so environments available would be the ones [documented on the process runtime](runtimes-matrix/process-available-runtimes.md#exposed-ivars). 

### Files and Directories

By default when using runtime `container` no additional files are imported, which is our case. But in case of extending an existing runtime, Hybridless will auto-copy some directories and files to the Docker image build directory. This is also [documented on the process runtime](runtimes-matrix/process-available-runtimes.md#directory-and-files) and on any other event type runtime documentation.



## Existing Runtimes

We reviewed the concept of custom runtimes by having the most complex perspective because **extending built-in Hybridless runtimes** is totally the same except you are **given a template** that should be followed and extended for further customization.

You should always rely on the latest documentation of the event type runtime and **pay attention** to the exported environment variables and default directory and files.

In this simple example, we are going to extend an [`httpd`](../api-reference/function-reference/function-type-httpd.md) event type to add `FFMPEG` and also  

## Extending vs. Custom Runtimes

Extending and creating custom can be sometimes mixed but in fact, they have a very clear definition and purpose.

You should extend a built-in runtime when the default behaviour of the runtime fits your needs but you have required additional dependencies or any customization that does not impact the default behaviour.

Creating new runtimes are tailored for unique behaviours as running an internal Redis service on your VPC or crazily fitting a WordPress with Nginx and also adding support for new languages.

If you created a cool runtime or have added support for a new language, we will be more than happy in receiving a pull request from you on GitHub.

