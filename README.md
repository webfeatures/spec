# web features

Exposing the web as generic features

```
This is a work in progress. Please feel free to contribute.
```

# Introduction

The web exists to interconnect the world. While it is true, it does so in many different ways that demand to all be understood.

We propose a minimalistic approach to abstract these into defined web features that can be used to connect to any web service in a generic way.

You want to get the weather forecast for the next 5 days? You should be able to use something like a `get-weather-forecast` web feature and execute it on any web service that provides weather forecast data (with the same interfaces).

# Why

1. **Simplicity**

As a developer, web services can be hard to interact with. Leveraging the abstraction of the web features can help to simplify the way we interact with web services.

2. **Consistency**

Providing generic web features models can help to standardize the way we interact with web services. This can help to create a consistent way to interact with web services. While this will be opinionated, it will help to create a common ground for developers to interact with web services.

3. **Flexibility**

Steps to interact with a feature on web service can be complex and hard to understand. By abstracting these into web features, we can handle the complexity as code in a more flexible way without restriction.

4. **Reusability**

Abstraction already happens in every project we build. By creating a common abstraction for web services, we may hope to build and maintain a community based set of web features that can be reused across projects.

# Rules

1. All properties should be in `snake_case` (lowercase with underscores)
2. References/names should be in `kebab-case` (lowercase with dashes)

# Definitions

## `web service`

A web service is a service that is accessible over the internet. It can be a website, an API, a web app, etc.

## `web feature`

A web feature is a generic way to describe how to perform an action on a web service.

It is composed of a [`model`](#-model) that describes the context to run an action and an [`instance`](#-instance) that implements the model specification for a specific web service.

### `- model`

| Property            | Description                    |
| ------------------- | ------------------------------ |
| [name](#--name)     | a unique descriptive name      |
| [input](#--input)   | a json schema compatible model |
| [output](#--output) | a json schema compatible model |
| [tests](#--tests)   | list of tests as functions     |

#### `-- name`

A unique descriptive name for the web feature model. It should comply with the following rules:

- starts with a verb (e.g. `get`, `post`, `put`, `delete`)
- uses kebab-case

Examples:

- `get-user`
- `post-comment`
- `search-products`
- `get-images`

#### `-- input`

A JSON-schema compatible model that describes the input properties. It should account for all possible input properties that can be passed to run the web feature on any web service.

Example:

```json
{
  "type": "object",
  "properties": {
    "search_term": {
      "type": "string"
    }
  },
  "required": ["search_term"]
}
```

#### `-- output`

A JSON-schema compatible model that describes the output properties.

Example:

```json
{
  "type": "object",
  "properties": {
    "items": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "title": {
            "type": "string"
          },
          "url": {
            "type": "string"
          }
        },
        "required": ["title", "url"]
      }
    }
  }
}
```

#### `-- tests`

### `- instance`

A web feature instance is based on a web feature model.

| Property      | Description                                |
| ------------- | ------------------------------------------ |
| [host](#host) | a unique host representing the service     |
| handler       | async (input) => output                    |
| samples       | list of input samples to run tests against |

## `host`

A host is a unique identifier that represents a web service. It should represent the main web service this web feature should be attached to by using the smallest domain level as the identifier.

Examples:

| Url                            | Host                   |
| ------------------------------ | ---------------------- |
| https://www.github.com         | github.com             |
| https://www.tiktok.com/@france | tiktok.com             |
| https://somebody.myshopify.com | somebody.myshopify.com |
