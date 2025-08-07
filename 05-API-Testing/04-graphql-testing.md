# GraphQL Testing

GraphQL is a query language and runtime for APIs that provides a more efficient, powerful, and flexible alternative to REST. Testing GraphQL APIs requires different approaches and tools compared to traditional REST API testing.

## 🎯 What is GraphQL?

GraphQL is a query language for APIs that allows clients to request exactly the data they need, nothing more and nothing less. It provides:

- **Single Endpoint:** All requests go through one endpoint (usually `/graphql`)
- **Strongly Typed Schema:** Clear contract between client and server
- **Introspection:** Self-documenting API with built-in schema exploration
- **Real-time Updates:** Support for subscriptions
- **Efficient Data Fetching:** Reduce over-fetching and under-fetching

### **Key GraphQL Concepts:**
- **Schema:** Defines the types and operations available
- **Queries:** Read operations to fetch data
- **Mutations:** Write operations to modify data
- **Subscriptions:** Real-time updates
- **Resolvers:** Functions that determine how to fetch data for each field

---

## 🔍 GraphQL vs REST

| Aspect | REST | GraphQL |
|--------|------|---------|
| **Endpoints** | Multiple endpoints | Single endpoint |
| **Data Fetching** | Fixed responses | Flexible queries |
| **Over-fetching** | Common problem | Eliminated |
| **Under-fetching** | Requires multiple requests | Single request |
| **Versioning** | URL-based versioning | Schema evolution |
| **Documentation** | External tools | Built-in introspection |
| **Caching** | HTTP-level caching | Query-level caching |

---

## 🛠️ GraphQL Testing Tools

### **1. GraphQL Playground**
- **Interactive IDE:** Visual interface for testing GraphQL queries
- **Schema Exploration:** Browse available types and operations
- **Query Validation:** Real-time syntax checking
- **Documentation:** Auto-generated documentation from schema

### **2. Insomnia**
- **GraphQL Support:** Native GraphQL client with excellent features
- **Query Builder:** Visual query builder
- **Environment Variables:** Support for dynamic values
- **Plugin System:** Extensible with plugins

### **3. Postman**
- **GraphQL Support:** Built-in GraphQL request type
- **Schema Import:** Import GraphQL schemas
- **Query Builder:** Visual query builder
- **Collection Management:** Organize GraphQL requests

### **4. Apollo Studio**
- **Schema Registry:** Centralized schema management
- **Performance Monitoring:** Track query performance
- **Error Tracking:** Monitor and debug errors
- **Team Collaboration:** Share schemas and queries

---

## 📱 Real-World Example: X (Twitter) GraphQL API

### **GraphQL Schema Example**
```graphql
type User {
  id: ID!
  username: String!
  name: String!
  description: String
  followersCount: Int!
  followingCount: Int!
  tweetsCount: Int!
  tweets: [Tweet!]!
  createdAt: DateTime!
}

type Tweet {
  id: ID!
  text: String!
  author: User!
  likesCount: Int!
  retweetsCount: Int!
  repliesCount: Int!
  createdAt: DateTime!
  isRetweet: Boolean!
  originalTweet: Tweet
}

type Query {
  user(username: String!): User
  tweet(id: ID!): Tweet
  searchTweets(query: String!, limit: Int = 10): [Tweet!]!
  trendingTopics: [String!]!
}

type Mutation {
  createTweet(text: String!): Tweet!
  deleteTweet(id: ID!): Boolean!
  likeTweet(id: ID!): Boolean!
  retweet(id: ID!): Tweet!
  followUser(userId: ID!): Boolean!
  unfollowUser(userId: ID!): Boolean!
}

type Subscription {
  newTweet: Tweet!
  tweetLiked(tweetId: ID!): Tweet!
  userFollowed(userId: ID!): User!
}

scalar DateTime
```

### **Query Examples**

#### **Get User Profile with Tweets**
```graphql
query GetUserProfile($username: String!) {
  user(username: $username) {
    id
    username
    name
    description
    followersCount
    followingCount
    tweetsCount
    tweets {
      id
      text
      likesCount
      retweetsCount
      createdAt
    }
  }
}
```

**Variables:**
```json
{
  "username": "jayzhai"
}
```

#### **Search Tweets**
```graphql
query SearchTweets($query: String!, $limit: Int) {
  searchTweets(query: $query, limit: $limit) {
    id
    text
    author {
      username
      name
    }
    likesCount
    retweetsCount
    createdAt
  }
}
```

**Variables:**
```json
{
  "query": "automation testing",
  "limit": 5
}
```

#### **Create Tweet**
```graphql
mutation CreateTweet($text: String!) {
  createTweet(text: $text) {
    id
    text
    author {
      username
      name
    }
    createdAt
  }
}
```

**Variables:**
```json
{
  "text": "Hello from GraphQL! Learning GraphQL testing. #QA #GraphQL #Testing"
}
```

---

## 🧪 GraphQL Testing Approaches

### **1. Schema Testing**

#### **Schema Validation**
- **Type Validation:** Ensure all types are properly defined
- **Field Validation:** Check that all fields have correct types
- **Directive Testing:** Validate custom directives
- **Scalar Testing:** Test custom scalar types

#### **Introspection Testing**
```graphql
query IntrospectionQuery {
  __schema {
    types {
      name
      kind
      fields {
        name
        type {
          name
          kind
        }
      }
    }
  }
}
```

### **2. Query Testing**

#### **Valid Queries**
- **Simple Queries:** Test basic field fetching
- **Nested Queries:** Test relationships between types
- **Query Arguments:** Test filtering, pagination, sorting
- **Query Variables:** Test dynamic query parameters

#### **Invalid Queries**
- **Missing Fields:** Test queries with non-existent fields
- **Invalid Arguments:** Test queries with wrong argument types
- **Malformed Queries:** Test syntax errors
- **Authorization:** Test queries without proper authentication

### **3. Mutation Testing**

#### **Valid Mutations**
- **Create Operations:** Test creating new resources
- **Update Operations:** Test modifying existing resources
- **Delete Operations:** Test removing resources
- **Complex Mutations:** Test operations with multiple steps

#### **Invalid Mutations**
- **Validation Errors:** Test with invalid input data
- **Authorization Errors:** Test without proper permissions
- **Business Logic Errors:** Test violating business rules
- **Constraint Violations:** Test unique constraints, foreign keys

### **4. Subscription Testing**

#### **Real-time Updates**
- **Connection Testing:** Test WebSocket connections
- **Event Testing:** Test subscription to specific events
- **Data Validation:** Test received data format
- **Error Handling:** Test connection failures

---

## 🔧 GraphQL Testing Tools and Frameworks

### **1. Python Testing**

#### **gql + pytest**
```python
import pytest
from gql import gql, Client
from gql.transport.requests import RequestsHTTPTransport

class GraphQLTestFramework:
    def __init__(self, endpoint: str, headers: dict = None):
        transport = RequestsHTTPTransport(url=endpoint, headers=headers)
        self.client = Client(transport=transport, fetch_schema_from_transport=True)
    
    def execute_query(self, query: str, variables: dict = None):
        """Execute a GraphQL query"""
        return self.client.execute(gql(query), variable_values=variables)
    
    def execute_mutation(self, mutation: str, variables: dict = None):
        """Execute a GraphQL mutation"""
        return self.client.execute(gql(mutation), variable_values=variables)
    
    def test_schema_introspection(self):
        """Test schema introspection"""
        query = """
        query IntrospectionQuery {
          __schema {
            types {
              name
              kind
            }
          }
        }
        """
        
        result = self.execute_query(query)
        assert "__schema" in result
        assert "types" in result["__schema"]
        print("✅ Schema introspection test passed")
    
    def test_user_query(self):
        """Test user query"""
        query = """
        query GetUser($username: String!) {
          user(username: $username) {
            id
            username
            name
            description
          }
        }
        """
        
        variables = {"username": "jayzhai"}
        result = self.execute_query(query, variables)
        
        assert "user" in result
        assert result["user"]["username"] == "jayzhai"
        assert "id" in result["user"]
        print("✅ User query test passed")
    
    def test_create_tweet_mutation(self):
        """Test create tweet mutation"""
        mutation = """
        mutation CreateTweet($text: String!) {
          createTweet(text: $text) {
            id
            text
            author {
              username
            }
          }
        }
        """
        
        variables = {"text": "Test tweet from GraphQL! #QA #Testing"}
        result = self.execute_mutation(mutation, variables)
        
        assert "createTweet" in result
        assert result["createTweet"]["text"] == variables["text"]
        assert "id" in result["createTweet"]
        print("✅ Create tweet mutation test passed")
    
    def test_invalid_query(self):
        """Test invalid query handling"""
        query = """
        query InvalidQuery {
          user(username: "jayzhai") {
            id
            nonExistentField
          }
        }
        """
        
        try:
            self.execute_query(query)
            assert False, "Should have thrown an error"
        except Exception as e:
            assert "nonExistentField" in str(e)
            print("✅ Invalid query test passed")

# Usage
if __name__ == "__main__":
    headers = {
        "Authorization": "Bearer YOUR_TOKEN"
    }
    
    framework = GraphQLTestFramework("https://api.twitter.com/graphql", headers)
    framework.test_schema_introspection()
    framework.test_user_query()
    framework.test_create_tweet_mutation()
    framework.test_invalid_query()
```

### **2. JavaScript Testing**

#### **Apollo Client + Jest**
```javascript
const { ApolloClient, InMemoryCache, gql } = require('@apollo/client');
const { createHttpLink } = require('@apollo/client/link/http');

class GraphQLTestFramework {
    constructor(endpoint, headers = {}) {
        const httpLink = createHttpLink({
            uri: endpoint,
            headers: headers
        });
        
        this.client = new ApolloClient({
            link: httpLink,
            cache: new InMemoryCache()
        });
    }
    
    async executeQuery(query, variables = {}) {
        return await this.client.query({
            query: gql`${query}`,
            variables: variables
        });
    }
    
    async executeMutation(mutation, variables = {}) {
        return await this.client.mutate({
            mutation: gql`${mutation}`,
            variables: variables
        });
    }
    
    async testSchemaIntrospection() {
        const query = `
            query IntrospectionQuery {
                __schema {
                    types {
                        name
                        kind
                    }
                }
            }
        `;
        
        const result = await this.executeQuery(query);
        expect(result.data.__schema).toBeDefined();
        expect(result.data.__schema.types).toBeDefined();
        console.log('✅ Schema introspection test passed');
    }
    
    async testUserQuery() {
        const query = `
            query GetUser($username: String!) {
                user(username: $username) {
                    id
                    username
                    name
                    description
                }
            }
        `;
        
        const variables = { username: 'jayzhai' };
        const result = await this.executeQuery(query, variables);
        
        expect(result.data.user).toBeDefined();
        expect(result.data.user.username).toBe('jayzhai');
        expect(result.data.user.id).toBeDefined();
        console.log('✅ User query test passed');
    }
    
    async testCreateTweetMutation() {
        const mutation = `
            mutation CreateTweet($text: String!) {
                createTweet(text: $text) {
                    id
                    text
                    author {
                        username
                    }
                }
            }
        `;
        
        const variables = { text: 'Test tweet from GraphQL! #QA #Testing' };
        const result = await this.executeMutation(mutation, variables);
        
        expect(result.data.createTweet).toBeDefined();
        expect(result.data.createTweet.text).toBe(variables.text);
        expect(result.data.createTweet.id).toBeDefined();
        console.log('✅ Create tweet mutation test passed');
    }
    
    async testInvalidQuery() {
        const query = `
            query InvalidQuery {
                user(username: "jayzhai") {
                    id
                    nonExistentField
                }
            }
        `;
        
        try {
            await this.executeQuery(query);
            throw new Error('Should have thrown an error');
        } catch (error) {
            expect(error.message).toContain('nonExistentField');
            console.log('✅ Invalid query test passed');
        }
    }
}

// Jest test suite
describe('GraphQL API Tests', () => {
    const framework = new GraphQLTestFramework('https://api.twitter.com/graphql', {
        'Authorization': 'Bearer YOUR_TOKEN'
    });
    
    test('should perform schema introspection', async () => {
        await framework.testSchemaIntrospection();
    });
    
    test('should query user data', async () => {
        await framework.testUserQuery();
    });
    
    test('should create tweet via mutation', async () => {
        await framework.testCreateTweetMutation();
    });
    
    test('should handle invalid queries', async () => {
        await framework.testInvalidQuery();
    });
});
```

---

## 📊 GraphQL Testing Checklist

### **Schema Testing**
- [ ] **Schema Validation:** Verify schema syntax and structure
- [ ] **Type Definitions:** Test all custom types and scalars
- [ ] **Field Validation:** Ensure all fields have correct types
- [ ] **Directive Testing:** Test custom directives
- [ ] **Introspection:** Verify introspection query works

### **Query Testing**
- [ ] **Valid Queries:** Test all query operations
- [ ] **Query Arguments:** Test filtering, pagination, sorting
- [ ] **Nested Queries:** Test relationships between types
- [ ] **Query Variables:** Test dynamic parameters
- [ ] **Error Handling:** Test invalid queries

### **Mutation Testing**
- [ ] **Create Operations:** Test creating new resources
- [ ] **Update Operations:** Test modifying existing resources
- [ ] **Delete Operations:** Test removing resources
- [ ] **Input Validation:** Test validation rules
- [ ] **Authorization:** Test permission requirements

### **Subscription Testing**
- [ ] **Connection Testing:** Test WebSocket connections
- [ ] **Event Testing:** Test subscription to events
- [ ] **Data Validation:** Test received data format
- [ ] **Error Handling:** Test connection failures
- [ ] **Reconnection:** Test automatic reconnection

### **Performance Testing**
- [ ] **Query Performance:** Test query execution time
- [ ] **N+1 Queries:** Test for query optimization issues
- [ ] **Caching:** Test query result caching
- [ ] **Load Testing:** Test under high load
- [ ] **Memory Usage:** Monitor memory consumption

---

## 💡 Best Practices

### **1. Schema-First Development**
- **Define Schema Early:** Start with schema definition
- **Use Schema Validation:** Validate schema against requirements
- **Version Control:** Track schema changes
- **Documentation:** Keep schema documentation updated

### **2. Query Optimization**
- **Avoid Over-fetching:** Request only needed fields
- **Use Fragments:** Reuse common field selections
- **Implement Pagination:** Handle large result sets
- **Monitor Query Complexity:** Track query performance

### **3. Error Handling**
- **GraphQL Errors:** Handle GraphQL-specific errors
- **Network Errors:** Handle connection issues
- **Validation Errors:** Handle input validation failures
- **Business Logic Errors:** Handle domain-specific errors

### **4. Testing Strategy**
- **Unit Tests:** Test individual resolvers
- **Integration Tests:** Test query execution
- **End-to-End Tests:** Test complete workflows
- **Performance Tests:** Test query performance

### **5. Security**
- **Authentication:** Implement proper authentication
- **Authorization:** Control access to operations
- **Input Validation:** Validate all inputs
- **Rate Limiting:** Implement query rate limiting

**Remember:** GraphQL testing requires understanding the schema, query structure, and GraphQL-specific concepts. Focus on testing the schema, queries, mutations, and subscriptions while ensuring good performance and security.

Happy GraphQL testing! 🚀
