# Extending

Built-in runtimes should suffice for a very wide variety of services and tasks you need to run. But there is always an extra case that we need a different behaviour or even a small additional dependency, and being extensible is one of the core principles of hybridless, so allowing customization of the runtimes by extending existing runtimes or even creating brand new ones are **fully supported** and **encouraged** **when needed**.

## Existing Runtimes

Extending or customizing existing runtimes is reasonably easy and the concept applies to all the runtimes that are managed by Hybridless.

There are 3 ivars that control 



## Custom Runtimes

Create custom runtimes is a reasonably easy process and the concept applies to most of the event types and usage types.

There are 4 ivars at the event-level that allow and control the customization of the runtime of that event.

* `runtime` - When using the constant `container`O on the runtime of the supported event types it enabled full customization and no extra imports or hybridless internal logic. 
* `dockerFile` -



