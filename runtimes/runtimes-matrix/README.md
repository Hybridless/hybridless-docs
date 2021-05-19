# Runtimes Matrix

The following event types and Runtimes are supported out of the box. More languages and runtimes are going to be added in the future. If you are missing one, please, let us know by commenting or creating [an issue on GitHub](https://github.com/Hybridless/hybridless/issues) for this runtime request.

| **Runtime** | [httpd](../../api-reference/function-reference/function-type-httpd.md) | [process](../../api-reference/function-reference/function-type-process.md) | [scheduledTask](../../api-reference/function-reference/function-type-scheduledtask.md) | [lambdaContainer](../../api-reference/function-reference/function-type-lambdacontainer.md) | [lambda](../../api-reference/function-reference/function-type-lambda.md) \* |
| :--- | :---: | :---: | :---: | :---: | :---: |
| nodejs10 | X | X | X | X | - |
| nodejs12 | - | - | - | X | - |
| nodejs13 | X | X | X | - | - |
| nodejs14 | - | - | - | X | - |
| php5 | X | - | - | - | - |
| php7 | X | - | - | - | - |
| container | X | X | X | X | - |

 _\*_ Lambda does use AWS internal runtimes. To check the available runtimes, check [here](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html).

### 

