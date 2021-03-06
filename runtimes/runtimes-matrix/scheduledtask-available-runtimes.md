# ScheduledTask Runtimes

## Runtimes for [scheduledTask](../../api-reference/function-reference/function-type-scheduledtask.md)

| **Language** | **Version** | **Built-In Runtime** |                                                                  **Details**                                                                  |
| :----------: | :---------: | :------------------: | :-------------------------------------------------------------------------------------------------------------------------------------------: |
|  **NodeJS**  |     10.x    |       nodejs10       | <p></p><ul><li>Based on <a href="https://hub.docker.com/_/node?tab=tags&#x26;page=1&#x26;name=10-alpine">nodejs:10-alpine</a> image</li></ul> |
|  **NodeJS**  |     13.x    |       nodejs13       | <p></p><ul><li>Based on <a href="https://hub.docker.com/_/node?tab=tags&#x26;page=1&#x26;name=13-alpine">nodejs:13-alpine</a> image</li></ul> |
|  **Custom**  |      -      |       container      |                                         Can be based on any hybridless scheduledTask runtimes or not.                                         |

###

## Runtime images for [scheduledTask](../../api-reference/function-reference/function-type-scheduledtask.md)

Hybridless uses the following images for the languages and versions specified above.

{% hint style="info" %}
You are not required to use the following docker files (`dockerFile` prop of a function event) if you are using the standard runtimes, however, if you want to customize nodejs10 runtime, for example, to have FFMPEG, you could use the following base nodejs10 image as the base and install what you need, but keeping the same runtime basics from what is automatically done by hybridless.&#x20;
{% endhint %}

{% tabs %}
{% tab title="Nodejs 10" %}
{% code title="Dockerfile" %}
```
FROM ghcr.io/hybridless/node:10-alpine

# Copy files
COPY /usr/src/app/ /usr/src/app/

# Set the working directory
WORKDIR /usr/src/app

# Run the specified command within the container.
ENTRYPOINT node -e "require('./$ENTRYPOINT').$ENTRYPOINT_FUNC()"
```
{% endcode %}
{% endtab %}

{% tab title="Nodejs 13" %}
{% code title="Dockerfile" %}
```
FROM ghcr.io/hybridless/node:13-alpine

# Copy files
COPY /usr/src/app/ /usr/src/app/

# Set the working directory
WORKDIR /usr/src/app

# Run the specified command within the container.
ENTRYPOINT node -e "require('./$ENTRYPOINT').$ENTRYPOINT_FUNC()"
```
{% endcode %}
{% endtab %}

{% tab title="Custom" %}
```
FROM ANY_IMAGE

# Copy files
COPY /usr/src/app/ /usr/src/app/

# Set the working directory
WORKDIR /usr/src/app

# Run the specified command within the container.
ENTRYPOINT EXEC -e $ENTRYPOINT -e $ENTRYPOINT_FUNC
```
{% endtab %}
{% endtabs %}



## Concept

Scheduled task runtimes are basically entrypoints to short-life executables that should execute a determined task at a specified scheduled frequency and exit after not additional work is required.

This task is auto-initiated the configured scheduled frequency (`once a day` for example) and have an undetermined execution time, allowing this task to run for minutes, days or even weeks).

{% hint style="warning" %}
Termination of this process will not initiate another task by the ECS service as the process runtimes. Your application code is responsible to detect failures from previously scheduled executions and resume the required/missed work.
{% endhint %}



## Additional Details

### Exposed Ivars

Following environment variables are exposed to the process:

* `ENTRYPOINT` - The file relative to the working directory that should be executed.&#x20;
* `ENTRYPOINT_FUNC` - The function inside the entrypoint file that should be executed.
* `STAGE` - Which stage this task is running on.
* `AWS_REGION` - In which AWS region we are.
* `AWS_ACCOUNT_ID` - Which account this task is deployed.
* `NEW_RELIC_ENABLED` - Indicates if new relic monitoring framework should be enabled.
* `NEW_RELIC_APP_NAME` - New relic application display name.
* `NEW_RELIC_LICENSE_KEY`- New relic license key.
* `NEW_RELIC_NO_CONFIG_FILE` - Indicates to new relic SDK to use license key from environment ivars.

###

### Directory and files

For [scheduledTask](../../api-reference/function-reference/function-type-scheduledtask.md) tasks the following files will be available at the Docker image build directory:

| Source                                                                                                                                                             |           Destination          | Runtime |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :----------------------------: | :-----: |
| <p>One of the following:</p><ul><li><code>Builtin Runtime</code> Docker File</li><li>Custom Docker File (prop <code>dockerFile</code> of the event)</li></ul>      |          ./Dockerfile          |   ANY   |
| <p></p><p>If webpack is enabled, first option will be selected.</p><ul><li><code>{projectDir}/.webpack/service</code></li><li><code>{projectDir}/</code></li></ul> |          ./usr/src/app         | nodejsX |
| <ul><li>All specified files from the event property<code>additionaDockerFiles</code>will be imported after the standard files listed above.</li></ul>              | ./{specified destination path} |   ANY   |

{% hint style="danger" %}
It's important to remember that these mappings are to the **Docker image build directory** so not copying the files from the build directory to the image with `RUN`  command in the **Docker file** will result in these files being discarded and not available at the container.
{% endhint %}
