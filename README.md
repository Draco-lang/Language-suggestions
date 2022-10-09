<p align="center">
    <img src="./Resources/Logo-Long.svg#gh-light-mode-only" width=60%>
    <img src="./Resources/Logo-Long-Inverted.svg#gh-dark-mode-only" width=60%>
</p>

<h2 align="center">A new .NET programming language in the making</h1>

<p align="center">
    <a href="https://discord.gg/gHfhpPFzYu"><img alt="Discord" src="https://badgen.net/discord/members/gHfhpPFzYu?icon=discord&color=D70&labelColor=F80&label=Join+our+Discord!"></a>
</p>

### I just want to see how the language looks!

[See the Samples folder.](./Samples/)

### The aims of the language

> I want to do to C# what Kotlin did to Java

Draco should be a high-level language which could replace C#. Here are a few major key points that we aren't changing:

 * It is strongly statically typed
 * It is not academic & should be easily used by a regular .NET developer
 * It should be more or less readable by people not knowing it (as opposed to ML, for instance)
 * It should be debuggable
 * It prefers generalized approaches over sugared special cases
 * It adds a bigger and more worked out feature earlier, than an underspecified one that will result in extra features to work out the missed use cases

### Current state of the project

There is a very basic and not finished [Compiler](https://github.com/Draco-lang/Compiler)

Right now we are still working out the core of the language. We do this by collecting ideas, inspiration, frustrating things and useful bits from existing languages. We have started working on a specification (see the [Specification](Specification) folder) and you can see an [example program](Example.fr) that we try to keep updated to more or less reflect our current idea of the language. You can also take a look at the [feature tracking](FeatureTracking.md) document, where we keep track of all features we want to investigate.

### Our workflow

 1. We start from an idea or a discussion, usually either on our [Discord server](https://discord.gg/gHfhpPFzYu) or in an issue.
 2. The ideas we like are then worked on as an issue, we append comments, elaborate on the features and internals.
 3. When we want to add something to the specification, we either create a new, or edit an existing file in the [Specification](Specification) folder, write the specification document and open a Pull Request with the name `RFCnnnn - Short description`.
 4. We critique the RFC, modify if needed, even leave out features we can't agree on for a while.
 5. When no more comments arrive on the RFC and we can agree on the feature, we merge it into the specification.

### Propose your ideas!

Now that you grasped the main idea, feel free to open an issue which

 * Proposes a language idea. No strict requirements, so for now, any idea is welcomed
 * Tells about your experience using any programming language. Tell us what you find interesting about a language or what you disliked about it
