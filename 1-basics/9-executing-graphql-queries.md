```
name:  Executing GraphQL Queries
bulletPackage: free
```

We've learned about GraphQL query language and how to define schemas. But, we didn't try to execute queries against a schema ourselves. We simply use our GraphQL Sandbox which is build on top of Facebook's GraphiQL project.

Now it's time to learn about how to run GraphQL schemas on our own. For this lesson, we'll discuss how to do it with NodeJS (and JavaScript), but later you'll learn how to use other languages as well.

### Using "graphql" Npm Module

`graphql` npm module is the only tool(or library) you want to define a basic schema and execute a query against it.

Let's get started!

*****

```
id:     setting-up
type:   text
points: 5
```

## Setting Up

First of all we need to create a directory to put our JavaScript file and npm modules.

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

Then, you can execute it with: `babel-node index.js`

> Here we use `babel-node` because, it transpile ES2015 into ES5 and execute with the any nodejs version installed on your app.

Finally, install `graphql` npm module into this directory.

~~~
npm install graphql
~~~

*****

```
id:     defining-the-schema
type:   text
points: 5
```

### Defining The Schema

Now we need to define a schema. In this schema, we've a single root query field called `echo`, which echo what's it receives.

### Import GraphQL Classes

First we need to import all basic GraphQL classes where we use to build the schema. Add following content on top of our file `index.js`.

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

## Executing a Query

Now it's time to execute a query. So, let's assign our query into a variable.

> Add these content to the end of the `index.js.`

~~~
let query = `
  {
    receivedMessage: echo(message: "Hello")
  }
`;
~~~

Then, this is how to execute this query.

~~~
graphql(Schema, query).then(function(result) {
  console.log(result);
});
~~~

Here, we use `graphql` function from the `graphql` npm module to execute a query. It returns a promise and that's why we use `.then` to get the result.

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

## Assigning Query Variables

Now we need to use query variables with our query. So, our query will be looks like this:

~~~
let query = `
  query getEcho($inputMessage: String) {
    receivedMessage: echo(message: $inputMessage)
  }
`;
~~~

Then, we need to provide query variables to the `graphql` function. So, here's how to do it.

~~~
graphql(Schema, query, null, {inputMessage: "Hello"}).then(function(result) {
  console.log(result);
});
~~~

Try to run our script with: `babel-node index.js`

You'll get the expected result.

---

Now you've a task.

Run our query, without providing query variables.

~~~
graphql(Schema, query, null).then(function(result) {
  console.log(result);
});
~~~

Then what happens after you run it?

  - There was an error saying: query variables are required
  - There was nothing in the console
  - **We got the result as: `received: undefined`**
  - We got the result as: `received: null`

*****

```
id:     answer-assigning-query-variables
type:   text
points: 5
```

Even without query variables, our query got executed and got the result as `received: undefined`. This happens because, `message` argument of the root query field is optional.

If you make it required, there will be an error.

*****

```
id:     what-next
type:   text
points: 5
```

## What Next

What we've learned is the very basic way to execute GraphQL queries. `graphql` npm module is isomorphic. So, you can use it in both client and the server.

When you are using it with a real app, you can get some help from high level modules which generates GraphQL schemas for you. Here are few of them:

* [express-graphql](https://github.com/graphql/express-graphql) - This project allows to create a graphql server with [express](http://expressjs.com/).
* [graffiti](https://github.com/RisingStack/graffiti) - This project allows you to use existing mongoose schema with GraphQL
