# Runtimes Matrix

The following event types and Runtimes are supported out of the box. More languages and runtimes are going to be added in the future. If you are missing one, please, let us know by commenting or creating [an issue on GitHub](https://github.com/Hybridless/hybridless/issues) for this runtime request.

## Built-in Runtimes

For each event type indicated on the columns, the following built-in runtimes are available.

| **Runtime** | [httpd](../../api-reference/function-reference/function-type-httpd.md) | [process](../../api-reference/function-reference/function-type-process.md) | [scheduledTask](../../api-reference/function-reference/function-type-scheduledtask.md) | [lambdaContainer](../../api-reference/function-reference/function-type-lambdacontainer.md) | [lambda](../../api-reference/function-reference/function-type-lambda.md) \* |
| ----------- | :--------------------------------------------------------------------: | :------------------------------------------------------------------------: | :------------------------------------------------------------------------------------: | :----------------------------------------------------------------------------------------: | :-------------------------------------------------------------------------: |
| nodejs10    |                                    X                                   |                                      X                                     |                                            X                                           |                                              X                                             |                                      -                                      |
| nodejs12    |                                    -                                   |                                      -                                     |                                            -                                           |                                              X                                             |                                      -                                      |
| nodejs13    |                                    X                                   |                                      X                                     |                                            X                                           |                                              -                                             |                                      -                                      |
| nodejs14    |                                    -                                   |                                      -                                     |                                            -                                           |                                              X                                             |                                      -                                      |
| php5        |                                    X                                   |                                      -                                     |                                            -                                           |                                              -                                             |                                      -                                      |
| php7        |                                    X                                   |                                      -                                     |                                            -                                           |                                              -                                             |                                      -                                      |
| container   |                                    X                                   |                                      X                                     |                                            X                                           |                                              X                                             |                                      -                                      |

&#x20;_\* _Lambda does use AWS internal runtimes. To check the available runtimes, check [here](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html).

## Specificities &#x20;

Each event runtime has its own particularities and is configured tailored for that event type purpose. If you want to **extend, customize or learn more about** a specific event type runtime. Please, check the documentation below.

{% content-ref url="http-available-runtimes.md" %}
[http-available-runtimes.md](http-available-runtimes.md)
{% endcontent-ref %}

{% content-ref url="process-available-runtimes.md" %}
[process-available-runtimes.md](process-available-runtimes.md)
{% endcontent-ref %}

{% content-ref url="scheduledtask-available-runtimes.md" %}
[scheduledtask-available-runtimes.md](scheduledtask-available-runtimes.md)
{% endcontent-ref %}

{% content-ref url="lambdacontainer-available-runtimes.md" %}
[lambdacontainer-available-runtimes.md](lambdacontainer-available-runtimes.md)
{% endcontent-ref %}

{% content-ref url="lambda-available-runtimes.md" %}
[lambda-available-runtimes.md](lambda-available-runtimes.md)
{% endcontent-ref %}

