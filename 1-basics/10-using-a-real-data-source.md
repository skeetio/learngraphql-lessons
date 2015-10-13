```
name:  Using A Real Data Source
bulletPackage: free
```

We have played a lot with GraphQL. In all cases, we were using in-memory data sources. But in the real world, we need to use real data sources behind a GraphQL schema.

That's what we are going to do in this lesson. We'll be using MongoDB as our data source. At the end, we'll show you how to integrate other data sources as well.

Let's get started!

*****

```
id:     setting-up
type:   text
points: 5
```

## Setting up

First you need to have a MongoDB database. I suggest that you  [download](https://www.mongodb.org/downloads) and run MongoDB locally. If that's not possible, you can use any cloud MongoDB solution.

Then, clone our GraphQL Sandbox:

~~~
git clone https://github.com/kadirahq/graphql-blog-schema.git
cd graphql-blog-schema
~~~

Then, check out the `server-side-schema` branch.

~~~
git checkout server-side-schema
~~~

Then, install dependencies and start it with:

~~~
npm install
npm start
~~~

That'll start our sandbox in <http://localhost:3000>

---

This version of sandbox is a bit different from the previous versions. Earlier, we defined our schemas on the client side. But now we are defining it inside the server. 

For that, we are using the [express-graphql](https://github.com/graphql/express-graphql) project.

#### Schema file

In this repo, our schema file is located in `server/schema.js`. We've already defined a root query field called `authors` and a mutation called `createAuthor`. 

If you followed the previous lessons, these should be familiar to you.

*****

```
id:     promises
type:   text
points: 10
```

## Async / promises

In the resolve function, earlier we simply return a value. Since they are in the memory, this doesn't cause a problem. But now, when we are accessing a real data source, we have to perform some async operations. Here, returning a value right away is not possible.

That's where [ES2015 promises](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise) to help us. We can return a promise from the resolve function. Then GraphQL knows how to process them in efficient ways.

### Accessing MongoDB

To access MongoDB, we are going to use a promise-enabled MongoDB driver called [promised-mongo](https://www.npmjs.com/package/promised-mongo). It's already installed in the repo.

*****

```
id:     implement-the-mutation
type:   mcq
points: 20
```

## Implementing a mutation

First we need to create a connection to our Mongo database. To do that, let's add this code on top of the `schema.js`:

~~~
const mongo = require('promised-mongo');
// You can use any MONGO_URL here, whether it's locally or on cloud.
const db = mongo('mongodb://localhost/mydb');
const authorsCollection = db.collection('authors');
~~~

Now it's time to write the logic inside our mutation `createAuthor`. For that, we will be using the `promised-mongo` package to promise an API.

So, this will be our mutation with mongo-related code:

~~~
const Mutation = new GraphQLObjectType({
  name: "Mutations",
  fields: {
    createAuthor: {
      type: Author,
      args: {
        _id: {type: new GraphQLNonNull(GraphQLString)},
        name: {type: new GraphQLNonNull(GraphQLString)},
        twitterHandle: {type: GraphQLString}
      },
      resolve: function(rootValue, args) {
        let author = _.clone(args);
        return authorsCollection.insert(author)
          .then(_ => author);
      }
    }
  }
});
~~~

Now you can invoke a mutation from our GraphQL Sandbox like this:

~~~
mutation _ {
  createAuthor(
    _id: "john",
    name: "John Carter"
  ) {
    name
  }
}
~~~

Then you'll get the following result:

~~~
{
  "data": {
    "createAuthor": {
      "name": "John Carter"
    }
  }
}
~~~

---

Now it's time for a simple task.

Try to invoke the same mutation again. What result did you get?

  - It was successful as usual.
  - Got an empty response.
  - Got an error stating that the insert had been forbidden.
  - **Got an error citing a duplicate key error.**

*****

```
id:     implement-the-root-query-field
type:   mcq
points: 20
```

## Implementing the root query field

Now we've a way to add new authors. Let's implement the `authors` query field. 

So, this will be our root query type with MongoDB integration.

~~~
const Query = new GraphQLObjectType({
  name: "RootQuery",
  fields: {
    authors: {
      type: new GraphQLList(Author),
      resolve: function() {
        return authorsCollection.find().toArray();
      }
    }
  }
});
~~~

Then you can try to retrieve authors from the GraphQL Sandbox with:

~~~
{
  authors {
    name
  }
}
~~~

You should be able to get the name of all the authors in our Mongo database.

---

This is working pretty well, but we've a little problem.
See if you can spot it:

  - **It can’t do field filtering in MongoDB level.**
  - It can't limit the result.
  - It can't sort the result.
  - There is no such issue.

*****

```
id:     field-filtering
type:   text
points: 10
```

Even though we don't have sort and limit functionalities, we can easily implement them by passing some arguments. But how can we filter the fields we mentioned in the query?

That's what we are going to do here.

## Field filtering

There is no direct way of retrieving fields from the resolve function. But there are ways. Have a look at this modified resolve function:

~~~
const Query = new GraphQLObjectType({
  name: "Queries",
  fields: {
    authors: {
      type: new GraphQLList(Author),
      resolve: function(rootValue, args, info) {
        let fields = {};
        let fieldASTs = info.fieldASTs;
        fieldASTs[0].selectionSet.selections.map(function(selection) {
          fields[selection.name.value] = 1;
        });
        return authorsCollection.find({}, fields).toArray();
      }
    }
  }
});
~~~

Here we are using the info argument(3rd argument) passed to the resolve function. It has the parsed [AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree) of the query. Using that, we can retrieve fields. That's what we've done in the above code.

*****

```
id:     whats-more
type:   text
points: 5
```

## What's More

Even by just using `promised-mongo`, we could integrate a Mongo database with a GraphQL schema pretty easily. But this is just one way of doing it. There are a couple of other ways in which you can integrate existing data sources into GraphQL. Let me show you some of them:

* [graffiti](https://github.com/RisingStack/graffiti) - Use your existing Mongoose schemas with GraphQL
* [graphql-sequelize](https://github.com/mickhansen/graphql-sequelize) - Use existing [Sequelize ORM](http://docs.sequelizejs.com/en/latest/) schemas with GraphQL
* [graphql-bookshelf](https://github.com/brysgo/graphql-bookshelf) - Use existing [BookshelfJS](http://bookshelfjs.org/) models.

This is just the beginning. There will more and more ways to integrate different kinds of data sources with GraphQL.