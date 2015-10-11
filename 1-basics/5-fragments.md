```
name:  Fragments
bulletPackage: free
```

In GraphQL, fragments are the way to group commonly used fileds and reuse them. 

For an example, let's say we need to get some authors in our blog. So, this is a query we can write:

~~~
{
  arunoda: author(_id: "arunoda") {
    _id,
    name,
    twitterHandle
  },
  pahan: author(_id: "pahan") {
    _id,
    name,
    twitterHandle
  },
  indi: author(_id: "indi") {
    _id,
    name,
    twitterHandle
  }
}
~~~

Now, we are writing the fields of the `Author` type again and again. We could use fragments to avoid that.

Let's see how to use fragments.

*****

```
id:     hello-fragments
type:   text
points: 10
```

## Hello Fragments

Have a look at this query:

~~~
{
  arunoda: author(_id: "arunoda") {
    _id,
    name,
    twitterHandle
  },
  pahan: author(_id: "pahan") {
    _id,
    name,
    twitterHandle
  },
  indi: author(_id: "indi") {
    _id,
    name,
    twitterHandle
  }
}
~~~

Here we are writing the same set of fields again and again. It would be great, if we can have a way to reuse fields. That's where fragments comes handy.

So check this query. It's the same as above, but with fragments:

~~~
{
  arunoda: author(_id: "arunoda") {
    ...authorInfo
  },
  pahan: author(_id: "pahan") {
    ...authorInfo
  },
  indi: author(_id: "indi") {
    ...authorInfo
  }
}

fragment authorInfo on Author {
  _id,
  name,
  twitterHandle
}
~~~

Here we've defined a fragment on the `Author` type and include fields we want. Then, we can use them in queries with the spread operatior (`...`) as shown in the above query.

*****

```
id:     mixing-fragments-and-fields
type:   mcq
points: 15
```

## Mixing Fragments and Fields

We can mix fragments and fields together. For an example, have a look at this query.

~~~
{
  indi: author(_id: "indi") {
    ...requiredAuthorInfo
    twitterHandle
  }
}

fragment requiredAuthorInfo on Author {
  _id,
  name,
}
~~~

Here we are using a fragment to get `_id` and `name`. Also, we've defined `twitterHandle` field additionally.

---

So, here's a simple question for you.

What happened, if you re-defined a field exists in the fragment?

~~~
{
  indi: author(_id: "indi") {
    ...requiredAuthorInfo,
    twitterHandle,
    _id # this is already defined in the fragment
  }
}
~~~

Invoke this query and see what's the response is?

  - It throws an error saying: _id is already defined
  - **Query executed without any issues**
  - It throws an error saying: Incorrect query
  - Query executed without any issues, but it gives a warnning about the duplicate _id field.

*****

```
id:     fragments-with-nested-fields
type:   text
points: 5
```

## Fragments With Nested Fields

In a fragment, we can define nested fields as well. That's where fragments are starting to be very useful. For an example, have a look at the following query:

~~~
{
  post1: post(_id: "03390abb5570ce03ae524397d215713b") {
    ...postInfo
  },
  post2: post(_id: "0176413761b289e6d64c2c14a758c1c7") {
    ...postInfo
  }
}

fragment postInfo on Post {
  title,
  content,
  author {
    name
  },
  comments {
    content
  }
}
~~~

In this query, we are asking two posts with author name and comments. Instead of writing fields again and again, we used a fragment with nested fields.

*****

```
id:     fragments-with-nested-fragments
type:   text
points: 5
```

## Fragments with Nested Fragments

You can also use fragments inside some other fragments as well. Have a look this query:

~~~
{
  post1: post(_id: "03390abb5570ce03ae524397d215713b") {
    ...postInfo
  },
  post2: post(_id: "0176413761b289e6d64c2c14a758c1c7") {
    ...postInfo
  }
}

fragment postInfo on Post {
  title,
  content,
  author {
    ...authorInfo
  },
  comments {
    content,
    author {
      ...authorInfo
    }
  }
}

fragment authorInfo on Author {
  _id, 
  name
}
~~~

Here we've used `authorInfo` fragment inside `postInfo` fragment. 

We can also improve this query like this:

~~~
{
  post1: post(_id: "03390abb5570ce03ae524397d215713b") {
    ...postInfo
  },
  post2: post(_id: "0176413761b289e6d64c2c14a758c1c7") {
    ...postInfo
  }
}

fragment postInfo on Post {
  title,
  content,
  ...authorInfo
  comments {
    content,
    ...authorInfo
  }
}

fragment on HasAuthor {
  author {
    _id,
    name
  }
}
~~~

For that, we need to learn about interfaces. We'll do it in a different lesson in this course.

*****

```
id:     finally
type:   text
points: 5
```

## Finally

Now we've a good understanding about fragments and how we use them in GraphQL queries. You can also use fragments with mutations, since mutation is a variation of queries.

When we are talking about how to use GraphQL in a client app, fragments plays a huge role. We'll talk more about that later on.