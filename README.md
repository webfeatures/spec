# web features

Exposing the web as generic features

# Sample

API usage example in Bun (Typescript)

```ts
// Experimental example in Bun (typescript)
// Using Typebox as the JSON schema library
import { Feature, Service, Pod } from ".";

// Define the web feature model
const GetTrendingPosts = Feature.create({
  name: "GetTrendingPosts",
  input: Type.Void(),
  output: Type.Object({
    posts: Type.Array(
      Type.Object({
        title: Type.String(),
        url: Type.String(),
      })
    ),
  }),
});

// Create the services
const hackernews = Service.create("hackernews.com");
const medium = Service.create("medium.com");

// Create a web feature instance
const getTrendingPostsOnHackerNews = GetTrendingPosts.implement({
  async handler({ output }) {
    const response = await fetch("https://news.ycombinator.com/");
    const html = await response.text();

    output.posts = html
      .split("<a href=")
      .map((link) => {
        const url = link.split('">')[0];
        const title = link.split('">')[1].split("</a>")[0];
        return { title, url };
      })
      .filter((post) => post.title && post.url);
  },
});

// Execute the web feature with the service
const { posts } = await getTrendingPostsOnHackerNews.execute();

// Attach the web feature to a service
hackernews.add(getTrendingPostsOnHackerNews);

// Create a web feature instance
const getTrendingPostsOnMedium = GetTrendingPosts.implement({
  async handler({ output }) {
    const response = await fetch("https://medium.com/");
    const html = await response.text();
    // Parse the html to get the posts and set the output
    // output.posts = ...
  },
});

// Execute the web feature with the service
const { posts } = await getTrendingPostsOnMedium.execute();

// Attach the web feature to a service
medium.add(getTrendingPostsOnMedium);

// Executing the web feature on the services
const { posts } = await hackernews.execute({ name: "GetTrendingPosts" });

const { posts } = await medium.execute({ name: "GetTrendingPosts" });

// Alternatively

const pod = Pod.create();
pod.addService(hackernews);
pod.addService(medium);

const { posts } = await pod.execute({
  host: "hackernews.com",
  name: "GetTrendingPosts",
});

const { posts } = await pod.execute({
  host: "medium.com",
  name: "GetTrendingPosts",
});
```

</details>

# Introduction

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

# Definitions

`Web features` are bound to `web services`.

## `service`

A (web) service can be a website, an API, a web app or anything accessible on the web and is referenced as a [host](#host).

Ex: `github.com`, `tiktok.com`, `somebody.myshopify.com`.

## `feature`

A (web) feature is a generic way to describe how to perform an action on a web service.

It is composed of a [`model`](#model) that describes the context to run an action and an [`instance`](#instance) that implements the model specification for a specific web service.

### `- model`

| Property          | Description                    |
| ----------------- | ------------------------------ |
| [name](#name)     | a unique descriptive name      |
| [input](#input)   | a json schema compatible model |
| [output](#output) | a json schema compatible model |
| [tests](#tests)   | list of tests as functions     |

#### `-- name`

A unique descriptive name for the web feature model. It should comply with the following rules:

- starts with a verb (e.g. `Get`, `Post`, `Search`)
- uses kebab-case

Examples:

- `GetUser`
- `PostComment`
- `SearchProducts`
- `GetImages`

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

A list of tests that can be run against the web feature model. Each test should be a function that takes an input and returns an output. The output should be compared to the expected output.

### `- instance`

A web feature instance is based on a web feature model.

| Property      | Description                                |
| ------------- | ------------------------------------------ |
| [name](#name) | a web feature model name                   |
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

# Development Rules

- All properties should be in `snake_case` (lowercase with underscores)
- References and names should be in `pascal-case` (capitalized with no spaces)
