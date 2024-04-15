# Web features

Abstracting the web through generic features

```mermaid
graph LR;
    a["Feature Contract"]-->b["Feature Adapter"]-->c["Service"];
```

> From a contract, we create many adapters that are used to interact with many web services using an identical input and expecting the same output shape.

**Example**

```mermaid
graph LR;
    contract1["name: get_trending_articles
    (Feature Contract)"];
    contract2["name: get_best_authors
    (Feature Contract)"];

    service1["host: medium.com
    (Service)"];
    service2["host: hackernews.com
    (Service)"];
    service3["host: reddit.com
    (Service)"];

    contract1-->|Feature Adapter| service2;
    contract1-->|Feature Adapter| service1;
    contract2-->|Feature Adapter| service1;
    contract2-->|Feature Adapter| service3;
```

# Definitions

## Feature Contract

| Property   | Format                          | Description               | Example                          |
| ---------- | ------------------------------- | ------------------------- | -------------------------------- |
| **name**   | pascal-cased name               | a unique descriptive name | `get_last_posts`                 |
| **input**  | json schema compatible model    | input schema              | `{ size: 10 }`                   |
| **output** | json schema compatible model    | output schema             | `{ posts: post[] }`              |
| **ctx**    | json schema compatible model    | additional context schema | `{ fetch:(Request) => Response}` |
| **test**   | async ({ samples, ctx}) => void | test method               | -                                |

## Feature Adapter

| Property                  | Format                                | Description                                                |
| ------------------------- | ------------------------------------- | ---------------------------------------------------------- |
| [**contract**](#contract) | feature contract                      | ref to existing contract                                   |
| **handler**               | async ({ input, output, ctx}) => void | async method that builds the output given an input and ctx |
| **samples**               |                                       | list of input samples to run tests against                 |

## Service

| Property                         | Format                    | Description                            |
| -------------------------------- | ------------------------- | -------------------------------------- |
| **host**                         | valid host name           | a unique host representing the service |
| [**adapters**](#feature-adapter) | kv<name, feature adapter> | adapters bound to this service         |

## Service Group

| Property                 | Format            | Description      |
| ------------------------ | ----------------- | ---------------- |
| [**services**](#service) | kv<name, service> | grouped services |

# Genesis

The web exists to help the world openely share data and inter-connect. The only thing is that we don't all share how that should be happening and rightfully so.

We propose a minimalistic approach to abstract these into defined web features that can be used to connect to any web service in a generic way.

# Why

1. **Simplicity**

As a developer, web services can be hard to interact with. Leveraging the abstraction of the web features can help to simplify the way we interact with web services.

2. **Consistency**

Providing generic web features models can help to standardize the way we interact with web services. This can help to create a consistent way to interact with web services. While this will be opinionated, it will help to create a common ground for developers to interact with web services.

3. **Flexibility**

Steps to interact with a feature on web service can be complex and hard to describe accross languages or projects. By abstracting these into web features, we can handle the complexity as code in a more flexible way without restriction, only caring to get the correct output for a given input.

4. **Reusability**

Abstraction already happens in every project we build. By creating a common abstraction for web services, we may hope to build and maintain a community based set of web features that can be reused across projects.

## Development Guidelines

### Host

A host is a unique identifier that represents a web service. It should represent the main web service this web feature should be attached to by using the smallest domain level as the identifier.

Examples:

| Url                            |     | Host                   |
| ------------------------------ | --- | ---------------------- |
| https://www.github.com         | --> | github.com             |
| https://www.tiktok.com/@france | --> | tiktok.com             |
| https://somebody.myshopify.com | --> | somebody.myshopify.com |

### Schema & Name properties

All schema properties should follow the `snake_case` formatting rule (lowercase with underscores).
This is to ensure that the schema properties are consistent and easy to read accross services/languages.
