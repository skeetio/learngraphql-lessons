```
name:  Query Variables
bulletPackage: free
```

When we are writing some GraphQL queries, it's very common to use arguments. This is how we normally do it by hardcoding them.

~~~
{
  recentPosts: recentPosts(count: 10) {
    title
  }
}
~~~

Let's say, we need to change count as `20`. Then, we need to generate this query again. This can be hard to manage in a real app.

With this approach, there is no possible way to set some of these arguments inside the server. Normally, `userId` is an argument we **must** set from the server side.

But fortunately, GraphQL has a better way to pass values to the queries rather than hard coding them inside the query. That's the job of **query variables**.

Let's see how to use query variables.

*****

```
id:     named-queries
type:   text
points: 5
```

## Named Queries

In previous lessons, we defined GraphQL queries using the shorthand syntax. See:

~~~
{
  recentPosts: recentPosts(count: 10) {
    title
  }
}
~~~


But, this is how to write it with the complete syntax.

~~~
query getFewPosts {
  recentPosts: recentPosts(count: 10) {
    title
  }
}
~~~

Here we used the `query` keyword and gave a name to the query. You can give any name you need. In the shorthand version, we simply ignored `query getFewPosts` part from the complete syntax.

> We need to write queries with the complete syntax to use query variables.

*****

```
id:     using-query-variables
type:   mcq
points: 20
```

## Using Query Variables

Let's say, we need to use a query variable for the `count` argument in the following query. 

~~~
query getFewPosts {
  recentPosts: recentPosts(count: 10) {
    title
  }
}
~~~

Then this is how we could write it.

~~~
query getFewPosts($postCount: Int!) {
  recentPosts: recentPosts(count: $postCount) {
    title
  }
}
~~~

Here we've defined a query variable called `postCount` with the type of `Int`. We also marked it as required by using `!` at the end.

> We had to make `postCount` query variable required. That's because `count` argument for `recentPosts` is a required argument.

### How To Provide Query Variables

So, you might have a problem with how to provide query variables?

We normally provide query variables when we are executing the query in the server. For now, we are not going to worry about how exactly we do it. We'll do it in another lesson.

In our GraphQL Sandbox, we've a separate tab where we can write query variables. See following image:

![](https://cldup.com/EJVOyG42CJ.png)

Now you can try to use Query Variables in the GraphQL Sandbox by adding following JSON into this.

~~~
{
  "postCount": 2
}
~~~

---

Now it's time for a simple question. What happen if you didn't provide any value to the query variable `postCount`. To do that, remove `postCount` field from the "QUERY VARIABLES" tabs in the GraphQL Sandbox.

  - **It throws an error saying: Variable "$postCount" of required type "Int!" was not provided.**
  - It throws an error saying: Cannot run `getFewPosts` operator without one of more required query variable.
  - It throws an error saying: Cannot run `getFewPosts` operator without the `postCount` query variable.
  - There was no issue.

*****

```
id:     use-query-variable-anywhere
type:   text
points: 5
```

## Use Query Variables Anywhere

Once defined, we can use a query variable anywhere in our queries. This is how we can use it for a field argument.

~~~
query getFewPosts($postCount: Int!, $commentCount: Int) {
  recentPosts: recentPosts(count: $postCount) {
    title,
    comments(limit: $commentCount) {
      content
    }
  }
}
~~~

We can also use it inside a fragment:

~~~
query getFewPosts($postCount: Int!, $commentCount: Int) {
  recentPosts: recentPosts(count: $postCount) {
    title,
    ...comments
  }
}

fragment comments on Post {
  comments(limit: $commentCount) {
    content
  }
}
~~~

Just like this, you can use it for mutations as well.

*****

```
id:     input-types
type:   mcq
points: 15
```

## Input Types

In our GraphQL Schema, we've few different types including built-in types like "Int", "String" and custom types such as "Post", "Author". But, for query variables we can't use all of them.

We can only use a subset of types which are known as **Input Types**.

Normally input types includes,

* Scalers like Int, String, Float, Boolean
* Enums
* Arrays of above types

To learn more about Input Types [refer](https://facebook.github.io/graphql/#sec-Input-Types) the GraphQL Spec.

---

Now it's time for a simple task.

Have a look at this query:

~~~
query getFewPosts($category: Category) {
  posts: posts(category: $category) {
    title
  }
}
~~~

Here we are trying to filter posts by a category. Here `category` argument expects an value of Category enum. We've few categories in our blog and `PRODUCT` is one of them. So, you can define query variables for this query as:

~~~
{ 
  "category": "PRODUCT"
}
~~~

Okay, here's the question for you. 

From the list below, select a category which is not exists in our blog:

  - METEOR
  - OTHER
  - USER_STORY
  - **GRAPHQL**

*****

```
id:     at-the-end
type:   text
points: 5
```

## At The End

Now we know almost everything about query variables and use them in different ways. When you are using GraphQL in the client side of your app, query variables will be pretty useful.