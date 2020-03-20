GraphQL
=======

# Overview
A language used to request data from a server. Whereas REST GET requests typically hit a single endpoint with little/no filtering or customisation possible, GraphQL queries are more similar in nature to SQL queries in the sense that the client can be very specific about which subset of data they wish to be returned. Getting exactly the data that is required in one request reduces the total number of requests needed (thus saving on the request overhead).

# Querying
Querying in GraphQL uses a JSON-like structure. The structure is built up with the desired query, and send to the server. The server will return data that looks very similar in structure to the query that was sent.

A query is held within an outermost-level `query` block:

```
query {
}
```

For the particular application we are querying against, there will be a series of resolvers available (similar to functions. These resolvers, when called, return an object of some sort. These objects, in terms of other languages, can either be primitive or complex.

For example, in GitHub's GraphQL API, `viewer` returns a `User` object representing the currently logged-in user. In the query, we specify that we want to call this `viewer` resolver, and we specify which fields within the `User` object we want returning.

This query...
```
query {
  viewer {
    login
  }
}
```

Returns...
```
{
  "data": {
    "viewer": {
      "login": "MikeGreenSainsburys"
    }
  }
}
```

The specific resolvers, objects, and fields available for any given API is different from another API and the details can be found in their documentation.

A key difference between the GraphQL approach and the REST approach here is that every field is explicitly requested. Therefore, there is no over-fetching.

Some resolvers have parameters to tailor the result. A common use case for this is when a list will be returned from a resolver. We may have an attribute on the resolver called `last` which allows you to specify to return only the last `n` elements of the list.

In this example, `following` returns a list of some object. We specify to return the last 2 elements of this list. The object type within this list has a resolver called `nodes` for which returns another object with a field called `login`. This example serves to also show how nested the query can become if needed:

```
query {
  viewer {
    login
    following(last: 2){
      nodes{
        login
      }
    }
  }
}
```

It returns:

```
{
  "data": {
    "viewer": {
      "login": "MikeGreenSainsburys",
      "following": {
        "nodes": [
          {
            "login": "mikegreen1995"
          },
          {
            "login": "tommyclark"
          }
        ]
      }
    }
  }
}
```
