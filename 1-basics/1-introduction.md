```
name: Introduction to GraphQL
bulletPackage: free
```

[GraphQL](http://graphql.org/) is an **application layer query language** from Facebook. With GraphQL, you can define your backend as a well-defined [graph](https://goo.gl/9JgOxd)-based schema. Then client applications can query your dataset as they are needed. 

![GraphQL Overview](https://cldup.com/ysnmIMhqRU.png)

So, you don't need to change your backend for data requirement changes in client apps. This simply solves one of the biggest problems in managing REST API.

GraphQL also allows client applications to batch and fetch data very efficiently. For an example, have a look at the following GraphQL query:

~~~
{
  latestPost {
    _id,
    title,
    content,
    author {
      name
    },
    comments {
      content,
      author {
        name
      }
    }
  }
}
~~~

This is a GraphQL query to fetch data for a blog post with comments and author information. Here's the result of the above query:

~~~
{
  "data": {
    "latestPost": {
      "_id": "03390abb5570ce03ae524397d215713b",
      "title": "New Feature: Tracking Error Status with Kadira",
      "content": "Here is a common feedback we received from our users ...",
      "author": {
        "name": "Pahan Sarathchandra"
      },
      "comments": [
        {
          "content": "This is a very good blog post",
          "author": {
            "name": "Arunoda Susiripala"
          }
        },
        {
          "content": "Keep up the good work",
          "author": {
            "name": "Kasun Indi"
          }
        }
      ]
    }
  }
}
~~~

If you are using REST, you need to invoke multiple API calls to gather this information.

**GraphQL is a specification.**

So, it can be used with any platform and any language. It has a reference implementation on [JavaScript](https://github.com/graphql/graphql-js), maintained by Facebook. There are well-maintained community [implementations](https://github.com/chentsulin/awesome-graphql#table-of-contents) exists in many languages. 

> Here's the specification: <http://facebook.github.io/graphql/>

Once you have tried GraphQL, you will want to use it with every one of your projects.