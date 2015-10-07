```
name:  Building Schema: Defining Queries
bulletPackage: free
```

In all the previous lessons, we simply invoke queries agaist a Schema which is already defined. We didn't worry about looking into the Schema because, we wanted to master the GraphQL query language.

Now, we've a better understanding of GraphQL and it's query languge. Now it's the best time to start writing GraphQL Schemas.

Let's get started!

*****

```
id:     setting-up
type:   text
points: 5
```

## Setting Up

First we need to download our GraphQL Sandbox and use it to build the Blog schema we've used earlier.

Clone this repo:

~~~
git clone https://github.com/kadirahq/graphql-blog-schema.git
~~~

Then checkout `build-schema` branch:

~~~
git checkout build-schema
~~~

Now install dependencies with:

~~~
npm install
~~~

Start the sandbox with:

~~~
npm start
~~~

Now you can open the sandbox in `http://localhost:3000`. Try to play with it.

![](https://cldup.com/MnoG2RvAja.png)

*****

```
id:     inspecting-the-schema
type:   mcq
points: 25
```

# Inspecting The Schema

In our Sandbox, it comes with Schema which has a simple query called `echo`. It accepts a message as an arguments and return it.

Try running this query:

~~~
{
  recivedMessage: echo(message: "Hello")
}
~~~

You'll get a result like:

~~~
{
  "data": {
    "recivedMessage": "recieved Hello"
  }
}
~~~

So, let's see how we created the Schema for this. For that open the following file in the cloned repo:

* File: `src/schema.js`

It's a self documented file and used [ES2015](https://github.com/lukehoban/es6features) to write the app. We've also used ES2015 [modules](https://github.com/lukehoban/es6features#modules).

At the end of the file, we've defined our Schema like this:

~~~
const Schema = new GraphQLSchema({
  query: Query
});
~~~

Here we create a new GraphQL Schema object and assign the root query object. This is what you've seen as `BlogQueries` in the documentation tab of our GraphQL Sandbox. Then let's see what's in this `Query` object. 


~~~
const Query = new GraphQLObjectType({
  name: 'BlogQueries',
  description: "Root of the Blog Schema",
  fields: () => ({
    echo: {
      type: GraphQLString,
      description: "Echo what you enter",
      args: {
        message: {type: GraphQLString}
      },
      resolve: function(root, {message}) {
        return `recieved ${message}`;
      }
    }
  })
});
~~~

Here we are creating new GraphQL Object type with a name called `BlogQueries`. We've also given a description for that type too. In the GraphQL Object type, we can define fields. 

We do define them as mention in the above code. We use a function for that. This allow us to use types which will be defined later in your code.

So, according to the above we've a field called `echo`. It's type is a GraphQL String.

> You can look at the top of the file to see common types we can use

Then we define our arguments. In this case, we've only one argument. You can give a description to the type if you need. Here's how to do it.

~~~
{
  ...
  args: {
    message: {type: GraphQLString, description: "Give me a message"}
  }
  ...
}
~~~

Next, we've the resolve function which is place we can implement the logic for this query and return a value of String.

~~~
{
  ...
  resolve: function(root, {message}) {
    return `recieved ${message}`;
  }
  ...
}
~~~

Here in the second parameter of the resolve function, we get our arguments. We used ES2015 [destructuring](https://github.com/lukehoban/es6features#destructuring) feature to get the message directly.

---

Now here's a simple task for you. Try to return following object from the `resolve` function. 

~~~
{aa: 10};
~~~

Then invoke a query to get the `echo` field and see what's the response.

Select the result:

  - {aa: 10}, // Object
  - {"aa": 10} // JSON string
  - **[object Object]**
  - There was an error

*****

```
id:     answer-inspecting-the-schema
type:   text
points: 5
```

We got the result as **[object Object]**. Here's the reason.

We defined the `echo` field as GraphQLString type. Then, GraphQL tries to get a string from the object we provide. Normally, `[object Object]` is the value get when we called the `toString()` method of an object.

Try to return other values and see the response. Also, try to change the type of the `echo` field and understand how this works.

*****

```
id:     defining-the-post-type
type:   mcq
points: 25
```

## Defining The Post Type

Now we are going to define an actual type and write a proper root query field. Let's try to implement the `Post` type in our blog and create a Root Query field `getPosts`.

Here's the minimal version of the `Post` type in our blog. 

~~~
const Post = new GraphQLObjectType({
  name: "Post",
  description: "This represent a Post",
  fields: () => ({
    _id: {type: new GraphQLNonNull(GraphQLString)},
    title: {type: new GraphQLNonNull(GraphQLString)},
    content: {type: GraphQLString}
  })
});
~~~

In this type, we make `_id` and `title` required and make `content` optional. In GraphQL, by default all the fields we define are optional. If we need to make them required, we need to mention it explicitly.

Add this type our `schema.js` file. You can define this in the file, marked as "DEFINE YOUR TYPES BELOW".

Now it's time to create the `getPosts` field in the `Query` object. Here it is:

~~~
const Query = new GraphQLObjectType({
  name: 'BlogQueries',
  description: "Root of the Blog Schema",
  fields: () => ({
    getPosts: {
      type: new GraphQLList(Post),
      resolve: function() {
        return PostsList;
      }
    }
  })
});
~~~

Here `getPosts` field is list of `Post` types. In the resolve function we simply return the actual data for PostList. `PostList` is defined in the `src/data/posts.js` file and you can inspect it.

> Here's how `schema.js` file looks like once you added these changes: https://gist.github.com/arunoda/2cbba32de83bfa96099d

Now visit to the locally running GraphQL Sanbox and try to query `getPosts` field.

> You may need to reload `http://localhost:3000` once you edited the schema.js. Otherwise, autocompletion may not work.

---

Now you've to do a simple task.

Return following value from the `resolve` function instead of the `PostList`.

~~~
[{}];
~~~

Now try to invoke following query:

~~~
{
  posts: getPosts {
    title
  }
}
~~~

What's the result you got?

  - Empty value for the title
  - **Error as: Cannot return null for non-nullable field Post.title.**
  - Error as: Needs Post.title in the return value
  - "null" value for the title

*****

```
id:     answer-defining-the-post-type
type:   text
points: 5
```

## Defining Default Values

You'll get an error saying "Cannot return null for non-nullable field Post.title." This is because, we define the title field as a requried value.

But sometimes, in our dataset we might have some posts with empty values for title. That's due to some inconsitancy in our dataset. But, that shouldn't throw an error.

So, in this case we've two options. Either make `title` as an optional value. Or define a default value like this:

~~~
const Post = new GraphQLObjectType({
  name: "Post",
  description: "This represent a Post",
  fields: () => ({
    _id: {type: new GraphQLNonNull(GraphQLString)},
    title: {
      type: new GraphQLNonNull(GraphQLString),
      resolve: function(post) {
        return post.title || "Does not exists";
      }
    },
    content: {type: GraphQLString}
  })
});
~~~

Have a look at the `resolve` function of the `title` field. So, as the first argument of we'll get post object returned by the `getPosts`'s resolve function.

Then we can customize it and returned the value as we want. That's exactly what we are doing here.

*****
```
id:  defining-rested-fields
type: mcq
points: 20
```

## Defining Nested Fields

Now we are trying to define a nested field into our type. For an example, let's try to add a author field to our `Post` type.

For that, first we need to create the `Author` type.

~~~
const Author = new GraphQLObjectType({
  name: "Author",
  description: "This represent an author",
  fields: () = ({
    _id: {type: new GraphQLNonNull(GraphQLString)},
    name: {type: GraphQLString}
  })
});
~~~

Then let's define the author field in our `Post` type.

~~~
const Post = new GraphQLObjectType({
  name: "Post",
  description: "This represent a Post",
  fields: () => ({
    ...
    author: {
      type: Author,
      resolve: function(post) {
        return _.find(AuthorsList, a => a._id == post.author);
      }
    }
  })
});
~~~

In our `PostsList` each object as a string value for the "author". That's the `_id` of the author which is stored in `AuthorsList`. 

So, `author` field's resolve function simply find the correct author and return him. We've also defined the type of the `author` field with a type of `Author`.

> Here's the final `schema.js` file looks like: https://gist.github.com/arunoda/c29128be2c5e979475ec

---

Now invoke following query:

~~~
{
  posts: getPosts {
    title,
    author {
      name
    }
  }
}
~~~

Find out, how many posts created by author "Kasun Indi"?

  - **1**
  - 2
  - 3
  - 4

*****
```
id:  finally
type: text
points: 5
```

## Finally

Now you know how to define a GraphQL Schema and resolve fields. We've also discussed on how to write nested fields. You can define as many as nested fields you like.

That's how you build your dataset as a graph. We'll explore more in upcoming lessons.