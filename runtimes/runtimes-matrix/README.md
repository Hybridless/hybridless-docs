# Runtimes Matrix

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



