# Saturn with CAS Single Sign-On Sample Application

Lessons learned building a web application built on Saturn and using CAS for single sign-on.

## Sample code

You can find all of my sample code on my [Github](https://github.com/MarneeDear/saturn-example).

## Why am I doing this?

> I decided to try out Saturn to see if we can't start building 100% F# web apps. But first I needed to make CAS work.

At the [University of Arizona, College of Medicine - Tucson](https://medicine.arizona.edu/) we build a lot of web apps. As the Applications Architect I have tried to use as much F# as possible, but we usually ended up with a hybrid of F# and C#. This looked like F# for Core and Infrastructure, and C# for the web host on .NET MVC. This worked great but it is not F#, or functional programming, all the way through. But we don't have to do that anymore now that we have F# web frameworks like [Giraffe](https://github.com/giraffe-fsharp/Giraffe) and [Saturn](https://saturnframework.org/).

## A little about Saturn

![Alt text](https://saturnframework.org/assets/img/logo.png)

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

> Pro Tip: Make sure you have the [.NET SDK and .NET Runtime](https://www.microsoft.com/net/download) installed.

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

## My project (saturn-example)

Here is what you get from my [repo on Github](https://github.com/MarneeDear/saturn-example):

* [CAS](https://en.wikipedia.org/wiki/Central_Authentication_Service) integration for single-sign-on. This is similar to OAuth and uses [this CAS library](https://www.nuget.org/packages/AspNetCore.Security.CAS) from [Indiana University Foundation](https://github.com/iuCrimson/aspnet.security.cas).
* A simple [Onion Architecture](https://www.c-sharpcorner.com/UploadFile/dhananjaycoder/step-by-step-implementing-onion-architecture-in-Asp-Net-mvc/) example. There is a Core library where you put your models, and Infrastructure where you put your business logic and data access.
* Test projects using [xUnit](https://xunit.github.io/) and [FsUnit](https://fsprojects.github.io/FsUnit/xUnit.html).

### CAS Single-Sign-On (Authentication)

Saturn doesn't have built-in CAS support, but it does have OAuth with GitHub, Google, and custom providers, which is great, but I need CAS, so I had to integrate it myself. This turned out to be pretty easy because I found a compatible [CAS auth library](https://github.com/iuCrimson/aspnet.security.cas) available on Nuget, which meant I could install wit with `paket`.

Once imported, I could implement it by creating a new module with an`ApplicationBuilder` class with a new `CustomOperation` method. This will make it so I can use it in the [`application` computation expression](https://saturnframework.org/docs/api/application/#application-builder).

```fsharp
module CAS

open Saturn
open Microsoft.Extensions.DependencyInjection
open Microsoft.AspNetCore.Builder
open Microsoft.AspNetCore.Authentication.Cookies
open Microsoft.AspNetCore.Authentication
open AspNetCore.Security.CAS

type ApplicationBuilder with
    //Enables CAS authentication
    //Uses https://github.com/IUCrimson/AspNet.Security.CAS
    [<CustomOperation("use_cas")>]
    member __.UseCasAuthentication(state: ApplicationState, casServerUrlBase) =
:
:
:
```

And then we can use it in our application like this. In Program.fs, the entry point of the app we do this.

```fsharp
let app = application {
    pipe_through endpointPipe
    error_handler (fun ex _ -> pipeline { render_html (InternalError.layout ex) })
    use_router Router.appRouter
    url "http://saturn.local:8085/"
    memory_cache
    use_static "static"
    use_gzip
    use_config (fun _ -> {connectionString = "DataSource=database.sqlite"} ) 
    use_iis
    use_cas "https://webauth.arizona.edu/webauth"
}
```

You can see the full source code on my GitHub repo:

* The entry point [Program.fs](https://github.com/MarneeDear/saturn-example/blob/master/src/Template.Saturn.WebHost/Program.fs)
* The CAS implementation [Cas.fs](https://github.com/MarneeDear/saturn-example/blob/master/src/Template.Saturn.WebHost/Authentication/Cas.fs)

### The CAS implementation

I followed the pattern used in the Saturn OAuth implementations.

`use_cas` takes two arguments:

* `casServerUrlBase` -- your CAS server's authentication URL. In my case I am using my University's CAS server known as `webauth`.
* `state` -- `ApplicationState` defined in Saturn [here](https://github.com/SaturnFramework/Saturn/blob/master/src/Saturn/Application.fs). In the `application` computation expression, the state is automatically passed to the method.

What we want to end up with in `UseCasAuthentication` is a new state with the CAS authentication configuration added to the old state.

I defined a middleware function in which I enabled [`UseAuthentication`](https://docs.microsoft.com/tr-tr/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

```fsharp
  let middleware (app : IApplicationBuilder) =
    app.UseAuthentication()
```

I defined a service function which configures the CAS authentication by following the guide to the [CAS library](https://github.com/iuCrimson/aspnet.security.cas).

```fsharp
  let service (s : IServiceCollection) =
    let c = s.AddAuthentication(fun cfg ->
      cfg.DefaultScheme <- CookieAuthenticationDefaults.AuthenticationScheme
      cfg.DefaultChallengeScheme <- "CAS"
      )
    addCookie state c
    c.AddCAS(fun o -> 
        o.CasServerUrlBase <- casServerUrlBase
        o.SignInScheme <- CookieAuthenticationDefaults.AuthenticationScheme
        )
    |> ignore
    s
```

Here I configured the default scheme and gave the Challenge Scheme a name (`CAS`). Later, when I want to do a login, I can have the Giraffe Auth challenge to use `CAS` like this:

```fsharp
  (Giraffe.Auth.challenge "CAS")
```

I also configure the `CAS` server URL and the Authentication Scheme.

Finally I return a new state with all of the configurations added. Like this:

```fsharp
  { state with
      ServicesConfig = service::state.ServicesConfig
      AppConfigs = middleware::state.AppConfigs
      CookiesAlreadyAdded = true
  }
```

## Make the login work

Ok, great, we have added CAS authentication, but how do I login? I need a login button and to add some security that restricts access to resources. We do this with the [`router`](https://saturnframework.org/docs/api/scope/) and [`pipeline`](https://saturnframework.org/docs/api/pipeline/) computation expressions.

> Note: Router in the Saturn user guide is a outdated. It still references `scope`, which was deprecated in favor of `router`. I did a pull request to update this in the source files, but as of 11/23/2018 the published guide has not been updated.

Router defines paths and routes through your application. It has a lot in common with [.NET MVC routing](https://docs.microsoft.com/en-us/aspnet/mvc/overview/older-versions-1/controllers-and-routing/asp-net-mvc-routing-overview-cs).

### Router.fs

First I need a top-level-router which will handle all requests to the app. This might route requests to a controller, a pipeline, or another router. In my apps I need a public side, usually the login page, and a private side that only authenticated users can access. To handle this I created both a top-level router, `browserRouter`, and a `loggedInView` router.

*logged-in view router*

```fsharp
let loggedInView = router {
    pipe_through login
    pipe_through protectFromForgery
    forward "/books" Books.Controller.resource 
    forward "/dashboard" (fun next ctx -> htmlView (Dashboard.layout ctx) next ctx)
}
```

Requests that pass through the `loggedInView` router are checked for authentication status and sent to webauth if not. `pipe_through login` makes this happen like this:

```fsharp
let login = pipeline {
    requires_authentication (fun next ctx -> htmlView (Login.layout ctx) next ctx)
}
```

The important part is `requires_authentication`. Is a `CustomOperation` `PipelineBuilder` which checks for authentication. In my case, here, if not authenticated, the user will be sent to the login page.

*top-level router*

```fsharp
let browserRouter = router {
    not_found_handler (htmlView NotFound.layout) //Use the default 404 webpage
    pipe_through browser //Use the default browser pipeline
    forward "" defaultView //Use the default view
    get "/books" loggedInView
    get "/login" (fun next ctx -> htmlView (Login.layout ctx) next ctx)
    get "/logout" (signOut "Cookies" >=> (fun next ctx -> htmlView (Logout.layout ctx) next ctx)) 
    get "/dashboard" loggedInView 
    get "/webauth" (fun next ctx -> (isAuthenticated ctx) next ctx)
}
```

* `/login` gets the login page but does not do send the user to `webauth`
* `/logout` sign-out the user (clears auth cookies) and gets the logout page
* `/webauth` checks if the user has been authenticated and send the user to webauth if not authenticated.
* `/books` and `/dashboard` are private pages so they go through the `loggedInView` router

## Logged-in view layout template vs. public view layout template

I had a problem with the App level layout. This layout had the login button and the menu and each was toggled based on the app context passed to it. Using the context I would check for authentication status and toggle the login and menu. The problem was that the menu would not get replaced with the login button after logout. In order to deal with this I created two app level layouts.

* App.fs is the public view layout
* AppAuth.fs is the private view layout

In the public views I plug into the public view layout like this:

```fsharp
let layout ctx =
    App.layout (login ctx) ctx
```

In the private views I plug into the logged-in view layout like this:

```fsharp
let layout ctx =
    AuthApp.layout (dashboard ctx) ctx
```

## Final thoughts

![I'm trying](http://www.memeologist.com/wp-content/uploads/2017/05/25074d69a2658f5d354bc03890b47fd7.jpg?67ef12)

And that is all there is is to it. Working with Saturn was a bit painful at first as I was trying to learn the abstractions and `opinions`, but eventually it all just made a lot of sense and it is pretty easy to work with. Try it out!