---
id: version-0.12.5-create-server
title: Create a Tartiflette instance
sidebar_label: 4. Create a Tartiflette instance
original_id: create-server
---

Make sure you followed the [Project initialization](./install-tartiflette.md) steps.

To help you with the code to build the "Recipes Manager" application, we'll provide some code blocks. Simply copy-paste the code into the corresponding file which was created at the previous step and which should be empty for now.

## Write code

### **recipes_manager/sdl/Query.graphql**

As explained on the homepage, our Engine uses the [official Schema Definition Language](https://graphql.org/learn/schema/).

We will start to define the `Query` Schema, first.

```graphql
type Query {
    recipes: [Recipe]
    recipe(id: Int!): Recipe
}

enum IngredientType {
    GRAM
    LITER
    UNIT
}

type Ingredient {
    name: String!
    quantity: Float!
    type: IngredientType!
}

type Recipe {
    id: Int
    name: String
    ingredients: [Ingredient]
    cookingTime: Int
}
```

### **recipes_manager/app.py**

The following file will be in charge of starting the HTTP Server _(`web.run_app` function)_.

* `register_graphql_handlers` will attach HTTP handlers to the aiohttp application
  * `app`: The `aiohttp` application (created with `app = web.Application()`)
  * `engine_sdl`: The Tartiflette Engine which will be used by the HTTP Handlers
  * `engine_modules`: The modules list which will be loaded by Tartiflette at cooking time _(building process)_
  * `executor_http_endpoint`: Endpoint path where the GraphQL API will be exposed
  * `executor_http_methods`: HTTP methods where the GraphQL API will be exposed _(We recommend to expose it only on `POST`)_
  * `graphiql_enabled`: A [GraphiQL](https://github.com/graphql/graphiql) client to browse the GraphQL, used mostly in for development.

```python
import os

from aiohttp import web

from tartiflette_aiohttp import register_graphql_handlers


def run():
    app = web.Application()

    web.run_app(
        register_graphql_handlers(
            app=app,
            engine_sdl=os.path.dirname(os.path.abspath(__file__)) + "/sdl",
            engine_modules=[
                "recipes_manager.query_resolvers",
                "recipes_manager.mutation_resolvers",
                "recipes_manager.subscription_resolvers",
                "recipes_manager.directives.rate_limiting",
                "recipes_manager.directives.auth",
            ],
            executor_http_endpoint='/graphql',
            executor_http_methods=['POST'],
            graphiql_enabled=True
        )
    )

```

### **recipes_manager/__main__.py**

This is the entrypoint of our application, we won't go into more details here.

```python
#!/usr/bin/env python
import sys

from recipes_manager.app import run

if __name__ == "__main__":
    sys.exit(run())

```

## Launch the app

We are now ready to launch our GraphQL API. _(Make sure that your shell is inside the virtualenv with the `pipenv shell` command)_.

```bash
$ python -m recipes_manager
======== Running on http://0.0.0.0:8080 ========
(Press CTRL+C to quit)

```

Your **Recipes Manager GraphQL API** is now live.

But ... there is nothing in it, yet. We are now going to implement our first resolver.
