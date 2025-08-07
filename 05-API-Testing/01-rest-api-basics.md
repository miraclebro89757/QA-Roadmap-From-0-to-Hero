# REST API Basics

REST (Representational State Transfer) APIs are the foundation of modern web applications. Understanding REST API fundamentals is essential for effective API testing and automation.

## 🎯 What is REST API?

REST is an architectural style for designing networked applications. REST APIs use HTTP methods to perform operations on resources, making them stateless, cacheable, and scalable.

### **Key REST Principles:**
- **Stateless:** Each request contains all information needed
- **Client-Server:** Separation of concerns between client and server
- **Cacheable:** Responses can be cached for better performance
- **Uniform Interface:** Consistent way to interact with resources
- **Layered System:** Components can't see beyond immediate layer

---

## 🌐 HTTP Fundamentals

### **HTTP Methods**
```http
GET     - Retrieve data (safe, idempotent)
POST    - Create new data
PUT     - Update entire resource
PATCH   - Update partial resource
DELETE  - Remove data
HEAD    - Get headers only
OPTIONS - Get allowed methods
```

### **HTTP Status Codes**
```http
2xx - Success
  200 OK - Request successful
  201 Created - Resource created
  204 No Content - Success but no content

4xx - Client Error
  400 Bad Request - Invalid request
  401 Unauthorized - Authentication required
  403 Forbidden - Access denied
  404 Not Found - Resource not found
  409 Conflict - Resource conflict
  422 Unprocessable Entity - Validation error

5xx - Server Error
  500 Internal Server Error
  502 Bad Gateway
  503 Service Unavailable
  504 Gateway Timeout
```

### **HTTP Headers**
```http
Content-Type: application/json
Authorization: Bearer <token>
Accept: application/json
User-Agent: MyApp/1.0
Cache-Control: no-cache
```

---

## 📱 Real-World Example: X (Twitter) API

### **Base URL Structure**
```
https://api.twitter.com/v2/
```

### **Authentication**
```http
Authorization: Bearer <your-bearer-token>
```

### **1. User Management Endpoints**

#### **Get User Profile**
```http
GET /users/by/username/{username}
```

**Request:**
```bash
curl -X GET "https://api.twitter.com/v2/users/by/username/jayzhai" \
  -H "Authorization: Bearer YOUR_BEARER_TOKEN"
```

**Response:**
```json
{
  "data": {
    "id": "123456789",
    "username": "jayzhai",
    "name": "Jay Zhai",
    "description": "QA Engineer and Automation Expert",
    "created_at": "2020-01-01T00:00:00.000Z",
    "public_metrics": {
      "followers_count": 1000,
      "following_count": 500,
      "tweet_count": 2500
    }
  }
}
```

#### **Create User Account**
```http
POST /users
```

**Request:**
```json
{
  "username": "newuser",
  "email": "newuser@example.com",
  "password": "SecurePass123!",
  "name": "New User",
  "description": "New user account"
}
```

### **2. Tweet Management Endpoints**

#### **Get User Tweets**
```http
GET /users/{id}/tweets
```

**Response:**
```json
{
  "data": [
    {
      "id": "1234567890123456789",
      "text": "Learning API testing with REST! #QA #Testing",
      "created_at": "2024-01-15T09:00:00.000Z",
      "public_metrics": {
        "retweet_count": 5,
        "like_count": 25,
        "reply_count": 3
      }
    }
  ],
  "meta": {
    "result_count": 1,
    "next_token": "NEXT_TOKEN_HERE"
  }
}
```

#### **Create Tweet**
```http
POST /tweets
```

**Request:**
```json
{
  "text": "Hello from API testing! Learning REST APIs with X (Twitter) examples. #QA #Automation #Testing"
}
```

#### **Delete Tweet**
```http
DELETE /tweets/{id}
```

**Response:**
```json
{
  "data": {
    "deleted": true
  }
}
```

---

## 🔧 API Testing Examples

### **1. Manual Testing with cURL**

#### **Test User Authentication**
```bash
# Test valid credentials
curl -X POST "https://api.twitter.com/v2/oauth2/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=password&username=testuser&password=TestPass123!"

# Expected: 200 OK with access token
```

#### **Test Invalid Authentication**
```bash
# Test invalid credentials
curl -X POST "https://api.twitter.com/v2/oauth2/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=password&username=testuser&password=WrongPass"

# Expected: 401 Unauthorized
```

### **2. Python Testing with requests**

```python
import requests
import pytest

class TwitterAPITest:
    def __init__(self):
        self.base_url = "https://api.twitter.com/v2"
        self.headers = {
            "Authorization": "Bearer YOUR_BEARER_TOKEN",
            "Content-Type": "application/json"
        }
    
    def test_get_user_profile(self):
        """Test getting user profile"""
        username = "jayzhai"
        response = requests.get(
            f"{self.base_url}/users/by/username/{username}",
            headers=self.headers
        )
        
        assert response.status_code == 200
        data = response.json()
        assert data["data"]["username"] == username
        assert "id" in data["data"]
        print("✅ Get user profile test passed")
    
    def test_create_tweet(self):
        """Test creating a new tweet"""
        tweet_text = "Test tweet from API automation! #QA #Testing"
        payload = {"text": tweet_text}
        
        response = requests.post(
            f"{self.base_url}/tweets",
            headers=self.headers,
            json=payload
        )
        
        assert response.status_code == 201
        data = response.json()
        assert data["data"]["text"] == tweet_text
        assert "id" in data["data"]
        print("✅ Create tweet test passed")
        
        return data["data"]["id"]
    
    def test_invalid_tweet_length(self):
        """Test tweet with invalid length"""
        long_tweet = "A" * 281
        payload = {"text": long_tweet}
        
        response = requests.post(
            f"{self.base_url}/tweets",
            headers=self.headers,
            json=payload
        )
        
        assert response.status_code == 422
        print("✅ Invalid tweet length test passed")
    
    def test_unauthorized_access(self):
        """Test accessing API without authentication"""
        response = requests.get(
            f"{self.base_url}/users/by/username/jayzhai"
        )
        
        assert response.status_code == 401
        print("✅ Unauthorized access test passed")

# Run tests
if __name__ == "__main__":
    api_test = TwitterAPITest()
    api_test.test_get_user_profile()
    api_test.test_create_tweet()
    api_test.test_invalid_tweet_length()
    api_test.test_unauthorized_access()
    print("🎉 All API tests completed!")
```

### **3. JavaScript Testing with axios**

```javascript
const axios = require('axios');

class TwitterAPITest {
    constructor() {
        this.baseURL = 'https://api.twitter.com/v2';
        this.headers = {
            'Authorization': 'Bearer YOUR_BEARER_TOKEN',
            'Content-Type': 'application/json'
        };
    }
    
    async testGetUserProfile() {
        try {
            const username = 'jayzhai';
            const response = await axios.get(
                `${this.baseURL}/users/by/username/${username}`,
                { headers: this.headers }
            );
            
            console.assert(response.status === 200, 'Status should be 200');
            console.assert(response.data.data.username === username, 'Username should match');
            console.log('✅ Get user profile test passed');
        } catch (error) {
            console.error('❌ Get user profile test failed:', error.message);
        }
    }
    
    async testCreateTweet() {
        try {
            const tweetText = 'Test tweet from JavaScript API automation! #QA #Testing';
            const payload = { text: tweetText };
            
            const response = await axios.post(
                `${this.baseURL}/tweets`,
                payload,
                { headers: this.headers }
            );
            
            console.assert(response.status === 201, 'Status should be 201');
            console.assert(response.data.data.text === tweetText, 'Tweet text should match');
            console.log('✅ Create tweet test passed');
        } catch (error) {
            console.error('❌ Create tweet test failed:', error.message);
        }
    }
    
    async testUnauthorizedAccess() {
        try {
            const response = await axios.get(
                `${this.baseURL}/users/by/username/jayzhai`
            );
            
            console.assert(false, 'Should have thrown an error');
        } catch (error) {
            console.assert(error.response.status === 401, 'Status should be 401');
            console.log('✅ Unauthorized access test passed');
        }
    }
    
    async runAllTests() {
        console.log('🚀 Starting API tests...');
        await this.testGetUserProfile();
        await this.testCreateTweet();
        await this.testUnauthorizedAccess();
        console.log('🎉 All API tests completed!');
    }
}

// Run tests
const apiTest = new TwitterAPITest();
apiTest.runAllTests();
```

---

## 📋 API Testing Checklist

### **Functional Testing**
- [ ] **Happy Path Testing:** Test successful operations
- [ ] **Error Handling:** Test various error scenarios
- [ ] **Input Validation:** Test invalid inputs and edge cases
- [ ] **Business Logic:** Verify business rules and constraints
- [ ] **Data Integrity:** Ensure data consistency

### **Non-Functional Testing**
- [ ] **Performance:** Response time and throughput
- [ ] **Security:** Authentication and authorization
- [ ] **Reliability:** Error rates and availability
- [ ] **Scalability:** Handle load and stress
- [ ] **Compatibility:** Different clients and versions

### **API-Specific Testing**
- [ ] **HTTP Methods:** Test all supported methods
- [ ] **Status Codes:** Verify correct status codes
- [ ] **Headers:** Test required and optional headers
- [ ] **Response Format:** Validate JSON/XML structure
- [ ] **Rate Limiting:** Test API limits and throttling

---

## 💡 Best Practices

### **1. Test Design**
- **Start with Manual Testing:** Understand API behavior first
- **Use Real Data:** Test with realistic data scenarios
- **Test Edge Cases:** Boundary values and error conditions
- **Document Test Cases:** Clear test documentation

### **2. Automation**
- **Reusable Components:** Create reusable test functions
- **Environment Management:** Use different environments
- **Data Management:** Clean up test data after tests
- **CI/CD Integration:** Run tests in pipelines

### **3. Validation**
- **Response Status:** Verify HTTP status codes
- **Response Body:** Validate JSON structure and content
- **Response Headers:** Check important headers
- **Business Logic:** Verify business rules

### **4. Error Handling**
- **Expected Errors:** Test known error scenarios
- **Unexpected Errors:** Handle unexpected failures
- **Error Messages:** Validate error message content
- **Error Recovery:** Test error recovery mechanisms

**Remember:** REST API testing is about understanding how systems communicate and ensuring they work correctly together. Focus on both the technical aspects (HTTP, JSON, status codes) and the business logic (what the API should do). Practice with real APIs and build a comprehensive testing strategy.

Happy API testing! 🚀
