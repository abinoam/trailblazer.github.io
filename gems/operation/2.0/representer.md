---
layout: operation2
title: Operation Representer
gems:
  - ["operation", "trailblazer/trailblazer-operation", "2.0", "1.1"]
---

Representers help to parse and render documents for JSON or XML APIs.

After defining a representer, it can either parse an incoming document into an object graph, or, the other way round, serialize a nested object into a document.

You can use representers from the [Representable](/gems/representable) gem with your operations.

## Parsing

In Trailblazer, incoming is usually deserialized (parsed) into an object graph, then validated, then persisted. Transforming the data into one or many objects is done by a representer.

Normally, this happens internally in the Reform object, which receives the data in `validate` and then uses an automatically infered representer to parse the data onto itself.

<!-- Basically, this happens. -->

TODO: more explanations about generic parsing.

NOTE: parsing needs Representable >= 3.0.2.

## Parsing: Explicit

In the `Contract::Validate` macro, you can specify a representer used to deserialize the incoming document onto the contract.

This requires a representer class.

{{  "representer_test.rb:explicit-rep" | tsnippet }}

While this representer could be used stand-alone, the operation helps you to leverage it for parsing.

{{  "representer_test.rb:explicit-op" | tsnippet }}

Note that the contract can also be an inline contract.

You may now pass a JSON document instead of a hash into the operation's call.

{{  "representer_test.rb:explicit-call" | tsnippet }}

This parses the JSON document and the representer will assign the property values and objects to the contract. Afterwards, the contract validates itself with the normal mechanics.

In fact, the contract doesn't even know its data was parsed from a JSON or XML document.

## Parsing: Inline Representer

If you quickly want to try a representer or you're facing a small amount of properties, only, you can use an inline representer.

{{  "representer_test.rb:inline" | tsnippet }}

The behavior is identical to referencing the representer class constant.

## Parsing: Infer

A representer can also be infered from the contract's schema. All you need to do is define the format, e.g. `Representable::JSON`.

{{  "representer_test.rb:infer" | tsnippet }}

The `Operation::Representer.infer` method will return a representer class.

## Parsing: Dependency Injection

You can override the parsing representer when calling the operation with dependency injection. This allows things like exchanging the representer to parse other document formats, such as XML.

{{  "representer_test.rb:di-rep" | tsnippet }}

The representer can be injected using Trailblazer's well-defined injection interface.

{{  "representer_test.rb:di-call" | tsnippet }}

Note how the XML representer replaces the built-in JSON representer and can parse the XML document to the contract. The latter doesn't know anything about the swapped documents.

## Naming

Without a name specified, the representer will be named `default`.


{{  "representer_test.rb:naming" | tsnippet }}

To maintain multiple representers per operation, you may name them.

    representer :parse, MyRepresenter
    representer :errors, ErrorsRepresenter

They are now accessable via their named path.

    Create["representer.parse.class"] #=> MyRepresenter


## Rendering

Rendering a document after the operation finished is part of the presentation layer, which should *not* happen inside the operation itself. Serializing a document is to happen where the operation was called, such as a controller.

However, you may use the result object to grab representers and models.

{{  "representer_test.rb:render" | tsnippet }}

Luckily, [`Endpoint`](endpoint.html) and `respond` in Rails controllers help you with this.

## Full Example

Often, an operation will maintain multiple representers, e.g. for parsing, to render into a specific media format, and to handle error cases.

You could have a generic errors representer.

{{  "representer_test.rb:errors-rep" | tsnippet }}

Using naming, the operation may then contain several representers.

{{  "representer_test.rb:full" | tsnippet }}

An exemplary controller method to handle both outcomes could look like the following snippet.

{{  "representer_test.rb:full-call" | tsnippet }}


Make sure to check out [Endpoint](endpoint.html) which bundles the most common outcomes for you and is easily extendable.