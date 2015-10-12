```
name:  Using A Real Data Source
bulletPackage: free
```

We played a lot with GraphQL. We were using in-memory data sources everytime. But in real world, we need to use real data sources behind a GraphQL schema.

That's what we are going to do in this lesson. We'll be using MongoDB as our data source. At the end, We'll show you how to integrate other data sources as well.

Let's get started!

*****

```
id:     setting-up
type:   text
points: 5
```

## Setting Up

First you need to have a MongoDB database. I suggest you to [download](https://www.mongodb.org/downloads) and run mongodb locally. If that's not possible, you can use any cloud MongoDB solution.

Then, clone our GraphQL Sandbox:

~~~
git clone https://github.com/kadirahq/graphql-blog-schema.git
cd graphql-blog-schema
~~~

Then, checkout the `server-side-schema` branch.

~~~
git checkout server-side-schema
~~~

Then install dependencies and start it with:

~~~
npm install
npm start
~~~

That'll start our sandbox in <http://localhost:3000>

---

This version of sandbox is bit different from the previous versions. Earlier, we defined our schemas in the client side. But now, we are doing it inside the server. 

For that, we are using the [express-graphql](https://github.com/graphql/express-graphql) project.

#### Schema File

In this repo, our schema file is located in `server/schema.js`. We've already defined a root query field called `authors` and a mutation called `createAuthor`. 

If you followed previous lessons, those should be familar to you. 

*****

```
id:     promises
type:   text
points: 10
```

## Async / Promises

In the resolve function, earlier we simply return a value. Since they are in the memory, we didn't had any issue. But now, when we are accessing a real data source, we have to do async operations. Then returning a value right away is not possible.

That's where [ES2015 promises](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise) is going to help us. We can return a promise from the resolve function. Then GraphQL knows how to process them in efficient ways.

### Accessing MongoDB

To access MongoDB, we are going to a use a promise enabled mongodb driver called [promised-mongo](https://www.npmjs.com/package/promised-mongo). It's already installed in the repo.

*****

```
id:     implement-the-mutation
type:   mcq
points: 20
```

## Implement The Mutation

First we need to create a connection to our Mongo database. To do that, let's add this code on top of the `schema.js`.

~~~
const mongo = require('promised-mongo');
// You can use any MONGO_URL here, whether it's locally or on cloud.
const db = mongo('mongodb://localhost/mydb');
const authorsCollection = db.collection('authors');
~~~

Now it's time to write the logic inside our mutation `createAuthor`. For that, we will be using `promised-mongo` package's promise API.

So, this will be our mutation with mongo related code:

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

Then you'll get following as the result:

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

Try to invoke the same mutation again. What was the result?

  - It was successful as usual.
  - Got an emply response.
  - Got an error mentioning about insert has been forbidden.
  - **Got an error mentioning about a duplicate key error.**

*****

```
id:     implement-the-root-query-field
type:   mcq
points: 20
```

## Implement The Root Query Field

Now we've a way to add new authors. Let's implement the `authors` query field. 

So, this will be our root query type with the MongoDB integration.

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

Then you can try to get authors from the GraphQL Sandbox with:

~~~
{
  authors {
    name
  }
}
~~~

You should get the name of all the authors in our Mongo database.

---

This is a working pretty well, but we've a little problem.
Could you spot that?

  - **It can't filter the fields only we asked**
  - It can't limit the result.
  - It can't sort the result.
  - There is no such issue.

*****

```
id:     field-filtering
type:   text
points: 10
```

Even though, we don't have sort and limit functionalities, we can easily implement them by passing some arguments. But how can we filter fields we mention in the query?

That's what we going to do here.

## Field Filtering

There is no direct way to get fields from the resolve function. But, there are ways. Have a look at this modified resolve function.

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

Here we are using the info argument(3rd argument) passed to the resolve function. It has the parsed [AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree) of the query. Using that, we can get fields. That's what we've done in the above code.

*****

```
id:     whats-more
type:   text
points: 5
```

## What's More

Even by just using `promised-mongo` we could integrate a Mongo databse with a GraphQL schema pretty easily. But this is just a one way. There are couple of other ways you can integrate existing data sources into GraphQL. Let me show you some of them.

* [graffiti](https://github.com/RisingStack/graffiti) - Use your existing Mongoose schemas with GraphQL
* [graphql-sequelize](https://github.com/mickhansen/graphql-sequelize) - Use existing [Sequelize ORM](http://docs.sequelizejs.com/en/latest/) schemas with GraphQL
* [graphql-bookshelf](https://github.com/brysgo/graphql-bookshelf) - Use existing [BookshelfJS](http://bookshelfjs.org/) models

This is just the begining. There will more and more ways to integrate different kinds of data sources with GraphQL.