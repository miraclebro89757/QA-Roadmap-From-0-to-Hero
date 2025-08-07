# Automated API Testing

Automated API testing involves creating test scripts that can run automatically to validate API functionality, performance, and reliability.

## 🎯 Why Automate API Testing?

### **Benefits:**
- **Faster Execution:** Run hundreds of tests in minutes
- **Consistent Results:** Eliminate human errors and variations
- **Early Detection:** Catch issues before they reach production
- **Regression Testing:** Ensure new changes don't break existing functionality
- **CI/CD Integration:** Automate testing in deployment pipelines
- **Scalability:** Test multiple environments and data sets

---

## 🐍 Python API Testing Frameworks

### **1. requests + pytest**

#### **Basic API Test Framework**
```python
import requests
import pytest
import os
from typing import Dict, Any

class APITestFramework:
    def __init__(self, base_url: str, headers: Dict[str, str] = None):
        self.base_url = base_url
        self.headers = headers or {}
        self.session = requests.Session()
        self.session.headers.update(self.headers)
    
    def get(self, endpoint: str, params: Dict[str, Any] = None) -> requests.Response:
        url = f"{self.base_url}{endpoint}"
        return self.session.get(url, params=params)
    
    def post(self, endpoint: str, json_data: Dict[str, Any] = None) -> requests.Response:
        url = f"{self.base_url}{endpoint}"
        return self.session.post(url, json=json_data)
    
    def assert_status_code(self, response: requests.Response, expected_code: int):
        assert response.status_code == expected_code, f"Expected {expected_code}, got {response.status_code}"
    
    def assert_response_time(self, response: requests.Response, max_time: float = 3.0):
        assert response.elapsed.total_seconds() < max_time, f"Response time {response.elapsed.total_seconds()}s exceeds {max_time}s"

class TwitterAPITest(APITestFramework):
    def __init__(self):
        headers = {
            "Authorization": f"Bearer {os.getenv('TWITTER_BEARER_TOKEN')}",
            "Content-Type": "application/json"
        }
        super().__init__("https://api.twitter.com/v2", headers)
    
    def test_get_user_profile(self):
        """Test getting user profile"""
        response = self.get("/users/by/username/jayzhai")
        
        self.assert_status_code(response, 200)
        self.assert_response_time(response, 3.0)
        
        data = response.json()
        assert data["data"]["username"] == "jayzhai"
        assert "id" in data["data"]
        print("✅ Get user profile test passed")
    
    def test_create_tweet(self):
        """Test creating a new tweet"""
        tweet_text = "Test tweet from Python automation! #QA #Testing"
        payload = {"text": tweet_text}
        
        response = self.post("/tweets", json_data=payload)
        
        self.assert_status_code(response, 201)
        data = response.json()
        assert data["data"]["text"] == tweet_text
        assert "id" in data["data"]
        print("✅ Create tweet test passed")
        
        return data["data"]["id"]
    
    def test_invalid_tweet_length(self):
        """Test tweet with invalid length"""
        long_tweet = "A" * 281
        payload = {"text": long_tweet}
        
        response = self.post("/tweets", json_data=payload)
        self.assert_status_code(response, 422)
        print("✅ Invalid tweet length test passed")
    
    def test_unauthorized_access(self):
        """Test accessing API without authentication"""
        temp_session = requests.Session()
        response = temp_session.get(f"{self.base_url}/users/by/username/jayzhai")
        
        self.assert_status_code(response, 401)
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

#### **Data-Driven Testing**
```python
import pytest
import json

class DataDrivenAPITest(APITestFramework):
    def __init__(self):
        headers = {
            "Authorization": f"Bearer {os.getenv('TWITTER_BEARER_TOKEN')}",
            "Content-Type": "application/json"
        }
        super().__init__("https://api.twitter.com/v2", headers)
    
    @pytest.mark.parametrize("user_data", [
        {"username": "testuser1", "expected_status": 200},
        {"username": "nonexistentuser", "expected_status": 404},
        {"username": "", "expected_status": 400}
    ])
    def test_get_user_with_different_usernames(self, user_data):
        """Test getting user with different username scenarios"""
        username = user_data["username"]
        expected_status = user_data["expected_status"]
        
        response = self.get(f"/users/by/username/{username}")
        self.assert_status_code(response, expected_status)
        
        if expected_status == 200:
            data = response.json()
            assert data["data"]["username"] == username
            print(f"✅ User profile test passed for {username}")
        else:
            print(f"✅ Error handling test passed for {username}")
    
    @pytest.mark.parametrize("tweet_data", [
        {"text": "Normal tweet", "expected_status": 201},
        {"text": "A" * 280, "expected_status": 201},  # Max length
        {"text": "A" * 281, "expected_status": 422},  # Too long
        {"text": "", "expected_status": 422},         # Empty
    ])
    def test_create_tweet_with_different_texts(self, tweet_data):
        """Test creating tweets with different text scenarios"""
        text = tweet_data["text"]
        expected_status = tweet_data["expected_status"]
        
        payload = {"text": text}
        response = self.post("/tweets", json_data=payload)
        
        self.assert_status_code(response, expected_status)
        
        if expected_status == 201:
            data = response.json()
            assert data["data"]["text"] == text
            print(f"✅ Tweet creation test passed for text: {text[:50]}...")
        else:
            print(f"✅ Validation test passed for text: {text[:50]}...")

# Run with pytest
if __name__ == "__main__":
    pytest.main([__file__, "-v"])
```

### **2. pytest with Fixtures**

```python
import pytest
import requests

@pytest.fixture(scope="session")
def api_client():
    """Create API client fixture"""
    headers = {
        "Authorization": f"Bearer {os.getenv('TWITTER_BEARER_TOKEN')}",
        "Content-Type": "application/json"
    }
    return APITestFramework("https://api.twitter.com/v2", headers)

@pytest.fixture(scope="function")
def test_tweet_id(api_client):
    """Create a test tweet and return its ID for cleanup"""
    tweet_text = "Test tweet for cleanup! #QA #Testing"
    payload = {"text": tweet_text}
    
    response = api_client.post("/tweets", json_data=payload)
    assert response.status_code == 201
    
    tweet_id = response.json()["data"]["id"]
    yield tweet_id
    
    # Cleanup: delete the tweet
    cleanup_response = api_client.delete(f"/tweets/{tweet_id}")
    assert cleanup_response.status_code == 200

class TestTwitterAPI:
    def test_get_user_profile(self, api_client):
        """Test getting user profile"""
        response = api_client.get("/users/by/username/jayzhai")
        
        api_client.assert_status_code(response, 200)
        api_client.assert_response_time(response, 3.0)
        
        data = response.json()
        assert data["data"]["username"] == "jayzhai"
        assert "id" in data["data"]
    
    def test_create_and_verify_tweet(self, api_client, test_tweet_id):
        """Test creating and verifying a tweet"""
        # Tweet was created in fixture, now verify it exists
        response = api_client.get(f"/tweets/{test_tweet_id}")
        
        api_client.assert_status_code(response, 200)
        data = response.json()
        assert data["data"]["id"] == test_tweet_id
        assert "text" in data["data"]
```

---

## 💻 JavaScript API Testing Frameworks

### **1. axios + Jest**

```javascript
const axios = require('axios');

class APITestFramework {
    constructor(baseURL, headers = {}) {
        this.baseURL = baseURL;
        this.headers = headers;
        this.client = axios.create({
            baseURL,
            headers,
            timeout: 10000
        });
    }
    
    async get(endpoint, params = {}) {
        return await this.client.get(endpoint, { params });
    }
    
    async post(endpoint, data = {}) {
        return await this.client.post(endpoint, data);
    }
    
    assertStatus(response, expectedStatus) {
        expect(response.status).toBe(expectedStatus);
    }
    
    assertResponseTime(response, maxTime = 3000) {
        expect(response.headers['x-response-time'] || 0).toBeLessThan(maxTime);
    }
}

class TwitterAPITest extends APITestFramework {
    constructor() {
        const headers = {
            'Authorization': `Bearer ${process.env.TWITTER_BEARER_TOKEN}`,
            'Content-Type': 'application/json'
        };
        super('https://api.twitter.com/v2', headers);
    }
    
    async testGetUserProfile() {
        const response = await this.get('/users/by/username/jayzhai');
        
        this.assertStatus(response, 200);
        expect(response.data.data.username).toBe('jayzhai');
        expect(response.data.data.id).toBeDefined();
        console.log('✅ Get user profile test passed');
    }
    
    async testCreateTweet() {
        const tweetText = 'Test tweet from JavaScript automation! #QA #Testing';
        const payload = { text: tweetText };
        
        const response = await this.post('/tweets', payload);
        
        this.assertStatus(response, 201);
        expect(response.data.data.text).toBe(tweetText);
        expect(response.data.data.id).toBeDefined();
        console.log('✅ Create tweet test passed');
        
        return response.data.data.id;
    }
    
    async testInvalidTweetLength() {
        const longTweet = 'A'.repeat(281);
        const payload = { text: longTweet };
        
        try {
            await this.post('/tweets', payload);
            throw new Error('Should have thrown an error');
        } catch (error) {
            expect(error.response.status).toBe(422);
            console.log('✅ Invalid tweet length test passed');
        }
    }
    
    async testUnauthorizedAccess() {
        try {
            await this.get('/users/by/username/jayzhai');
            throw new Error('Should have thrown an error');
        } catch (error) {
            expect(error.response.status).toBe(401);
            console.log('✅ Unauthorized access test passed');
        }
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
    
    test('should handle invalid tweet length', async () => {
        await apiTest.testInvalidTweetLength();
    });
    
    test('should handle unauthorized access', async () => {
        await apiTest.testUnauthorizedAccess();
    });
});
```

### **2. Supertest (Express.js Testing)**

```javascript
const request = require('supertest');
const express = require('express');

// Mock Express app for testing
const app = express();
app.use(express.json());

// Mock Twitter API endpoints
app.get('/v2/users/by/username/:username', (req, res) => {
    const { username } = req.params;
    
    if (!req.headers.authorization) {
        return res.status(401).json({ error: 'Unauthorized' });
    }
    
    if (username === 'jayzhai') {
        res.json({
            data: {
                id: '123456789',
                username: 'jayzhai',
                name: 'Jay Zhai',
                description: 'QA Engineer'
            }
        });
    } else {
        res.status(404).json({ error: 'User not found' });
    }
});

app.post('/v2/tweets', (req, res) => {
    const { text } = req.body;
    
    if (!req.headers.authorization) {
        return res.status(401).json({ error: 'Unauthorized' });
    }
    
    if (!text || text.length === 0) {
        return res.status(422).json({ error: 'Tweet text is required' });
    }
    
    if (text.length > 280) {
        return res.status(422).json({ error: 'Tweet too long' });
    }
    
    res.status(201).json({
        data: {
            id: '987654321',
            text: text,
            created_at: new Date().toISOString()
        }
    });
});

// Test suite
describe('Twitter API Mock Tests', () => {
    test('should get user profile', async () => {
        const response = await request(app)
            .get('/v2/users/by/username/jayzhai')
            .set('Authorization', 'Bearer test-token')
            .expect(200);
        
        expect(response.body.data.username).toBe('jayzhai');
        expect(response.body.data.id).toBeDefined();
    });
    
    test('should create tweet', async () => {
        const tweetText = 'Test tweet from Supertest! #QA #Testing';
        
        const response = await request(app)
            .post('/v2/tweets')
            .set('Authorization', 'Bearer test-token')
            .send({ text: tweetText })
            .expect(201);
        
        expect(response.body.data.text).toBe(tweetText);
        expect(response.body.data.id).toBeDefined();
    });
    
    test('should reject unauthorized requests', async () => {
        await request(app)
            .get('/v2/users/by/username/jayzhai')
            .expect(401);
    });
    
    test('should reject empty tweets', async () => {
        await request(app)
            .post('/v2/tweets')
            .set('Authorization', 'Bearer test-token')
            .send({ text: '' })
            .expect(422);
    });
    
    test('should reject long tweets', async () => {
        const longTweet = 'A'.repeat(281);
        
        await request(app)
            .post('/v2/tweets')
            .set('Authorization', 'Bearer test-token')
            .send({ text: longTweet })
            .expect(422);
    });
});
```

---

## 🔄 CI/CD Integration

### **GitHub Actions Workflow**
```yaml
name: API Tests

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  api-tests:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Set up Python
      uses: actions/setup-python@v3
      with:
        python-version: '3.9'
    
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install requests pytest pytest-html
    
    - name: Run API tests
      env:
        TWITTER_BEARER_TOKEN: ${{ secrets.TWITTER_BEARER_TOKEN }}
      run: |
        python -m pytest tests/api/ --html=reports/api-test-report.html --self-contained-html
    
    - name: Upload test results
      uses: actions/upload-artifact@v3
      with:
        name: api-test-results
        path: reports/
    
    - name: Run Newman tests
      run: |
        npm install -g newman
        newman run "postman/Twitter API Tests.postman_collection.json" \
          -e "postman/Twitter API Environment.postman_environment.json" \
          --reporters cli,json \
          --reporter-json-export newman-results.json
    
    - name: Upload Newman results
      uses: actions/upload-artifact@v3
      with:
        name: newman-results
        path: newman-results.json
```

---

## 💡 Best Practices

### **1. Test Organization**
- **Group Related Tests:** Organize tests by API endpoint or functionality
- **Use Descriptive Names:** Clear test names that explain what is being tested
- **Separate Test Data:** Keep test data separate from test logic
- **Use Fixtures:** Reuse common setup and teardown code

### **2. Error Handling**
- **Test Error Scenarios:** Include tests for expected error conditions
- **Validate Error Responses:** Check error messages and status codes
- **Handle Timeouts:** Set appropriate timeout values
- **Log Errors:** Provide meaningful error messages

### **3. Data Management**
- **Clean Test Data:** Clean up test data after tests
- **Use Unique Data:** Avoid conflicts between test runs
- **Mock External Dependencies:** Mock external services when possible
- **Test Data Isolation:** Ensure tests don't interfere with each other

### **4. Performance Considerations**
- **Parallel Execution:** Run tests in parallel when possible
- **Optimize Requests:** Minimize unnecessary API calls
- **Use Connection Pooling:** Reuse HTTP connections
- **Monitor Response Times:** Track and alert on slow responses

### **5. Security**
- **Secure Token Management:** Never hardcode sensitive data
- **Use Environment Variables:** Store secrets in environment variables
- **Validate Authentication:** Test authentication and authorization
- **Check Input Validation:** Test for security vulnerabilities

**Remember:** Automated API testing is about creating reliable, maintainable, and valuable tests that provide confidence in your API's functionality. Focus on testing the most critical paths, handling errors gracefully, and integrating with your development workflow.

Happy automating! 🚀
Absolutely! Here’s a clear, code-free summary of the **approach to Automated API Testing** for your roadmap:

---

# Automated API Testing: Approach & Methodology

## 1. **Define Testing Objectives**
- Identify what you want to validate: functionality, reliability, security, performance, and data integrity.
- Prioritize critical business flows and high-risk endpoints.

## 2. **Select the Right Tools**
- Choose tools that fit your tech stack, team skills, and CI/CD environment (e.g., Postman, REST Assured, pytest, Supertest, Newman).
- Ensure the tool supports automation, reporting, and integration with your workflow.

## 3. **Design Test Cases**
- **Functional Tests:** Validate each endpoint’s expected behavior (CRUD operations, authentication, error handling).
- **Negative Tests:** Check how the API handles invalid input, missing data, and unauthorized access.
- **Boundary & Edge Cases:** Test limits (e.g., max string length, empty payloads).
- **Data-Driven Tests:** Use multiple data sets to cover a wide range of scenarios.

## 4. **Organize Test Suites**
- Group tests by resource (e.g., user, tweet, search) or by feature (e.g., authentication, rate limiting).
- Separate smoke, regression, and integration tests for efficient execution.

## 5. **Manage Test Data**
- Use fixtures or setup/teardown methods to create and clean up test data.
- Isolate test data to avoid conflicts and ensure repeatability.
- Use environment variables for sensitive data (tokens, credentials).

## 6. **Automate Test Execution**
- Integrate tests into your CI/CD pipeline (e.g., GitHub Actions, Jenkins).
- Run tests automatically on code changes, deployments, or scheduled intervals.
- Generate and store test reports for traceability.

## 7. **Validate Responses**
- Check HTTP status codes, response times, and headers.
- Validate response body structure and content (JSON schema, required fields).
- Assert business logic and data consistency.

## 8. **Handle Dependencies and Mocking**
- Mock external services or unstable dependencies to ensure reliable test runs.
- Use stubs or service virtualization for third-party APIs.

## 9. **Monitor and Maintain**
- Regularly review and update tests as APIs evolve.
- Remove obsolete tests and add new ones for new features or bug fixes.
- Monitor test flakiness and address root causes.

## 10. **Reporting and Feedback**
- Use dashboards or reports to visualize test results and trends.
- Provide actionable feedback to developers and stakeholders.
- Track coverage and identify gaps in testing.

---

**Summary:**  
Automated API testing is a systematic process: define goals, select tools, design and organize tests, manage data, automate execution, validate results, and continuously improve. The focus is on reliability, maintainability, and integration with your development lifecycle.
