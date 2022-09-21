![GitHub last commit (master)](https://img.shields.io/github/last-commit/mtucker6784/EncompassRest/master.svg?logo=github&logoColor=lightgray&style=popout-square)
[![NuGet Version](https://img.shields.io/nuget/v/EncompassRest.svg?style=popout-square&logoColor=lightgray&logo=nuget)](https://www.nuget.org/packages/EncompassRest/)
[![NuGet Downloads](https://img.shields.io/nuget/dt/EncompassRest.svg?style=popout-square&logoColor=lightgray&logo=nuget)](https://www.nuget.org/packages/EncompassRest/)
[![Build status](https://img.shields.io/azure-devops/build/tydude4christ/Public/1.svg?style=popout-square&logoColor=lightgray&logo=azuredevops)](https://dev.azure.com/tydude4christ/Public/_build?definitionId=1)

# EncompassRest
Encompass API Client Library for .NET Framework 4.5+ and .NET Standard 1.1+.

## Why does this exist?
You may wonder why this library exists when Ellie Mae has provided their own [Encompass API .NET Language Bindings](https://github.com/EllieMae/developerconnect-dotnet-bindings).

First, the Encompass API .NET Language Bindings were released long after the API's became available so users needed a common .NET library for consuming the Encompass API's, hence this library was born.

Second, the swagger generated Encompass API .NET Language Bindings are less robust compared to the custom crafted, well thought-out, and thoroughly tested EncompassRest library.

### More features
* Field ID support
* Dirty checking serialization to only send back the updated data
* Option to auto retrieve new token and resend the request when using an expired token
* Option to auto resend the request on server timeouts
* Raw API calls support
* Ability to assign and retrieve string properties as enum values to reduce the need of magic strings
* .NET Standard support
* Extension data support, for when Ellie Mae adds properties to the returned json objects but the library isn't updated yet
* `CancellationToken` support
* Properties are lazily created upon gets so you don't need to new them up first to use them
* `INotifyPropertyChanged` support
* Uses `decimal` instead of `double` to prevent precision loss

### Simpler
* More convenient interface with a single defined entry point, the `EncompassRestClient` object which is the equivalent of the `Session` object in the SDK
* Simpler Type names, e.g. `UCDDetail` instead of `LoanContractClosingCostClosingDisclosure3UCDDetails`
* Single NuGet package
* No configuration files
* Publicly exposes only relevant .NET API

### Optimized for performance
* Serializes directly to output `Stream` meaning no string allocation
* Reuses one `JsonSerializer` instance so it's cache isn't needed to be repopulated on each request
* `HttpClient` is in general more performant over `RestSharp`

## Getting started
1. Install the [EncompassRest](https://www.nuget.org/packages/EncompassRest) Nuget package.
2. [Create an async method](#create-an-async-method) in your consuming code.
3. [Create an `EncompassRestClient` object](#create-an-encompassrestclient-object).
4. [Use `EncompassRestClient` object](#use-encompassrestclient-object) to make API calls.

### Create an async method
EncompassRest is a fully asynchronous library meaning all of it's Encompass API calls are made asynchronously to allow great performance for maximum throughput. To get started using the library you need to create an `async` method. To avoid deadlocks with async code it is recommended you **_DO NOT_** use `Task.Result` or the `Task.Wait` methods and instead implement `async` all the way from the top, e.g. `Main` or your `Controller`s actions.

#### Web apps:
```c#
public async Task<IActionResult> GetAsync()
{
    // Your async code goes here
}
```

#### Console apps:
For C#7.1 and up you should use an async `Main` method like so.

```c#
public async Task Main()
{
    // Your async code goes here
}
```

For C#7 and below you should use `Task.Run` inside of `Main` to use async methods.

```c#
public void Main()
{
    Task.Run(async () => {
        // Your async code goes here
    }).GetAwaiter().GetResult();
}
```

### Create an `EncompassRestClient` object
The `EncompassRestClient` class implements `IDisposable` so it is recommended to use a `using` statement to automatically dispose of the object.

#### From user credentials which auto-retrieves new token when expired
```c#
using (var client = await EncompassRestClient.CreateAsync(
    new ClientParameters("apiClientId", "apiClientSecret"),
    tokenCreator => tokenCreator.FromUserCredentialsAsync("encompassInstanceId", "encompassUserId", "encompassPassword")))
{
    // use client
}
```

#### From authorization code
```c#
using (var client = await EncompassRestClient.CreateFromAuthorizationCodeAsync(
    new ClientParameters("apiClientId", "apiClientSecret"), "redirectUri", "authorizationCode"))
{
    // use client
}
```

#### From access token
```c#
using (var client = await EncompassRestClient.CreateFromAccessTokenAsync(
    new ClientParameters("apiClientId", "apiClientSecret"), "accessToken"))
{
    // use client
}
```

### Use `EncompassRestClient` object
Use the various properties on `EncompassRestClient` such as `Loans`, `Schema`, `Webhook`, `Pipeline`, and `BatchUpdate` to make Encompass API calls.

---

## Resources
* [Developer Connect](https://docs.developer.elliemae.com/reference) - Encompass API's reference location.

* If you'd like to join our Slack channel please reach out to one of the repo's maintainers.

If you're interested in contributing please look over the [Guidelines Doc](Guidelines.md).
