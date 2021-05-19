# Process Available runtimes

## Runtimes for [process](../../api-reference/function-reference/function-type-process.md)

<table>
  <thead>
    <tr>
      <th style="text-align:center"><b>Language</b>
      </th>
      <th style="text-align:center"><b>Version</b>
      </th>
      <th style="text-align:center"><b>Hybridless Runtime</b>
      </th>
      <th style="text-align:center"><b>Details</b>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:center"><b>NodeJS</b>
      </td>
      <td style="text-align:center">10.x</td>
      <td style="text-align:center">X</td>
      <td style="text-align:center">
        <p></p>
        <ul>
          <li>Based on <a href="https://hub.docker.com/_/node?tab=tags&amp;page=1&amp;name=10-alpine">nodejs:10-alpine</a> image</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:center"><b>NodeJS</b>
      </td>
      <td style="text-align:center">13.x</td>
      <td style="text-align:center">X</td>
      <td style="text-align:center">
        <p></p>
        <ul>
          <li>Based on <a href="https://hub.docker.com/_/node?tab=tags&amp;page=1&amp;name=13-alpine">nodejs:13-alpine</a> image</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:center"><b>Custom</b>
      </td>
      <td style="text-align:center">-</td>
      <td style="text-align:center">-</td>
      <td style="text-align:center">Can be based on any hybridless process runtimes or not.</td>
    </tr>
  </tbody>
</table>

### 

## Runtime images for [process](../../api-reference/function-reference/function-type-process.md)

Hybridless uses the following images for the languages and versions specified above.

{% hint style="info" %}
You are not required to use the following docker files \(`dockerFile` prop of a function event\) if you are using the standard runtimes, however, if you want to customize nodejs10 runtime, for example, to have FFMPEG, you could use the following base nodejs10 image as the base and install what you need, but keeping the same runtime basics from what is automatically done by hybridless. 
{% endhint %}

{% tabs %}
{% tab title="Nodejs 10" %}
{% code title="Dockerfile" %}
```text
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

Process runtimes are basically entrypoints to long-life executables that should only exit when something wrong happens. This process initiated by executing the executable is in the foreground and the monitored process. So forking or spawning new child processes is possible as long the root process is not terminated. 

{% hint style="warning" %}
Terminated processes will generally result in ECS trying to initiate a new task, keeping it on a loop indefinitely.
{% endhint %}



## Additional Details

### Exposed Ivars

Following environment variables are exposed to the process:

* `ENTRYPOINT` - The file relative to the working directory that should be executed. 
* `ENTRYPOINT_FUNC` - The function inside the entrypoint file that should be executed.
* `STAGE` - Which stage this task is running on.
* `AWS_REGION` - In which AWS region we are.
* `AWS_ACCOUNT_ID` - Which account this task is deployed.
* `NEW_RELIC_ENABLED` - Indicates if new relic monitoring framework should be enabled.
* `NEW_RELIC_APP_NAME` - New relic application display name.
* `NEW_RELIC_LICENSE_KEY`- New relic license key.
* `NEW_RELIC_NO_CONFIG_FILE` - Indicates to new relic SDK to use license key from environment ivars.



### Directory and files

For [process](../../api-reference/function-reference/function-type-process.md) tasks the following files will be available at the Docker image build directory:

<table>
  <thead>
    <tr>
      <th style="text-align:left">Source</th>
      <th style="text-align:center">Destination</th>
      <th style="text-align:center">Runtime</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">
        <p>One of the following:</p>
        <ul>
          <li><code>Builtin Runtime</code> Docker File</li>
          <li>Custom Docker File (prop <code>dockerFile</code> of the event)</li>
        </ul>
      </td>
      <td style="text-align:center">./Dockerfile</td>
      <td style="text-align:center">ANY</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p></p>
        <p>If webpack is enabled, first option will be selected.</p>
        <ul>
          <li><code>{projectDir}/.webpack/service</code>
          </li>
          <li><code>{projectDir}/</code>
          </li>
        </ul>
      </td>
      <td style="text-align:center">./usr/src/app</td>
      <td style="text-align:center">nodejsX</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <ul>
          <li>All specified files from the event property<code>additionaDockerFiles</code>will
            be imported after the standard files listed above.</li>
        </ul>
      </td>
      <td style="text-align:center">./{specified destination path}</td>
      <td style="text-align:center">ANY</td>
    </tr>
  </tbody>
</table>



