# graphql-endpoint
A lightweight framework for GraphQL endpoints.

**Before**:
```javascript
fetch('somewhere/graphql', {
  method: 'POST',
  headers: {
    'content-type': 'application/json',
  },
  body: JSON.stringify({ bigGraphQLQueryString, variables })
).then(response => response.json()) // receive GraphQLResult
```

**After**:
```javascripta
// put variables into http GET queris
fetch('somewhere/wrapped/endpoint?variable1=abc&variable2=def')
.then(response => response.json()) // receive GraphQLResult

// or make http POST
fetch('somehwere/wrapped/endpoint', {
  method: 'POST',
  headers: {
    'content-type': 'application/json',
  },
  body: JSON.stringify({ variables }) // no need query, it's on server side
).then(response => response.json()) // receive GraphQLResult
```

## Usage

```javascript
const { ApolloServer } = require('apollo-server-express')
const express = require('express')
const { GraphQLEndpoint } = require('graphql-endpoint')
const bodyParser = require('body-parser')

const app = express()

// graphql-endpoint requires json body to be parsed
app.use(bodyParser.json())
// Adopt graphql-endpoint
GraphQLEndpoint(app)

const server = new ApolloServer(configs)
server.applyMiddleware({
  app,
  bodyParser: false // optional disable bodyParser, we already have it
})
```

Then put your GraphQL query files into `src/endpoints/`. For example, the `src/endpoints/my_api` with content `{ example(id: 123) { title, description } }` will serve GraphQL result on `/my_api` .

The wrapped API endpoint will support two type of http requests:

* simple http GET: the http GET queries will be collected into GraphQL variables then be sent to GraphQL server with server side predefined GraphQL query.
* http POST: the http POST body be merged with server side predefined query, then be sent to GraphQL server.
