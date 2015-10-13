```
name:  Defining Mutations
bulletPackage: free
```

In GraphQL, mutations are the way to allow GraphQL clients(or external parties) to modify your dataset.

Defining mutations is very similar to defining queries. The only difference is how you implement the logic inside the mutation.

Let's define some mutations.

*****

```
id:     setting-up
type:   text
points: 5
```


## Setting up

> This lesson is a continuation of the previous lesson [Defining Queries](/basics/defining-queries). We will assume you've already completed that lesson.

Clone our GraphQL Sandbox:

~~~
git clone https://github.com/kadirahq/graphql-blog-schema.git
~~~

Then check out the `defining-mutations` branch:

~~~
cd graphql-blog-schema
git checkout defining-mutations
~~~

Now, install the dependencies and start the sandbox:

~~~
npm install
npm start
~~~

This will start a local version of our GraphQL Sandbox and you can access it from <http://localhost:3000>

![](https://cldup.com/MnoG2RvAja.png)

This sandbox has a schema that looks similar to what we used in the first few lessons. But it's a scaled-down version. (It's very similar to what we built in the previous lesson.)

You can look at it by inspecting the `src/schema.js` in the cloned repo. That's the place where we are also going to write mutation-related code.

*****

```
id:     hello-muatations
type:   mcq
points: 20
```

## Hello mutations

Now we are going to write our first mutation. It's a mutation to add a new post to our blog. So, this is the mutation request we are going to invoke:

~~~
mutation insertFirstPost() {
  post: createPost(
    title: "GraphQL is Awesome",
    content: "Yep, it's purely awesome."
  ) {
    _id,
    title
  }
}
~~~

So, defining a mutation is just like defining a root query field. This is how to do it:

~~~
const Mutation = new GraphQLObjectType({
  name: "BlogMutations",
  description: "Mutations of our blog",
  fields: () => ({
    createPost: {
      type: Post,
      args: {
        title: {type: new GraphQLNonNull(GraphQLString)},
        content: {type: new GraphQLNonNull(GraphQLString)}
      },
      resolve: function(source, args) {
        let post = _.clone(args);
        // Generate the _id
        post._id = Date.now() + Math.ceil(Math.random() * 9999999);
        // Assign a user
        post.author = "arunoda";

        // Add the Post to the data store
        PostsList.push(post);

        // return the new post.
        return post;
      }
    }
  })
});
~~~

Here in the `resolve` function, we simply add a new post to the `PostList` array and return it.

Now we need to assign this to our schema. So, our updated schema looks like this:

~~~
const Schema = new GraphQLSchema({
  query: Query,
  mutation: Mutation
});
~~~

That's it. Now try to invoke the following mutation:

~~~
mutation insertFirstPost {
  post: createPost(
    title: "GraphQL is Awesome",
    content: "Yep, it's purely awesome."
  ) {
    _id,
    title
  }
}
~~~

You can check whether this post has been added or not by querying our schema.

--- 

Now we've a simple task for you. 
Try to invoke the following query.

~~~
mutation insertFirstPost {
  post: createPost(
    title: "GraphQL is Awesome"
  ) {
    _id,
    title
  }
}
~~~

What result did you get?

  - Successfully added the post.
  - Successfully added the post, but there is a warning stating that a content field is required.
  - **Got an error stating that a content field is required.**
  - Nothing happened, there was a JavaScript console error.

*****

```
id:     answer-hello-muatations
type:   text
points: 5
```

Once we invoke that mutation, we'll get an error stating that a content field is required. That's because we've defined our arguments as required. See:

~~~
const Mutation = new GraphQLObjectType({
  ...
      args: {
        title: {type: new GraphQLNonNull(GraphQLString)},
        content: {type: new GraphQLNonNull(GraphQLString)}
      },
  ...
});
~~~

*****

```
id:     finally
type:   text
points: 5
```

## Finally

Now we understand how to define a mutation in our schema. As you know already, a mutation is very similar to a root query field, but it mutates your underlining dataset.

And after the mutation, it is very important to return the modified document. You can learn more about this in a later lesson and while learning [Relay](https://facebook.github.io/relay/).