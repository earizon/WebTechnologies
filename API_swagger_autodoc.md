[[{API_MANAGEMENT,20_QA.DOCUMENTATION,STACK.NODEJS]]
# swagger-autogen API Documentation 

## PRESETUP:

```
$ npm install --save-dev swagger-autogen
```

https://medium.com/swlh/automatic-api-documentation-in-node-js-using-swagger-dd1ab3c78284

swagger-autogen input:
- output path for .json Swagger.
- files containing the endpoints (files containing get(), post(),  functions)
- object containing documentation details: (optional)
  - version, title, description, schemes, etc.,

Example: https://github.com/davibaltar/example-swagger-autogen. 


project structure:
|-- ...
|-- swagger.js     <··· Script in charge of calling swagger-autogen
                        similar to:
       const swaggerAutogen = require('swagger-autogen')()
       const outputFile = './swagger_output.json' // <··· Generated Output (save to Git)
       const endpointsFiles = [
         './endpoints.js'
       ]          

       const doc = {
         info: {
             version: "1.0.0",
             title: "My API",
             description: "Documentation ..."
         },
         host: "api.bla.ble",
         basePath: "/",
         schemes: ['http', 'https'],
         consumes: ['application/json'],  // 
         produces: ['application/json'],
         tags: [
             { "tag_key1": "...", 
               "tag_key2": "..." }
         ],
         securityDefinitions: {
           api_key: { 
              type: "apiKey", name: "api_key", in: "header" },
           petstore_auth: {
             type: "oauth2",
             authorizationUrl: "https://petstore.swagger.io/oauth/authorize",
             flow: "implicit",
             scopes: {
               read_pets: "read your pets",
               write_pets: "modify pets in your account"
             }
           }
         },
         definitions: {  // <·· INPUT and Output data schemas
           User: {
             $id: "myUser01",    // '$' indicates attribute is mandatory
             $prop01: 10,
             $prop02: { ... },
             $prop03: [ ... ]
           },
           ...
         }
       }

    // swaggerAutogen(outputFile, endpointsFiles, doc) // alt 1. Just generate code
       swaggerAutogen(outputFile, endpointsFiles, doc) // alt 2. Auto gen code,
       .then(() => {                                        then launch server
         require('./index.js')
       })
|-- endpoints.js   <··· file containing .get()/post()/... functions.
    Something similar to:
        module.exports = function (app) {
          app.get('/users/:id', (req, res) => {
            /*
             * #swagger.tags = ['User']
             * #swagger.description = 'Endpoint para obter um usuário.'
             * ^^^^^^^^ 
             * This is the way to communicate wit swagger-autogen
             * WARN: Commment must be inside the function.
             */
            ... 
            return res.status(404).send(false)
          })
        }
        └─────────────────┬───────────────────┘
    TIP: With Express.js (or similar) swagger-autogen 
         automatically recognizes the 
         methods/path/status-of-responses and 
         content-type of Header!
|-- package.json
    {
      "main": "index.js",
      "scripts": {
        "start": "node swagger.js",          <···· Autogen doc and start server
        "swagger-autogen": "node swagger.js" <···· npm run swagger-autogen
      },                                           to generate the info
      "dependencies": {
        "express": "^4.17.1",
        "swagger-autogen": "^2.23.1",
        "swagger-ui-express": "^4.1.4"
      }
    }

TIP: every time the project is started, new documentation is generated.
     making it always updated.

TIPS:  #swagger.ignore = true       <··· Ignore this endpoint 

If the framework is NOT automatically recognised by swagger, do it manually like:
 function myFunction(params) {
  // #swagger.path = '/forcedEndpoint/{id}'
  // #swagger.method = 'put'
  // #swagger.description = 'Endpoint forçado'
  // #swagger.produces = ["application/json"]
  // #swagger.tags = ['User']
  }

https://github.com/davibaltar/example-swagger-autogen-with-router
https://github.com/davibaltar/example-swagger-autogen.

Note: We can also expose the API directly to our micro using swagger-ui-express

[[}]]
