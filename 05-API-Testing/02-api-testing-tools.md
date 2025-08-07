# API Testing Tools

API testing tools help you efficiently test, validate, and automate API interactions. This section covers the most popular and powerful tools used by QA professionals.

## 🎯 Popular API Testing Tools

### **1. Postman**
Postman is the most popular API testing tool, offering a comprehensive platform for API development and testing.

#### **Key Features:**
- **Collection Management:** Organize requests into collections
- **Environment Variables:** Manage different environments (dev, staging, prod)
- **Automated Testing:** Write test scripts in JavaScript
- **Team Collaboration:** Share collections and environments
- **API Documentation:** Generate and publish API docs
- **Monitoring:** Schedule and monitor API performance

#### **Postman Collection Example:**
```json
{
  "info": {
    "name": "Twitter API Tests",
    "description": "Comprehensive test collection for Twitter API"
  },
  "variable": [
    {
      "key": "base_url",
      "value": "https://api.twitter.com/v2"
    },
    {
      "key": "bearer_token",
      "value": "{{TWITTER_BEARER_TOKEN}}"
    }
  ],
  "item": [
    {
      "name": "Get User Profile",
      "request": {
        "method": "GET",
        "header": [
          {
            "key": "Authorization",
            "value": "Bearer {{bearer_token}}"
          }
        ],
        "url": "{{base_url}}/users/by/username/jayzhai"
      },
      "event": [
        {
          "listen": "test",
          "script": {
            "exec": [
              "pm.test(\"Status code is 200\", function () {",
              "    pm.response.to.have.status(200);",
              "});",
              "",
              "pm.test(\"Response has user data\", function () {",
              "    var jsonData = pm.response.json();",
              "    pm.expect(jsonData.data).to.have.property('id');",
              "    pm.expect(jsonData.data.username).to.eql('jayzhai');",
              "});"
            ]
          }
        }
      ]
    }
  ]
}
```

#### **Postman Test Scripts:**
```javascript
// Comprehensive test script
pm.test("Response time is acceptable", function () {
    pm.expect(pm.response.responseTime).to.be.below(3000);
});

pm.test("Response has correct content type", function () {
    pm.expect(pm.response.headers.get("Content-Type")).to.include("application/json");
});

pm.test("Response structure is valid", function () {
    const jsonData = pm.response.json();
    pm.expect(jsonData).to.have.property('data');
});

// Data validation
pm.test("User data validation", function () {
    const jsonData = pm.response.json();
    if (jsonData.data) {
        pm.expect(jsonData.data).to.have.property('id');
        pm.expect(jsonData.data).to.have.property('username');
    }
});
```

### **2. Insomnia**
Insomnia is a modern, open-source API client with excellent GraphQL support.

#### **Key Features:**
- **GraphQL Support:** Built-in GraphQL playground
- **Plugin System:** Extensible with plugins
- **Design Mode:** Visual API design
- **Dark Theme:** Modern UI with dark mode
- **Team Sync:** Real-time collaboration

### **3. REST Assured (Java)**
REST Assured is a Java library for testing REST APIs with a fluent interface.

#### **REST Assured Test Example:**
```java
import io.restassured.RestAssured;
import io.restassured.http.ContentType;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class TwitterAPITest {
    
    @BeforeClass
    public void setup() {
        RestAssured.baseURI = "https://api.twitter.com/v2";
    }
    
    @Test
    public void testGetUserProfile() {
        given()
            .header("Authorization", "Bearer " + System.getenv("TWITTER_BEARER_TOKEN"))
            .contentType(ContentType.JSON)
        .when()
            .get("/users/by/username/jayzhai")
        .then()
            .statusCode(200)
            .body("data.username", equalTo("jayzhai"))
            .body("data.id", notNullValue())
            .time(lessThan(3000L));
    }
    
    @Test
    public void testCreateTweet() {
        String tweetText = "Hello from REST Assured! #QA #Testing #Java";
        
        given()
            .header("Authorization", "Bearer " + System.getenv("TWITTER_BEARER_TOKEN"))
            .contentType(ContentType.JSON)
            .body("{\"text\": \"" + tweetText + "\"}")
        .when()
            .post("/tweets")
        .then()
            .statusCode(201)
            .body("data.text", equalTo(tweetText))
            .body("data.id", notNullValue());
    }
}
```

### **4. Newman (Command Line)**
Newman is a command-line tool for running Postman collections.

#### **Newman Installation:**
```bash
npm install -g newman
```

#### **Running Collections:**
```bash
# Basic collection run
newman run "Twitter API Tests.postman_collection.json"

# With environment
newman run "Twitter API Tests.postman_collection.json" -e "Twitter API Environment.postman_environment.json"

# Generate reports
newman run "Twitter API Tests.postman_collection.json" -r cli,json,html

# CI/CD integration
newman run "Twitter API Tests.postman_collection.json" \
  -e "Twitter API Environment.postman_environment.json" \
  --reporters cli,json \
  --reporter-json-export results.json
```

### **5. Python Libraries**

#### **requests + pytest**
```python
import requests
import pytest

class TwitterAPITest:
    def __init__(self):
        self.base_url = "https://api.twitter.com/v2"
        self.headers = {
            "Authorization": f"Bearer {self.get_bearer_token()}",
            "Content-Type": "application/json"
        }
    
    def get_bearer_token(self):
        import os
        return os.getenv("TWITTER_BEARER_TOKEN")
    
    def test_get_user_profile(self):
        """Test getting user profile"""
        response = requests.get(
            f"{self.base_url}/users/by/username/jayzhai",
            headers=self.headers
        )
        
        assert response.status_code == 200
        data = response.json()
        assert data["data"]["username"] == "jayzhai"
        assert "id" in data["data"]
        assert response.elapsed.total_seconds() < 3
    
    def test_create_tweet(self):
        """Test creating a new tweet"""
        tweet_text = "Test tweet from Python automation! #QA #Testing"
        response = requests.post(
            f"{self.base_url}/tweets",
            headers=self.headers,
            json={"text": tweet_text}
        )
        
        assert response.status_code == 201
        data = response.json()
        assert data["data"]["text"] == tweet_text
        assert "id" in data["data"]

# Run with pytest
if __name__ == "__main__":
    pytest.main([__file__])
```

### **6. JavaScript Libraries**

#### **axios + Jest**
```javascript
const axios = require('axios');

class TwitterAPITest {
    constructor() {
        this.baseURL = 'https://api.twitter.com/v2';
        this.headers = {
            'Authorization': `Bearer ${process.env.TWITTER_BEARER_TOKEN}`,
            'Content-Type': 'application/json'
        };
    }
    
    async testGetUserProfile() {
        const response = await axios.get(
            `${this.baseURL}/users/by/username/jayzhai`,
            { headers: this.headers }
        );
        
        expect(response.status).toBe(200);
        expect(response.data.data.username).toBe('jayzhai');
        expect(response.data.data.id).toBeDefined();
    }
    
    async testCreateTweet() {
        const tweetText = 'Test tweet from Jest! #QA #Testing';
        const response = await axios.post(
            `${this.baseURL}/tweets`,
            { text: tweetText },
            { headers: this.headers }
        );
        
        expect(response.status).toBe(201);
        expect(response.data.data.text).toBe(tweetText);
        expect(response.data.data.id).toBeDefined();
    }
}

// Jest test suite
describe('Twitter API Tests', () => {
    const apiTest = new TwitterAPITest();
    
    test('should get user profile', async () => {
        await apiTest.testGetUserProfile();
    });
    
    test('should create tweet', async () => {
        await apiTest.testCreateTweet();
    });
});
```

---

## 📊 Tool Comparison

| Feature | Postman | Insomnia | REST Assured | Newman |
|---------|---------|----------|--------------|--------|
| **Ease of Use** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ |
| **GraphQL Support** | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐ |
| **Automation** | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Team Collaboration** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ |
| **CI/CD Integration** | ⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Cost** | Free/Paid | Free | Free | Free |

---

## 💡 Best Practices

### **1. Tool Selection**
- **Manual Testing:** Postman or Insomnia
- **Automation:** REST Assured (Java) or Python/JavaScript libraries
- **CI/CD:** Newman or custom scripts
- **GraphQL:** Insomnia or GraphQL Playground

### **2. Environment Management**
- Use environment variables for sensitive data
- Create separate environments for dev, staging, prod
- Never commit tokens to version control
- Use secure token management

### **3. Test Organization**
- Group related requests into collections
- Use descriptive names for requests and tests
- Add comprehensive test scripts
- Document test scenarios

### **4. Automation Strategy**
- Start with manual testing
- Automate repetitive tests
- Integrate with CI/CD pipelines
- Generate comprehensive reports

**Remember:** Choose the right tool for your specific needs. Postman is excellent for manual testing and learning, while REST Assured and custom scripts are better for automation. Focus on creating maintainable and reliable API tests that provide real value to your testing process.

Happy tooling! 🛠️
