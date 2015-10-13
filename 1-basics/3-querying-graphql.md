```
name:  Querying GraphQL
bulletPackage: free
```

GraphQL query language is a major part of GraphQL. In this lesson, we are going to learn about how to query against a GraphQL server and become familiar with the query syntax.

We'll be querying a dataset for a typical **blog app** (as we [discussed](/basics/lets-learn-graphql) earlier). For now, don't worry about how the GraphQL server works and how the schema is implemented. We'll just focus on the query syntax and play with it.

At the end of this lesson, you'll have a good understanding of GraphQL and how to write GraphQL queries.

Let's get started!

*****

```
id:     hello-graphql
type:   text
points: 5
```

## Hello GraphQL

Let's write our first GraphQL query: to retrieve the title and summary of the latest post in our blog. To invoke that, open our [GraphQL Sandbox](https://sandbox.learngraphql.com/).

Then run this query:

~~~
{
  latestPost {
    title,
    summary
  }
}
~~~

You'll then see something like this:

~~~
{
  "data": {
    "latestPost": {
      "title": "New Feature: Tracking Error Status with Kadira",
      "summary": "Lot of users asked us to add a feature to set status for errors in the Kadira Error Manager. Now, we've that functionality."
    }
  }
}
~~~

With this query, we asked our GraphQL server to send the field `latestPost` of the root of the graph (also known as “root query fields”). Also, we requested just the `title` and `summary` of the result object.

*****

```
id:     get-authors
type:   mcq
points: 15
```

## Get authors

Here's your first task. 

In our GraphQL schema, we have a root query field called `authors`. Try to query that field. Then select the author who is not in our blog.

  - Arunoda Susiripala
  - Pahan Sarathchandra
  - Kasun Indi
  - **Somapala Lamaya**

*****

```
id:     nested-querying
type:   mcq
points: 20
```

## Nested querying

In GraphQL, we can query in nested fashion. As an example, from a single GraphQL query we can retrieve all the posts and their comments as well.

Invoke this query:

~~~json
{
  posts {
    title,
    summary,
    comments {
      content
    }
  }
}
~~~

You'll get all the posts and their comments as we requested.

In this way, we can nest fields and go deeper into our graph as needed.

Now, we need to get the name of the author for each post as well. So, what'll be the GraphQL query for that?

> Hint: Try to browse the documentation tab of the GraphQL schema to retrieve more information about our schema. Autocompletion also helps you.

**Query 1**
~~~
{
  posts {
    title,
    author,
    summary,
    comments {
      content
    }
  }
}
~~~

**Query 2**
~~~
{
  posts {
    title,
    author {
      name
    },
    summary,
    comments {
      content
    }
  }
}
~~~

**Query 3**
~~~
{
  posts {
    title,
    author["name"],
    summary,
    comments {
      content
    }
  }
}
~~~

**Query 4**
~~~
{
  posts {
    title,
    author.name,
    summary,
    comments {
      content
    }
  }
}
~~~

Select the correct query:

  - Query 1
  - **Query 2**
  - Query 3
  - Query 4

*****

```
id:     arguments
type:   mcq
points: 20
```

## Arguments

We can filter the output by specifying any set of fields we like. In addition, we also need a way to select a subset of data rather than retrieving the whole dataset for a particular type. That's where arguments come in.

To experience that, let's get some recent posts from our blog.

Here's the query for that:

~~~
{
  recentPosts(count: 5) {
    title
  }
}
~~~

Once you have invoked, you'll be able to retrieve five recent posts from our blog. Try to change the `count` and see how it behaves.

Here, `count` is a defined argument for the `recentPosts` root query field. You can inspect that from the Docs section of our [GraphQL Sandbox](https://sandbox.learngraphql.com/).

### Arguments for nested fields

Just as in the top-level field arguments, you can add arguments for nested fields as well. As an example, we can limit the number of comments we can retrieve from our schema.

Let's try that by doing a simple task.

Get two recent posts from our blog. But limit the maximum number of comments to 1 for each of those posts. So, what'll be the query?

**Query 1**
~~~
{
  recentPosts(count: 2) {
    title,
    comments(count: 1) {
      content
    }
  }
}
~~~


**Query 2**
~~~
{
  recentPosts(count: 2) {
    title,
    comments(1) {
      content
    }
  }
}
~~~

**Query 3**
~~~
{
  recentPosts(count: 2) {
    title,
    comments(limit: 1) {
      content
    }
  }
}
~~~

**Query 4**
~~~
{
  recentPosts(count: 2) {
    title,
    comments(max: 1) {
      content
    }
  }
}
~~~

  - Query 1
  - Query 2
  - **Query 3**
  - Query 4

*****

```
id:     multiple-fields
type:   text
points: 10
```

## Multiple fields

In a single GraphQL query, we can write as many root query fields as we need. In the server, all these fields will be processed in parallel and will give you the result as a whole.

Let's have a look at an example. Here we are trying to get both authors and the latest post at once.

~~~
{
  latestPost {
    title
  },
  
  authors {
    name
  }
}
~~~

Just like, you can add any number of root query fields. Play with it.

*****

```
id:     assigning-result-to-a-variable
type:   text
points: 10
```

## Assigning a result to a variable

For a moment, let's have a look at the results for the query relating to the previous step:

~~~
{
  "data": {
    "latestPost": {
      "title": "New Feature: Tracking Error Status with Kadira"
    },
    "authors": [
      {
        "name": "Arunoda Susiripala"
      },
      {
        "name": "Pahan Sarathchandra"
      },
      {
        "name": "Kasun Indi"
      }
    ]
  }
}
~~~

Let's imagine you want use the same root query field multiple times in a single query. As an example, let's say we need to get `name` and `_id` of all the available authors as two results.

So naturally, we'll write a query like this:

~~~
{
  latestPost {
    title
  },
  
  authors {
    name
  },
  
  authors {
    _id
  }
}
~~~

Try to invoke this. Then, you'll get something like this:

~~~
{
  "data": {
    "latestPost": {
      "title": "New Feature: Tracking Error Status with Kadira"
    },
    "authors": [
      {
        "name": "Arunoda Susiripala",
        "_id": "arunoda"
      },
      {
        "name": "Pahan Sarathchandra",
        "_id": "pahan"
      },
      {
        "name": "Kasun Indi",
        "_id": "indi"
      }
    ]
  }
}
~~~

But that's not what we wanted. 

That's where assigning the result into a variable is useful. 

Try to run this query:

~~~
{
  latestPost: latestPost {
    title
  },
  
  authorNames: authors {
    name
  },
  
  authorIds: authors {
    _id
  }
}
~~~

We'll get what we wanted.

*****

```
id:     finally
type:   mcq
points: 5
```

## You are awesome

Now you've a pretty good idea about GraphQL. You have learned some useful core GraphQL features. With this knowledge you can interact with our schema and try to explore the dataset in our blog.

What do you think about GraphQL?

  - Really, this is GraphQL?
  - Hmm, REST is much simpler.
  - I was expecting something like SQL. This is okay.
  - **Freaking Cool.**