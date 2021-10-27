# LambdaContainer Runtimes



## Runtimes for [lambdaContainer](../../api-reference/function-reference/function-type-lambdacontainer.md)

| **Language** | **Version** | **Built-In Runtime** |                                                                                            **Details**                                                                                           |
| :----------: | :---------: | :------------------: | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|  **NodeJS**  |     10.x    |       nodejs10       |                                          <p></p><ul><li>Based on <a href="https://gallery.ecr.aws/lambda/nodejs:10">lambda/nodejs:10</a> image</li></ul>                                         |
|  **NodeJS**  |     12.x    |       nodejs12       |                                          <p></p><ul><li>Based on <a href="https://gallery.ecr.aws/lambda/nodejs:12">lambda/nodejs:12</a> image</li></ul>                                         |
|  **NodeJS**  |     14.x    |       nodejs14       |                                             <ul><li>Based on <a href="https://gallery.ecr.aws/lambda/nodejs:14">lambda/nodejs:14</a> image</li></ul>                                             |
|  **Custom**  |      -      |       container      | <p>Needs to be based on <strong>lambda/* runtimes</strong> </p><p>(Any image from <strong>AWS Lambda </strong><a href="https://gallery.ecr.aws/?page=1&#x26;searchTerm=AWS+Lambda">here</a>)</p> |

###

## Runtime images for [lambdaContainer](../../api-reference/function-reference/function-type-lambdacontainer.md)

Hybridless uses the following images for the languages and versions specified above.

{% hint style="info" %}
You are not required to use the following docker files (`dockerFile` prop of a function event) if you are using the standard runtimes, however, if you want to customize nodejs10 runtime, for example, to have FFMPEG, you could use the following base nodejs10 image as the base and install what you need, but keeping the same runtime basics from what is automatically done by hybridless.&#x20;
{% endhint %}

{% tabs %}
{% tab title="Nodejs 10" %}
{% code title="Dockerfile" %}
```
FROM public.ecr.aws/lambda/nodejs:10

# Copy function code
COPY /usr/src/app/ ${LAMBDA_TASK_ROOT}

# 
ENTRYPOINT /lambda-entrypoint.sh "$ENTRYPOINT.$ENTRYPOINT_FUNC"
```
{% endcode %}
{% endtab %}

{% tab title="Nodejs 12" %}
{% code title="Dockerfile" %}
```
FROM public.ecr.aws/lambda/nodejs:12

# Copy function code
COPY /usr/src/app/ ${LAMBDA_TASK_ROOT}

# 
ENTRYPOINT /lambda-entrypoint.sh "$ENTRYPOINT.$ENTRYPOINT_FUNC"
```
{% endcode %}
{% endtab %}

{% tab title="Nodejs 14" %}
{% code title="Dockerfile" %}
```
FROM public.ecr.aws/lambda/nodejs:14

# Copy function code
COPY /usr/src/app/ ${LAMBDA_TASK_ROOT}

# 
ENTRYPOINT /lambda-entrypoint.sh "$ENTRYPOINT.$ENTRYPOINT_FUNC"
```
{% endcode %}
{% endtab %}

{% tab title="Custom" %}
```
FROM public.ecr.aws/lambda/??:??
# Look for images from AWS Lambda at https://gallery.ecr.aws/?page=1&searchTerm=AWS+Lambda

# Copy function code
COPY /usr/src/app/ ${LAMBDA_TASK_ROOT}

# 
ENTRYPOINT /lambda-entrypoint.sh "$ENTRYPOINT.$ENTRYPOINT_FUNC"

```
{% endtab %}
{% endtabs %}



## Concept

Lambda container runtimes are images built with lambda base images as extended for further dependencies instalments or modifications. This runtimes in specifically can't have their different OS are commonly constrained to AWS Linux 2.

Lambdas are short-life executables, with maximum execution time of 15 minutes and should exit gracefully or forced by the Lambda 'supervisor'. Despite the fact the Lambda is complete serverless, we are having a containerized serverless functions running on AWS and paying only the time of the execution. Interesting, hm?

It gets way way more interesting when we list the variety of event sources this lambda container event can have, here on Hybridless known as [Lambda protocols](../../api-reference/function-reference/lambda-protocols/).

One limitation that comes to me is that even this behaves and is built as a container, internally this is not executed as the docker engine we are used to, so things might behave differently. A quick example that comes to my mind, is the execution user of Lambda containers; They are not the normal root users we would see in a alpine based images execution, it's a weird user `sbx_user1051` that does not have full execution rights to the container volume (if can name that). If you want to know and learn a little more about lambda environment itself I suggest [this reading](https://www.denialof.services/lambda/). So if you are trying to execute or install a new dependency (manually), make sure to give wheel `495` access to it.



## Additional Details

### Exposed Ivars

Following environment variables are exposed to the process:

* `ENTRYPOINT` - The file relative to the working directory that should be executed.&#x20;
* `ENTRYPOINT_FUNC` - The function inside the entrypoint file that should be executed.
* `STAGE` - Which stage this task is running on.
* `AWS_REGION` - In which AWS region we are.
* `AWS_ACCOUNT_ID` - Which account this task is deployed.

###

### Directory and files

For [lambdaContainer](../../api-reference/function-reference/function-type-lambdacontainer.md) events the following files will be available at the Docker image build directory:

| Source                                                                                                                                                             |           Destination          | Runtime |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :----------------------------: | :-----: |
| <p>One of the following:</p><ul><li><code>Builtin Runtime</code> Docker File</li><li>Custom Docker File (prop <code>dockerFile</code> of the event)</li></ul>      |          ./Dockerfile          |   ANY   |
| <p></p><p>If webpack is enabled, first option will be selected.</p><ul><li><code>{projectDir}/.webpack/service</code></li><li><code>{projectDir}/</code></li></ul> |          ./usr/src/app         | nodejsX |
| <ul><li>All specified files from the event property<code>additionaDockerFiles</code>will be imported after the standard files listed above.</li></ul>              | ./{specified destination path} |   ANY   |

{% hint style="danger" %}
It's important to remember that these mappings are to the **Docker image build directory** so not copying the files from the build directory to the image with `RUN`  command in the **Docker file** will result in these files being discarded and not available at the container.
{% endhint %}
