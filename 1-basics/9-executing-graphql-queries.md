```
name:  Executing GraphQL Queries
bulletPackage: free
```

We've learned about the GraphQL query language and how to define schemas. But we didn't try to execute queries against a schema ourselves. To do this, we simply use our GraphQL Sandbox, which is built on top of Facebook's GraphiQL project.

Now it's time to learn about how to run GraphQL schemas on our own. For this lesson, we'll discuss how to do it with NodeJS (and JavaScript), but later you'll learn how to use other languages as well.

### Using the "graphql" npm module

The `graphql` npm module is the only tool(or library) you'll need to define a basic schema and execute a query against it.

Let's get started!

*****

```
id:     setting-up
type:   text
points: 5
```

## Setting up

First of all we need to create a directory in which to put our JavaScript file and npm modules.

~~~
mkdir /tmp/hello-graphql
cd /tmp/hello-graphql
~~~

We'll use ES2015 with this project, so make sure to install [babel](https://babeljs.io/) in your machine.

~~~
npm install -g babel
~~~

Then create a file called `index.js`. That's the place where we are going to define our schema.

~~~
touch index.js
~~~

Then, you can execute it with: `babel-node index.js`.

> Here we use `babel-node` because it transpile ES2015 into ES5 and executes it with any nodejs version installed on your app.

Finally, install the `graphql` npm module into this directory.

~~~
npm install graphql
~~~

*****

```
id:     defining-the-schema
type:   text
points: 5
```

### Defining the schema

Now we need to define a schema. In this schema, we've a single root query field called `echo`, which echoes what it receives.

### Importing GraphQL classes

First we need to import all basic GraphQL classes, which we use to build the schema. Next, add the following content on top of our `index.js` file:

~~~
import {
  // These are the basic GraphQL types
  GraphQLInt,
  GraphQLFloat,
  GraphQLString,
  GraphQLList,
  GraphQLObjectType,
  GraphQLEnumType,

  // This is used to create required fields and arguments
  GraphQLNonNull,

  // This is the class we need to create the schema
  GraphQLSchema,

  // This function is used execute GraphQL queries
  graphql
} from 'graphql';
~~~

Then here's our schema:

~~~
const Query = new GraphQLObjectType({
  name: 'RootQueries',
  fields: () => ({
    echo: {
      type: GraphQLString,
      args: {
        message: {type: GraphQLString}
      },
      resolve(rootValue, args) {
        return `received: ${args.message}`;
      }
    }
  })
});

const Schema = new GraphQLSchema({
  query: Query
});
~~~

Now try to run our file with:

~~~
babel-node index.js
~~~

If everything is correct, the script will complete without printing anything.

*****

```
id:     executing-a-query
type:   text
points: 5
```

## Executing a query

Now it's time to execute a query. So, let's assign our query into a variable.

> Add this content to the end of the `index.js` file:

~~~
let query = `
  {
    receivedMessage: echo(message: "Hello")
  }
`;
~~~

Then, this is how to execute this query:

~~~
graphql(Schema, query).then(function(result) {
  console.log(result);
});
~~~

Here, we use the `graphql` function from the `graphql` npm module to execute a query. It returns a promise and that's why we use `.then` to get the result.

Execute our script with:

~~~
babel-node index.js
~~~

You'll get a result like this on the console:

~~~
{ data: { receivedMessage: 'received: Hello' } }
~~~

*****

```
id:     assigning-query-variables
type:   mcq
points: 15
```

## Assigning query variables

Now we need to use query variables with our query. So, our query will look like this:

~~~
let query = `
  query getEcho($inputMessage: String) {
    receivedMessage: echo(message: $inputMessage)
  }
`;
~~~

Then, we need to provide query variables for the `graphql` function. So, here's how to do it:

~~~
graphql(Schema, query, null, {inputMessage: "Hello"}).then(function(result) {
  console.log(result);
});
~~~

Try to run our script with: `babel-node index.js`.

You'll get the expected result.

---

Now you've a task to do.

Run our query, without providing any query variables.

~~~
graphql(Schema, query, null).then(function(result) {
  console.log(result);
});
~~~

What happens after you run it?

  - There was an error saying: query variables are required.
  - There was nothing in the console.
  - **We got the result as: `received: undefined`.**
  - We got the result as: `received: null`.

*****

```
id:     answer-assigning-query-variables
type:   text
points: 5
```

Even without query variables, our query was executed and got the result as `received: undefined`. This happens because the `message` argument of the root query field is optional.

If you make it required, there will be an error.

*****

```
id:     what-next
type:   text
points: 5
```

## What's next

What we've learned is a very basic way to execute GraphQL queries. The `graphql` npm module is isomorphic. So you can use it with both the client and the server.

When you are using it with a real app, you can get some help from high-level modules that generate GraphQL schemas for you. Here are a few of them:

* [express-graphql](https://github.com/graphql/express-graphql) - This project allows you to create a GraphQL server with [express](http://expressjs.com/).
* [graffiti](https://github.com/RisingStack/graffiti) - This project allows you to use an existing mongoose schema with GraphQL.
