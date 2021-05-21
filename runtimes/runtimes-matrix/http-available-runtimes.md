# Httpd Runtimes

## Runtimes for [httpd](../../api-reference/function-reference/function-type-httpd.md)

<table>
  <thead>
    <tr>
      <th style="text-align:center"><b>Language</b>
      </th>
      <th style="text-align:center"><b>Version</b>
      </th>
      <th style="text-align:center"><b>Built-In Runtime</b>
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
      <td style="text-align:center">nodejs10</td>
      <td style="text-align:center">
        <ul>
          <li>Uses <a href="https://github.com/Hybridless/runtime-nodejs-httpd">hybridess nodejs runtime</a>
          </li>
          <li>Based on <a href="https://hub.docker.com/_/node?tab=tags&amp;page=1&amp;name=10-alpine">nodejs:10-alpine</a> image</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:center"><b>NodeJS</b>
      </td>
      <td style="text-align:center">13.x</td>
      <td style="text-align:center">nodejs13</td>
      <td style="text-align:center">
        <p></p>
        <ul>
          <li>Uses <a href="https://github.com/Hybridless/runtime-nodejs-httpd">hybridess nodejs runtime</a>
          </li>
          <li>Based on <a href="https://hub.docker.com/_/node?tab=tags&amp;page=1&amp;name=13-alpine">nodejs:13-alpine</a> image</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:center"><b>PHP</b>
      </td>
      <td style="text-align:center">5</td>
      <td style="text-align:center">php5</td>
      <td style="text-align:center">
        <ul>
          <li>Based on <a href="https://hub.docker.com/r/webdevops/php-nginx/tags?page=1&amp;ordering=last_updated&amp;name=alpine-php5">webdevops/php-nginx:alpine-php5</a> image</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:center"><b>PHP</b>
      </td>
      <td style="text-align:center">7</td>
      <td style="text-align:center">php7</td>
      <td style="text-align:center">
        <ul>
          <li>Based on <a href="https://hub.docker.com/r/webdevops/php-nginx/tags?page=1&amp;ordering=last_updated&amp;name=alpine-php7">webdevops/php-nginx:alpine-php7</a> image</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:center"><b>Custom</b>
      </td>
      <td style="text-align:center">-</td>
      <td style="text-align:center">container</td>
      <td style="text-align:center">Can be based on any hybridless httpd runtimes or not.</td>
    </tr>
  </tbody>
</table>

### 

## Runtime images for [httpd](../../api-reference/function-reference/function-type-httpd.md)

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
COPY proxy.js /usr/src/httpd/proxy.js

## CUSTOMIZE YOUR CONTAINER HERE
RUN yum install ffmpeg # :()

# Set the working directory
WORKDIR /usr/src/app

RUN cd /usr/src/httpd/ && npm i -S @hybridless/runtime-nodejs-httpd@latest

EXPOSE $PORT

# Run the specified command within the container.
ENTRYPOINT node ../httpd/proxy.js
```
{% endcode %}

{% code title="proxy.js" %}
```javascript
const Runtime = require("@hybridless/runtime-nodejs-httpd");
const runtimeInstance = new Runtime({
  host: '0.0.0.0',
  port: process.env.PORT,
  timeout: process.env.TIMEOUT,
  cors: (process.env.CORS ? JSON.parse(process.env.CORS) : null),
  function: {
    path: process.env.ENTRYPOINT,
    handler: process.env.ENTRYPOINT_FUNC,
    preload: true
  },
  healthCheckRoute: process.env.HEALTH_ROUTE,
  baseDir: `${__dirname}/../app/`
});
runtimeInstance.start();
```
{% endcode %}

{% hint style="info" %}
If you want to customize your `proxy.js` file or any behaviour of the nodejs httpd runtime, you can check the [runtime repository](https://github.com/Hybridless/runtime-nodejs-httpd) and its [documentation](https://github.com/Hybridless/runtime-nodejs-httpd#hybridless-nodejs-runtime-httpd-).
{% endhint %}
{% endtab %}

{% tab title="Nodejs 13" %}
{% code title="Dockerfile" %}
```
FROM ghcr.io/hybridless/node:13-alpine

# Copy files
COPY /usr/src/app/ /usr/src/app/
COPY proxy.js /usr/src/httpd/proxy.js

## CUSTOMIZE YOUR CONTAINER HERE

# Set the working directory
WORKDIR /usr/src/app

RUN cd /usr/src/httpd/ && npm i -S @hybridless/runtime-nodejs-httpd@latest

EXPOSE $PORT

# Run the specified command within the container.
ENTRYPOINT node ../httpd/proxy.js
```
{% endcode %}

{% code title="proxy.js" %}
```javascript
const Runtime = require("@hybridless/runtime-nodejs-httpd");
const runtimeInstance = new Runtime({
  host: '0.0.0.0',
  port: process.env.PORT,
  timeout: process.env.TIMEOUT,
  cors: (process.env.CORS ? JSON.parse(process.env.CORS) : null),
  function: {
    path: process.env.ENTRYPOINT,
    handler: process.env.ENTRYPOINT_FUNC,
    preload: true
  },
  healthCheckRoute: process.env.HEALTH_ROUTE,
  baseDir: `${__dirname}/../app/`
});
runtimeInstance.start();
```
{% endcode %}

{% hint style="info" %}
If you want to customize your `proxy.js` file or any behaviour of the nodejs httpd runtime, you can check the [runtime repository](https://github.com/Hybridless/runtime-nodejs-httpd) and its [documentation](https://github.com/Hybridless/runtime-nodejs-httpd#hybridless-nodejs-runtime-httpd-).
{% endhint %}
{% endtab %}

{% tab title="PHP5" %}
{% code title="Dockerfile" %}
```text
FROM ghcr.io/hybridless/webdevops/php-nginx:alpine-php5

## CUSTOMIZE YOUR CONTAINER HERE

# customize fastcgi
RUN echo proxy_buffer_size          128k; >> /opt/docker/etc/nginx/vhost.common.d/10-php.conf 
RUN echo proxy_buffers          4 256k; >> /opt/docker/etc/nginx/vhost.common.d/10-php.conf 
RUN echo proxy_busy_buffers_size    256k; >> /opt/docker/etc/nginx/vhost.common.d/10-php.conf 

# Copy files
COPY /app/ /app/

```
{% endcode %}

{% code title="healthCheck.php" %}
```text
<?php echo('Healthy!'); ?>
```
{% endcode %}
{% endtab %}

{% tab title="PHP7" %}
{% code title="Dockerfile" %}
```
FROM ghcr.io/hybridless/webdevops/php-nginx:alpine-php7

## CUSTOMIZE YOUR CONTAINER HERE

# customize fastcgi
RUN echo proxy_buffer_size          128k; >> /opt/docker/etc/nginx/conf.d/10-php.conf
RUN echo proxy_buffers          4 256k; >> /opt/docker/etc/nginx/conf.d/10-php.conf
RUN echo proxy_busy_buffers_size    256k; >> /opt/docker/etc/nginx/conf.d/10-php.conf

# Copy files
COPY /app/ /app/

```
{% endcode %}

{% code title="healthCheck.php" %}
```text
<?php echo('Healthy!'); ?>
```
{% endcode %}
{% endtab %}

{% tab title="Custom" %}
```
FROM ANY_IMAGE

# Copy files -- this directory must be available at additionalDockerFiles prop
COPY /app/ /app/

```
{% endtab %}
{% endtabs %}

### 

## Concept

Httpd runtimes are expected to have a plain HTTP socket opened and listening to the port exposed via`$PORT`environment. It also has some other [ivars exposed](http-available-runtimes.md#exposed-ivars), so your HTTP server can impose some constraints on the server-side and gracefully handle timeouts for example. 

Events will generally come from ALB and will be routed through load balancer listener rules. So most of the routing job is done for you, allowing only routes specified on the routes configuration to go through.



## Additional Details

### Exposed Ivars

Following environment variables are exposed to the process:

* `PORT` - Which port should your listener be listening to.
* `TIMEOUT` - Timeout that should be respected by your request; If behind a load balancer request shall be dropped a second later if you don't timeout by yourself. **In milliseconds, not seconds.**
* `ENTRYPOINT` 
  * `nodejsX`- The specified handler file.
  * `container` - The `entrypoint` property of the event.
* `WEB_DOCUMENT_INDEX` - Only available on `phpX` runtimes, indicates the handler indicated on the function or event level.
* `ENTRYPOINT_FUNC` - The function inside the entrypoint file that should be executed. Only available on `nodejsX` runtimes.
* `HYBRIDLESS_RUNTIME` - Indicates it's running on hybridless runtime and not in lambda for example.
* `CORS` - Optionally, CORS structure from the configuration is exposed if available/specified.
* `HEALTH_ROUTE` - Which route is specified as the health check route so you can make additional logic if you will.
* `STAGE` - Which stage this task is running on.
* `AWS_REGION` - In which AWS region we are.
* `AWS_ACCOUNT_ID` - Which account this task is deployed.
* `NEW_RELIC_ENABLED` - Indicates if NewRelic monitoring framework should be enabled.
* `NEW_RELIC_APP_NAME` - NewRelic application display name.
* `NEW_RELIC_LICENSE_KEY`- NewRelic license key.
* `NEW_RELIC_NO_CONFIG_FILE` - Indicates to NewRelic SDK to use the license key from environment ivars.



### Directory and files

For [httpd](../../api-reference/function-reference/function-type-httpd.md) tasks the following files will be available at the Docker image build directory:

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
        <p></p>
        <p></p>
        <ul>
          <li><code>Builtin healthCheck</code>  <a href="https://github.com/Hybridless/hybridless/blob/master/src/assets/task-httpd/healthCheck.php">php file</a>
          </li>
        </ul>
      </td>
      <td style="text-align:center">./app/{dynamic|specified health route}</td>
      <td style="text-align:center">phpX</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <ul>
          <li>All specified files from the event property <code>additionaDockerFiles</code> will
            be imported after the standard files listed above.</li>
        </ul>
      </td>
      <td style="text-align:center">./{specified destination path}</td>
      <td style="text-align:center">ANY</td>
    </tr>
  </tbody>
</table>

{% hint style="danger" %}
It's important to remember that these mappings are to the **Docker image build directory** so not copying the files from the build directory to the image with `RUN`  command in the **Docker file** will result in these files being discarded and not available at the container.
{% endhint %}

