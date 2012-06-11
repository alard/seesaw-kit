Seesaw toolkit
==============

An attempt to write a toolkit for making seesaw scripts in Python, with support for concurrent downloads, uploads etc.

Needs the Tornado library for event-driven I/O.

General idea: a set of `Task`s that can be combined into a `Pipeline` that processes `Item`s:

* An `Item` is a thing that needs to be downloaded (a user, for example). It has properties that are filled by the `Task`s.
* A `Task` is a step in the download process: it takes an item, does something with it and passes it on. Example Tasks: getting an item name from the tracker, running a download script, rsyncing the result, notifying the tracker that it's done.
* A `Pipeline` represents a sequence of `Task`s. To make a seesaw script for a new project you'd specify a new `Pipeline`.

A `Task` can work on multiple `Item`s at a time (e.g., multiple Wget downloads). The concurrency can be limited by wrapping the task in a `LimitConcurrency` `Task`: this will queue the items and run them one-by-one (e.g., a single Rsync upload).

The `Pipeline` needs to be fed empty `Item` objects; by controlling the number of active `Item`s you can limit the number of items. (For example, add a new item each time an item leaves the pipeline.)

The output for each item is recorded to an `OutputCollector`, so instead of writing it to stdout it could also be displayed in, for example, a web interface.

With the `ItemValue`, `ItemInterpolation` and `ConfigValue` classes it is possible to pass item-specific arguments to the `Task` objects. The value of these objects will be re-evaluated for each item. Examples: a path name that depends on the item name, a configurable bandwidth limit, the number of concurrent downloads.

