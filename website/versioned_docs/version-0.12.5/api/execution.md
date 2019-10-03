---
id: version-0.12.5-execution
title: Execution
sidebar_label: Execution
original_id: execution
---

Aside from building the `Engine`, the `execute` method is responsible for executing the GraphQL query coming from the client.

Its parameters are:
* `query`: The GraphQL Request/Query as UTF8-encoded string _(sent by the client)_
* `operation_name`: Operation name to execute
* `context`: A dict containing anything you need to be pass through the execution process
* `variables`: The variables used in the GraphQL request
* `initial_value`: An initial value given to the resolver of the root type

```python

from tartiflette import create_engine

engine = await create_engine(
    "myDsl.graphql"
)

result = engine.execute(
    query="query MyVideo($id: String) { video(id: $id) { id title } }",
    operation_name="hello_world",
    context={
        "mysql_client": MySQLClient(),
        "auth_info": AuthInfo()
    },
    variables: {
        "id": "1234"
    },
    initial_value: {}
)

# `result` will contain something like
# {
#     "data": {
#         "video": {
#             "id": "1234",
#             "title": "My fabulous title"
#         }
#     }
# }
```
