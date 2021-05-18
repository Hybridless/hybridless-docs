---
description: Hybrid function-driven development framework
---

# Hybridless Framework

* ![npm](https://img.shields.io/npm/v/@hybridless/hybridless?style=for-the-badge) ![npm \(tag\)](https://img.shields.io/npm/v/@hybridless/hybridless/latest?style=for-the-badge) ![Libraries.io dependency status for latest release, scoped npm package](https://img.shields.io/librariesio/release/npm/@hybridless/hybridless?style=for-the-badge)
* ![npm](https://img.shields.io/npm/dy/@hybridless/hybridless?style=for-the-badge) ![GitHub commit activity](http://img.shields.io/github/commit-activity/m/hybridless/hybridless?style=for-the-badge) ![GitHub last commit](http://img.shields.io/github/last-commit/hybridless/hybridless?style=for-the-badge)
* ![GitHub License](https://img.shields.io/github/license/hybridless/hybridless?style=for-the-badge)
* [![GitHub stars](https://img.shields.io/github/stars/hybridless/hybridless?style=social&label=Star&maxAge=2592000)](https://github.com/hybridless/hybridless/stargazers/) [![GitHub watchers](https://img.shields.io/github/watchers/hybridless/hybridless?style=social&label=Watch&maxAge=2592000)](https://github.com/hybridless/hybridless/watchers/) [Repository Link](https://github.com/hybridless/hybridless)

{% hint style="warning" %}
🚧 This documentation is under initial revision might be inaccurate, wait until 0.1.0 release 🚧
{% endhint %}

## Hybrid function-driven development framework

Hybridless is a plug-in on top of serverless that allows you to have a hybrid platform defined on your serverless definition that shares the same code base and has minimal difference between the lambda and long-running functions.

It makes lambda functions and ECS tasks equivalents by managing the runtimes, container packing, deployment and configurations, allowing you to have applications that run on lambdas and ECS containers, within the same code base and without any differences. Additionally, it shortcuts SNS, SQS, dynamic streams and lambda containers so everything is managed in a similar manner and the developer can focus on the code.

Hybridless implements the Concept of runtimes/buildpacks to allow developers to quickly jump into code without worrying about how to deploy the code. All the complex application packing, runtimes, cluster configurations, load balancers and security rules can be handled for you.



