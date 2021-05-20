# Runtimes In-Depth

Runtimes are basically a set of instructions on how to build and run your code \(or the packaged application\) on a specific container. It's mainly composed by a Dockerfile that builds an image that will follow that specific event type rules. 

Further more, programming languages runtimes have additional logic to invoke certain files and specific functions by generally simulating lambda environment or long-life tasks, so development can be done agnostically of where you will actually run your code.

It's a cloned concept from AWS [lambda runtimes](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html) and also a well know concept from PAAS companies as Heroku [buildpacks](https://devcenter.heroku.com/articles/buildpacks), but way more powerful and customizable, because it allows you to use any operating system by using any base docker image available out there, and installing, building and managing what will be deployed in almost any matter.

This whole concept is one of the key components of Hybridless and also one of the most appealing features, because it aggregates lots of builds steps that are being done for the majority of companies and developers out there in a simple concept that can can be used by beginners to experts and be customized without much limitations, other than the ones imposed by the cloud providers.

Also, as today, the plugin is only available and compatible with AWS, but there are no barriers to transverse this concept between other provider on the future.

### 

### Runtime Lifecycle

As a good explanatory example, bellow is described the **key runtime steps** on the **packing**, **building**, and others til the whole execution of a  [`httpd`](../api-reference/function-reference/function-type-httpd.md) **event type** using a `nodejs13` **runtime** is completed. 

* Hybridless detects this event requires a Docker repository to host the final image. ECR repository is auto created if not existing.
* Hybridless detects you are using `nodejs` runtime, enables webpack/babel and **compile your code** if webpack option is not disabled.
* Docker **image build directory** is **prepared** by:
  * **Copying** the built-in **Dockerfile** or specified custom Dockerfile.
  * **Copying** the **application code** \(compile or not\).
  * Optionally, specified additional docker files will be copied last.
* **Docker Image** is **built** and tagged locally. 
  * Base image is official `nodejs-13-alpine` from [Hybridless registry](hybridless-registry.md).
  * **Files** listed above are **copied** to the image.
  * **Hybridless lambda nodejs runtime** is **installed**.
  * Entrypoint is created pointing to the lambda runtime, but specifying the actual file and function that needs to be executed.
* **Deployment** happens \(This is very well explained on [Deployment section](../deployments/build/)\)
* **Task** is **initiated**.
  * Hybridless lambda nodejs runtime is executed and open a port on the specified port.
* **Requests comes** from internet.
  * Load balancer routes to the task.
  * Hybridless lambda nodejs runtime executes the specified file and function and return the response.

You can probably notice, this is nothing different than what we have been seen in the last 10 years but everything layered into customizable layers to improve reusability, reduce the complexity and cover a wide variety of applications and services. But also keeping it simple and with built-in types for the most common usages.

{% hint style="warning" %}
Note to the **difference between** a **Hybridless Runtime** \(this whole section is about\) and what we have called on the example above as `Hybridless lambda nodejs runtime`, which is a nodejs package that opens a http socket to listen for incoming requests and executes you function with the same context as Lambda would do.
{% endhint %}

### 

### Runtimes Registry

For now, there are limited number of built-in runtimes, and more and more are being added each week. However Hybridless will reserve built-in runtimes for the most common usages and undeniable plans for Runtimes registry are very plausible and might be right under the corner. 🤔 

