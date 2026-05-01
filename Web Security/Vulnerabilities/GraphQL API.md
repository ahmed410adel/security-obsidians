GraphQL is an API query language that is designed to facilitate efficient communication between clients and servers. It enables the user to specify exactly what data they want in the response, helping to avoid the large response objects and multiple calls that can sometimes be seen with REST APIs.

![[Pasted image 20260131202225.png]]

The data described by a GraphQL schema can be manipulated using three types of operation:
- Queries fetch data.
- Mutations add, change, or remove data.
- Subscriptions are similar to queries, but set up a permanent connection by which a server can proactively push data to a client in the specified format.
## What is a GraphQL schema?
The example below shows a simple schema definition for a Product type. The `!` operator indicates that the field is non-nullable when called (that is, mandatory).

    type Product {
        id: ID!
        name: String!
        description: String!
        price: Int
    }
Schemas must also include at least one available query. Usually, they also contain details of available mutations.

## What are GraphQL queries?
GraphQL queries retrieve data from the data store. They are roughly equivalent to GET requests in a REST API.
- A `query` operation type. This is technically optional but encouraged, as it explicitly tells the server that the incoming request is a query.
- A query name. This can be anything you want. The query name is optional, but encouraged as it can help with debugging.
- A data structure. This is the data that the query should return.
- Optionally, one or more arguments. These are used to create queries that return details of a specific
For example:

	query myGetProductQuery {
		getProduct(id: 123) {
			name
			description
		}
	}

## What are GraphQL mutations?
Mutations change data in some way, either adding, deleting, or editing it. They are roughly equivalent to a REST API's POST, PUT, and DELETE methods.

  Example mutation request

    mutation {
        createProduct(name: "Flamin' Cocktail Glasses", listed: "yes") {
            id
            name
            listed
        }
    }

Example mutation response

    {
        "data": {
            "createProduct": {
                "id": 123,
                "name": "Flamin' Cocktail Glasses",
                "listed": "yes"
            }
        }
    }
