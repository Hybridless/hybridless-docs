# Http Available runtimes

### Runtimes for httpd

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
      <td style="text-align:center">X</td>
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
      <td style="text-align:center">X</td>
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
      <td style="text-align:center">X</td>
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
      <td style="text-align:center">-</td>
      <td style="text-align:center">Can be based on any hybridless httpd runtime</td>
    </tr>
  </tbody>
</table>

### Runtime images for httpd

Hybridless uses the following images for the languages and versions specified above.

{% tabs %}
{% tab title="Nodejs 10.x" %}
{% code title="Dockerfile" %}
```text
FROM ghcr.io/hybridless/node:10-alpine

# Copy files
COPY /usr/src/app/ /usr/src/app/
COPY proxy.js /usr/src/httpd/proxy.js

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
{% endtab %}

{% tab title="Nodejs 13.x" %}
{% code title="Dockerfile" %}
```
FROM ghcr.io/hybridless/node:13-alpine

# Copy files
COPY /usr/src/app/ /usr/src/app/
COPY proxy.js /usr/src/httpd/proxy.js

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
{% endtab %}

{% tab title="PHP5" %}
```text
FROM ghcr.io/hybridless/webdevops/php-nginx:alpine-php5

# customize fastcgi
RUN echo proxy_buffer_size          128k; >> /opt/docker/etc/nginx/vhost.common.d/10-php.conf 
RUN echo proxy_buffers          4 256k; >> /opt/docker/etc/nginx/vhost.common.d/10-php.conf 
RUN echo proxy_busy_buffers_size    256k; >> /opt/docker/etc/nginx/vhost.common.d/10-php.conf 

# Copy files
COPY /app/ /app/

```
{% endtab %}

{% tab title="PHP7" %}
```
FROM ghcr.io/hybridless/webdevops/php-nginx:alpine-php7

# customize fastcgi
RUN echo proxy_buffer_size          128k; >> /opt/docker/etc/nginx/conf.d/10-php.conf
RUN echo proxy_buffers          4 256k; >> /opt/docker/etc/nginx/conf.d/10-php.conf
RUN echo proxy_busy_buffers_size    256k; >> /opt/docker/etc/nginx/conf.d/10-php.conf

# Copy files
COPY /app/ /app/

```
{% endtab %}

{% tab title="Custom" %}
```
FROM ANY_IMAGE

# Copy files -- this directory must be available at additionalDockerFiles prop
COPY /app/ /app/

```
{% endtab %}
{% endtabs %}

