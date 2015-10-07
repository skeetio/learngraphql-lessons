```
name:  Invoking Mutations
bulletPackage: free
```

Mutations are the way to change the dataset behind GraphQL. A mutation is very similar to a query in GraphQL, but GraphQL assumes the mutation is going to change the dataset behind it.

We learned that, GraphQL execute queries in parellel to acheive fast response time. But, if there are more than one mutation in a single request, it'll invoke mutations by one by one. We'll learn about the reason behind that as well in this lesson.

So, let's mutate our blog dataset.

*****

```
id:     first-mutation
type:   mcq
points: 20
```

## First Mutation

Okay, let's add a new author to our blog. For that, apply this mutation in our GraphQL Sandbox.

~~~
mutation {
  createAuthor(
    _id: "john",
    name: "John Carter",
    twitterHandle: "@john"
  ) {
    _id
    name
  }
}
~~~

Once you invoked it you'll get a result like this:

~~~
{
  "data": {
    "createAuthor": {
      "_id": "john",
      "name": "John Carter"
    }
  }
}
~~~

So, this is what has happened. We invoked a mutation called `createAuthor` with some arguments. Then the mutation just returned the mutated document. In this case it returned the newly added author's` _id` and `name` fields.

Unlike in queries, we need to mention this is a mutation by starting the query with `mutation` keyword. See:

~~~
mutation {
  ...
}
~~~

---

Now you've a little task to do. Try to add a new author, but do not include the `name` in the argument. What's the response you got:

  - I could add the new author.
  - I could add the author, but server add `_id` as the name.
  - I got an error saying: there are few required arguments missing.
  - **I got an error saying: argument "createAuthor" is required.**

*****

```
id:     required-arguments
type:   text
points: 5
```

## Required Arguments

In our mutation `createAuthor`, `_id` and `name` are two required arguments. We can't invoke the mutation without them. That's why we got an error.

> This is not unique for mutations, you can define required arguments for queries too. Also we can make fields in types also as required. For an example, we can define _id for Author is a required field.

You can use GraphQL Sandbox's Docs tab to inspect arguments. [See](https://cldup.com/ZzkRN_fr9R.gif).

Here `name` field has a type of `String!`. So last charactor `!` indicate it's a required field.

*****

```
id:     multiple-mutations
type:   mcq
points: 15
```

## Multiple Mutations

Just like in queries, we can invoke multiple mutations and assign result into different variables. For an example, let's try to two authors as one:

~~~
mutation {
  sam: createAuthor(
    _id: "sam",
    name: "Sam Hautom"
    twitterHandle: "@sam"
  ) {
    _id
    name
  },
  
  chris: createAuthor(
    _id: "chris",
    name: "Chris Mather"
    twitterHandle: "@chris"
  ) {
    _id
    name
  } 
}
~~~

As expected, we get a result like this:

~~~
{
  "data": {
    "sam": {
      "_id": "sam",
      "name": "Sam Hautom"
    },
    "chris": {
      "_id": "chris",
      "name": "Chris Mather"
    }
  }
}
~~~

Now try to add the same author twice like this:

~~~
mutation {
  carter: createAuthor(
    _id: "carter",
    name: "Carter Boom"
    twitterHandle: "@carter"
  ) {
    _id
  },

  carter2: createAuthor(
    _id: "carter",
    name: "Carter Boom"
    twitterHandle: "@carter"
  ) {
    _id
  }
}
~~~

What's the response you got?

  - Both mutations succedded
  - First mutation succeeded. But the in the second one we got an error saying: "Can't invoke a single mutation with same data"
  - **First mutation succeeded. But the in the second one we got an error saying: "Author already exists: carter"**
  - Got an error saying: "Request failed because of identical mutation arguments"

*****

```
id:     mutations-in-sequence
type:   text
points: 5
```

## Mutations Executed as a Sequence

In GraphQL, mutations are executed as a sequence. Otherwise, it's hard to detect errors like adding the same author again and again.

> It's totally upto the GraphQL server implementation to implement mutations like this. Reference NodeJS implementation and other community implementations for Python and Scala follow this.

*****

```
id:     finally
type:   text
points: 5
```

## Finally

Now we've learned how to invoke mutations againts a GraphQL server. It's just like a query, but a mutation change the dataset behind GraphQL.

In our GraphQL Sandbox, we've another mutation called `createPost`. So, try to play with mutations by adding new posts and querying them.
