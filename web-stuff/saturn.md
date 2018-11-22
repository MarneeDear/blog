# Saturn with CAS Single Sign-On Sample Application

Lessons learned building a web application built on Saturn and using CAS for single sign-on.

## Sample code

You can find all of my sample code on my [Github](https://github.com/MarneeDear/saturn-onion-template).

## Why am I doing this?

> I decided to try out Saturn to see if we can't start building 100% F# web apps.

At the [University of Arizona, College of Medicine - Tucson](https://medicine.arizona.edu/) we build a lot of web apps. As the Applications Architect I have tried to use as much F# as possible, but we usually ended up with a hybrid of F# and C#. This looked like F# for Core and Infrastructure, and C# for the web host on .NET MVC. This worked great but it is not F#, or functional programming, all the way through. But we don't have to do that anymore now that we have F# web frameworks like [Giraffe](https://github.com/giraffe-fsharp/Giraffe) and [Saturn](https://saturnframework.org/).

## A little about Saturn

There are a lot of great things to like about Saturn.

* Easy to scaffold with the .NET SDK
* Built on ASP.NET Core, Giraffe and Kestrel, so it is cross-platform.
* A number of [useful sample apps](https://github.com/SaturnFramework/Saturn/tree/master/sample).
* Useful extensions and abstractions for things like OAuth and Azure Functions.
* Supports .NET Core and .NET Standard.

and ...

## Saturn is opinionated! 

And I like it. Once you get used to the patterns and abstractions, it makes a lot of sense and is easy to make things work.

I especially like the use of [computation expressions](https://www.youtube.com/watch?v=bYor0oBgvws).

You can find the list of computation expressions used in Saturn in the [API Reference](https://saturnframework.org/docs/api/pipeline/).

We have these computation expressions:

* Pipeline
* Router
* Controller
* Application

We use these, and combinations of them, to build the wider application architecture.

## Getting started

Follow the documentation [here](https://saturnframework.org/docs/guides/how-to-start/).

Make sure you have the [.NET SDK and .NET Runtime](https://www.microsoft.com/net/download) installed.

As of 2018-11-18, the project template requires .NET SDK version `2.1.300`. So after creating the project, check `global.json` for the version and either change it to the version you have installed or install the version in `global.json`. You can install multiple versions at one time (older versions can be found at the download link above).

If you don't have the right SDK version installed you'll probably see an error like this during the build.

```text
A compatible SDK version for global.json version: [2.1.409] from [C:\Users\Marnee\Dropbox\github\saturn-stuff\saturn-onion-template\global.json] was not found
Did you mean to run dotnet SDK commands? Please install dotnet SDK from:
  http://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409
```

> Pro Tip: Be careful with dashes in names. By default, `dotnet` will name your project according to the folder in which it is contained, but it converts dashes to underscores, which throws off file paths. You might see an error that looks like this: 

```text
System.Exception: Start of process dotnet failed. WorkingDir C:\Users\Marnee\Dropbox\github\saturn-stuff\saturn-blog\src\saturn_blog\ does not exist.
```

## The big parts

