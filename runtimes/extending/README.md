# Extending

Built-in runtimes should suffice for a very wide variety of services and tasks you need to run. But there is always an extra case that we need a different behaviour or even a small additional dependency, and being extensible is one of the core principles of hybridless, so allowing customization of the runtimes by extending existing runtimes or even creating brand new ones are **fully supported** and **encouraged** **when needed**.

## Extending vs. Custom Runtimes

Extending existing runtimes and creating custom can be sometimes mixed but in fact, they have a very clear definition and purpose.

You should extend a built-in runtime when the default behaviour of the runtime fits your needs but you have required additional dependencies or any customization that does not impact the default behaviour.

Creating new runtimes are tailored for unique behaviours as running an internal Redis service on your VPC or crazily fitting a WordPress with Nginx and also adding support for new languages.

If you created a cool runtime or have added support for a new language, we will be more than happy in receiving a pull request from you on GitHub.

{% page-ref page="custom-runtimes.md" %}

{% page-ref page="existing-runtimes.md" %}



