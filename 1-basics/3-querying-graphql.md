```
name:  Querying GraphQL
bulletPackage: free
```

GraphQL is an application layer query language. So, querying is one of the major parts of it. In this lesson, we are going to start with how to query against a graphql server and get familiar with the graphql query syntax.

We'll be querying a data set for a typical **blog app** (as we decusssed earlier). For now, don't worry about how the GraphQL server works and how the schema is implemented. For now, we just focus on the query syntax and play with it.

At the end of the lesson, you'll have a good understanding on how to write GraphQL queries.

Let's get started!

*****

```
id:     hello-graphql
type:   text
points: 5
```

## Hello GraphQL

Let's write our first GraphQL query. It'll get the latest post's title and summary. To invoke that, open our GraphQL Sandbox.

> Go through our Setting Up lesson to get familiar with the GraphQL Sandbox. With that, you'll be able to learn about our schema and get familiarized with the Sandbox UI.

Then run this query:

~~~
{
  getLatestPost {
    title,
    summary
  }
}
~~~

As the result, you'll something like this:

~~~
{
  "data": {
    "getLatestPost": {
      "title": "New Feature: Tracking Error Status with Kadira",
      "summary": "Lot of users asked us to add a feature to set status for errors in the Kadira Error Manager. Now, we've that functionality."
    }
  }
}
~~~

Hurray! We just invoked our first GraphQL query.

With this query, we asked our GraphQL server with the query `getLatestPost`. Also, we only asked for the `title` and `summary` fields only. 

Now, try to query and get more fields from the query. 

*****

```
id:     get-authors
type:   mcq
points: 15
```

## Get Authors

Here's your first task. 

In our GraphQL Schema, we have a query called `getAuthors`. Try to invoke it. Then select the author who is not in our blog.

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

## Nested Querying

In GraphQL, we can query in nested fashion. For an example, from a single GraphQL query we can get all the posts and their comments as well.

Invoke this query:

~~~json
{
  getPosts {
    title,
    summary,
    comments {
      content
    }
  }
}
~~~

Just like that, we can add sub queries(we also called them as fields) as needed.

Now, we need to get the name of the author for each post as well. So, what'll be the GraphQL query for that:

**Query 1**
~~~
{
  getPosts {
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
  getPosts {
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
  getPosts {
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
  getPosts {
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

We can also use arguments in GraphQL queries. To witness that, let's get recent five posts from our blog:

Here's the query for that:

~~~
{
  getRecentPosts(count: 5) {
    title
  }
}
~~~

Once you invoked, you'll be able to see 5 posts. Try to change the count and see.

Here count is a defined argument for `getRecentPosts` query. You can inspect that from the Docs section of our GraphQL Sandbox.

### Field Level Arguments

Just like the top level query arguments, you can add arguments for fields as well. In our schema, we can limit the number of comments we can get. You can look at the argument information by looking at the docs or using auto completion feature.

So, here's a simple task for you.

Get the recent 2 posts from our blog. But limit the maximum number of comments to 1. So, what'll be the query?

**Query 1**
~~~
{
    getRecentPosts(count: 5) {
    comments(count: 1) {
      content
    }
  }
}
~~~


**Query 2**
~~~
{
    getRecentPosts(count: 5) {
    comments(1) {
      content
    }
  }
}
~~~

**Query 3**
~~~
{
    getRecentPosts(count: 5) {
    comments(limit: 1) {
      content
    }
  }
}
~~~

**Query 4**
~~~
{
    getRecentPosts(count: 5) {
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
id:     multiple-queries
type:   text
points: 10
```

## Multiple Queries

In a single request to the GraphQL server, we can write as many as queries we need. In the server all these queries will be processed in parrarel and give you the result as a whole.

> It's totally independant for the server implements to decide how to execute queries. But, GraphQL authors suggest to run queries in parallel.

Let's have a look at an example. Here we are trying to get both authors and the latest post at once.

~~~
{
  getLatestPost {
    title
  },
  
  getAuthors {
    name
  }
}
~~~

Just like, you can add any number of queries you want. Play with it.

*****

```
id:     assigning-result-to-a-variable
type:   text
points: 10
```

## Assigning Result to a Variable

For a momennt, let's have a look at the result for the previous step's query:

~~~
{
  "data": {
    "getLatestPost": {
      "title": "New Feature: Tracking Error Status with Kadira"
    },
    "getAuthors": [
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

Here in the result, multiple queries are grouped by their name. But let's say you want use the same query multiple times. For an example, we need to use authors' `name` and `_id` seperately. 

So natuarally, we'll write a query like this:

~~~
{
  getLatestPost {
    title
  },
  
  getAuthors {
    name
  },
  
  getAuthors {
    _id
  }
}
~~~

Try to invoke this. You can't get what you wanted. That's where assiging the result into a varibale comes handy. 

Try to run this query:

~~~
{
  latestPost: getLatestPost {
    title
  },
  
  authorNames: getAuthors {
    name
  },
  
  authorIds: getAuthors {
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

## You are Awesome

Now you've a pretty good idea about GraphQL and you've learned some useful core GraphQL features. With this knowledge you can interact with our schema and try to explore the dataset in our blog schema.

What do you think about GraphQL?

  - Really, this is GraphQL?
  - Hmm, REST is much simpler
  - I was expecting something like SQL. This is worst.
  - **Freaking Cool**