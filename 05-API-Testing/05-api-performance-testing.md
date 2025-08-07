# API Performance Testing

API performance testing evaluates how well an API performs under various load conditions, ensuring it meets performance requirements and can handle expected user traffic.

## 🎯 What is API Performance Testing?

API performance testing measures and validates the performance characteristics of APIs, including:

- **Response Time:** How quickly the API responds to requests
- **Throughput:** Number of requests the API can handle per second
- **Concurrency:** How many simultaneous requests the API can process
- **Scalability:** How performance changes with increased load
- **Reliability:** API stability under stress conditions

### **Types of Performance Testing:**
- **Load Testing:** Test API under expected load
- **Stress Testing:** Test API beyond its capacity
- **Spike Testing:** Test sudden increases in load
- **Endurance Testing:** Test API over extended periods
- **Scalability Testing:** Test API with increasing load

---

## 📊 Performance Metrics

### **Key Performance Indicators (KPIs)**

#### **Response Time Metrics**
- **Average Response Time:** Mean time to respond
- **Median Response Time:** Middle value of response times
- **95th Percentile:** 95% of requests respond within this time
- **99th Percentile:** 99% of requests respond within this time
- **Maximum Response Time:** Slowest response time

#### **Throughput Metrics**
- **Requests Per Second (RPS):** Number of requests handled per second
- **Transactions Per Second (TPS):** Number of successful transactions per second
- **Concurrent Users:** Number of simultaneous users the API can handle

#### **Error Metrics**
- **Error Rate:** Percentage of failed requests
- **Timeout Rate:** Percentage of requests that timeout
- **Availability:** Percentage of time API is available

#### **Resource Metrics**
- **CPU Usage:** Server CPU utilization
- **Memory Usage:** Server memory consumption
- **Network I/O:** Network bandwidth usage
- **Database Connections:** Database connection pool usage

---

## 🛠️ Performance Testing Tools

### **1. Apache JMeter**
- **Open Source:** Free and widely used
- **GUI Interface:** Easy to create test plans
- **Multiple Protocols:** HTTP, HTTPS, JDBC, LDAP
- **Reporting:** Built-in reporting and visualization
- **Plugins:** Extensible with plugins

### **2. Artillery**
- **Node.js Based:** JavaScript-based load testing
- **YAML Configuration:** Simple configuration format
- **Real-time Metrics:** Live performance metrics
- **Cloud Integration:** Easy cloud deployment
- **Custom Functions:** JavaScript functions for complex scenarios

### **3. k6 (Grafana)**
- **Modern JavaScript:** ES6+ JavaScript for test scripts
- **Developer Friendly:** Familiar syntax for developers
- **Cloud Platform:** Grafana Cloud integration
- **Real-time Monitoring:** Live metrics and alerts
- **Extensible:** Plugin system for custom functionality

### **4. Gatling**
- **Scala Based:** High-performance load testing
- **Real-time Reports:** Live reporting during tests
- **Scalable:** Can handle high load scenarios
- **CI/CD Integration:** Easy integration with pipelines
- **Recorder:** GUI recorder for test creation

### **5. Postman/Newman**
- **Collection Based:** Use existing Postman collections
- **Easy Setup:** Simple configuration
- **CI/CD Ready:** Newman for command-line execution
- **Reporting:** Built-in reporting features
- **Team Collaboration:** Share test collections

---

## 📱 Real-World Example: X (Twitter) API Performance Testing

### **Performance Requirements**
```
Response Time:
- Average: < 200ms
- 95th percentile: < 500ms
- 99th percentile: < 1000ms

Throughput:
- Read operations: 10,000 RPS
- Write operations: 1,000 RPS
- Concurrent users: 50,000

Availability:
- 99.9% uptime
- Error rate: < 0.1%
```

### **Test Scenarios**

#### **1. User Profile Loading**
- **Scenario:** Multiple users loading profiles simultaneously
- **Load:** 1,000 concurrent users
- **Duration:** 10 minutes
- **Expected:** < 300ms average response time

#### **2. Tweet Creation**
- **Scenario:** Users creating tweets under load
- **Load:** 500 concurrent users
- **Duration:** 5 minutes
- **Expected:** < 500ms average response time

#### **3. Search Operations**
- **Scenario:** Users searching for tweets
- **Load:** 2,000 concurrent users
- **Duration:** 15 minutes
- **Expected:** < 400ms average response time

---

## 🔧 Performance Testing Examples

### **1. Apache JMeter Test Plan**

#### **JMeter Test Structure**
```
Test Plan
├── Thread Group (Users)
│   ├── HTTP Request Defaults
│   ├── HTTP Header Manager
│   ├── HTTP Request (Get User Profile)
│   ├── HTTP Request (Create Tweet)
│   ├── HTTP Request (Search Tweets)
│   └── Response Assertion
├── Listeners
│   ├── View Results Tree
│   ├── Aggregate Report
│   └── Graph Results
└── Configuration Elements
    ├── CSV Data Set Config
    └── User Defined Variables
```

#### **JMeter Test Script Example**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<jmeterTestPlan version="1.2" properties="5.0">
  <hashTree>
    <TestPlan guiclass="TestPlanGui" testclass="TestPlan" testname="Twitter API Performance Test">
      <elementProp name="TestPlan.arguments" elementType="Arguments">
        <collectionProp name="Arguments.arguments"/>
      </elementProp>
      <boolProp name="TestPlan.functional_mode">false</boolProp>
      <stringProp name="TestPlan.user_define_classpath"></stringProp>
    </TestPlan>
    <hashTree>
      <ThreadGroup guiclass="ThreadGroupGui" testclass="ThreadGroup" testname="User Load Test">
        <elementProp name="ThreadGroup.main_controller" elementType="LoopController">
          <boolProp name="LoopController.continue_forever">false</boolProp>
          <stringProp name="LoopController.loops">10</stringProp>
        </elementProp>
        <stringProp name="ThreadGroup.num_threads">100</stringProp>
        <stringProp name="ThreadGroup.ramp_time">60</stringProp>
        <boolProp name="ThreadGroup.scheduler">false</boolProp>
        <stringProp name="ThreadGroup.duration"></stringProp>
        <stringProp name="ThreadGroup.delay"></stringProp>
      </ThreadGroup>
      <hashTree>
        <HTTPSamplerProxy guiclass="HttpTestSampleGui" testclass="HTTPSamplerProxy" testname="Get User Profile">
          <elementProp name="HTTPsampler.Arguments" elementType="Arguments">
            <collectionProp name="Arguments.arguments"/>
          </elementProp>
          <stringProp name="HTTPSampler.domain">api.twitter.com</stringProp>
          <stringProp name="HTTPSampler.port">443</stringProp>
          <stringProp name="HTTPSampler.protocol">https</stringProp>
          <stringProp name="HTTPSampler.path">/v2/users/by/username/jayzhai</stringProp>
          <stringProp name="HTTPSampler.method">GET</stringProp>
        </HTTPSamplerProxy>
        <hashTree>
          <HeaderManager guiclass="HeaderPanel" testclass="HeaderManager" testname="HTTP Header Manager">
            <collectionProp name="HeaderManager.headers">
              <elementProp name="" elementType="Header">
                <stringProp name="Header.name">Authorization</stringProp>
                <stringProp name="Header.value">Bearer ${TWITTER_BEARER_TOKEN}</stringProp>
              </elementProp>
            </collectionProp>
          </HeaderManager>
          <hashTree/>
        </hashTree>
      </hashTree>
    </hashTree>
  </hashTree>
</jmeterTestPlan>
```

### **2. Artillery Test Script**

#### **Artillery Configuration**
```yaml
config:
  target: 'https://api.twitter.com'
  phases:
    - duration: 60
      arrivalRate: 10
      name: "Warm up"
    - duration: 300
      arrivalRate: 50
      name: "Sustained load"
    - duration: 120
      arrivalRate: 100
      name: "Peak load"
  defaults:
    headers:
      Authorization: 'Bearer {{ $processEnvironment.TWITTER_BEARER_TOKEN }}'
      Content-Type: 'application/json'

scenarios:
  - name: "User Profile Loading"
    weight: 40
    flow:
      - get:
          url: "/v2/users/by/username/jayzhai"
          expect:
            - statusCode: 200
            - contentType: json
            - hasProperty: data
            - maxResponseTime: 500

  - name: "Tweet Creation"
    weight: 20
    flow:
      - post:
          url: "/v2/tweets"
          json:
            text: "Performance test tweet {{ $randomString() }} #QA #Testing"
          expect:
            - statusCode: 201
            - contentType: json
            - hasProperty: data.id
            - maxResponseTime: 1000

  - name: "Search Tweets"
    weight: 40
    flow:
      - get:
          url: "/v2/tweets/search/recent"
          qs:
            query: "automation"
            max_results: 10
          expect:
            - statusCode: 200
            - contentType: json
            - hasProperty: data
            - maxResponseTime: 800
```

### **3. k6 Test Script**

#### **k6 Performance Test**
```javascript
import http from 'k6/http';
import { check, sleep } from 'k6';
import { Rate } from 'k6/metrics';

const errorRate = new Rate('errors');

export const options = {
  stages: [
    { duration: '2m', target: 100 }, // Ramp up to 100 users
    { duration: '5m', target: 100 }, // Stay at 100 users
    { duration: '2m', target: 200 }, // Ramp up to 200 users
    { duration: '5m', target: 200 }, // Stay at 200 users
    { duration: '2m', target: 0 },   // Ramp down to 0 users
  ],
  thresholds: {
    http_req_duration: ['p(95)<500'], // 95% of requests must complete below 500ms
    http_req_failed: ['rate<0.1'],    // Error rate must be less than 10%
    errors: ['rate<0.1'],             // Custom error rate
  },
};

const BASE_URL = 'https://api.twitter.com/v2';
const HEADERS = {
  'Authorization': `Bearer ${__ENV.TWITTER_BEARER_TOKEN}`,
  'Content-Type': 'application/json',
};

export default function () {
  const userId = __VU; // Virtual user ID
  
  // Test 1: Get user profile
  const profileResponse = http.get(`${BASE_URL}/users/by/username/jayzhai`, {
    headers: HEADERS,
  });
  
  check(profileResponse, {
    'profile status is 200': (r) => r.status === 200,
    'profile response time < 300ms': (r) => r.timings.duration < 300,
    'profile has user data': (r) => r.json('data.username') === 'jayzhai',
  });
  
  errorRate.add(profileResponse.status !== 200);
  
  sleep(1);
  
  // Test 2: Create tweet
  const tweetText = `Performance test tweet ${userId} #QA #Testing`;
  const tweetResponse = http.post(`${BASE_URL}/tweets`, JSON.stringify({
    text: tweetText,
  }), {
    headers: HEADERS,
  });
  
  check(tweetResponse, {
    'tweet status is 201': (r) => r.status === 201,
    'tweet response time < 500ms': (r) => r.timings.duration < 500,
    'tweet was created': (r) => r.json('data.text') === tweetText,
  });
  
  errorRate.add(tweetResponse.status !== 201);
  
  sleep(2);
  
  // Test 3: Search tweets
  const searchResponse = http.get(`${BASE_URL}/tweets/search/recent?query=automation&max_results=10`, {
    headers: HEADERS,
  });
  
  check(searchResponse, {
    'search status is 200': (r) => r.status === 200,
    'search response time < 400ms': (r) => r.timings.duration < 400,
    'search has results': (r) => r.json('data').length > 0,
  });
  
  errorRate.add(searchResponse.status !== 200);
  
  sleep(1);
}

export function handleSummary(data) {
  return {
    'performance-report.json': JSON.stringify(data),
    'performance-report.html': generateHTMLReport(data),
  };
}

function generateHTMLReport(data) {
  return `
    <html>
      <head><title>Performance Test Report</title></head>
      <body>
        <h1>API Performance Test Results</h1>
        <h2>Summary</h2>
        <p>Total Requests: ${data.metrics.http_reqs.values.count}</p>
        <p>Average Response Time: ${data.metrics.http_req_duration.values.avg}ms</p>
        <p>95th Percentile: ${data.metrics.http_req_duration.values['p(95)']}ms</p>
        <p>Error Rate: ${(data.metrics.http_req_failed.values.rate * 100).toFixed(2)}%</p>
      </body>
    </html>
  `;
}
```

### **4. Python Performance Testing**

#### **Python with locust**
```python
from locust import HttpUser, task, between
import json
import random

class TwitterAPIUser(HttpUser):
    wait_time = between(1, 3)
    
    def on_start(self):
        """Set up headers for all requests"""
        self.headers = {
            'Authorization': f'Bearer {self.client.environment.parsed_options.twitter_token}',
            'Content-Type': 'application/json'
        }
    
    @task(3)
    def get_user_profile(self):
        """Get user profile - high frequency task"""
        response = self.client.get(
            "/v2/users/by/username/jayzhai",
            headers=self.headers,
            name="Get User Profile"
        )
        
        if response.status_code == 200:
            data = response.json()
            assert data['data']['username'] == 'jayzhai'
    
    @task(1)
    def create_tweet(self):
        """Create tweet - lower frequency task"""
        tweet_text = f"Performance test tweet {random.randint(1000, 9999)} #QA #Testing"
        payload = {"text": tweet_text}
        
        response = self.client.post(
            "/v2/tweets",
            json=payload,
            headers=self.headers,
            name="Create Tweet"
        )
        
        if response.status_code == 201:
            data = response.json()
            assert data['data']['text'] == tweet_text
    
    @task(2)
    def search_tweets(self):
        """Search tweets - medium frequency task"""
        queries = ["automation", "testing", "QA", "performance"]
        query = random.choice(queries)
        
        response = self.client.get(
            f"/v2/tweets/search/recent?query={query}&max_results=10",
            headers=self.headers,
            name="Search Tweets"
        )
        
        if response.status_code == 200:
            data = response.json()
            assert 'data' in data

# Run with: locust -f twitter_api_test.py --host=https://api.twitter.com --twitter-token=YOUR_TOKEN
```

---

## 📊 Performance Testing Strategy

### **1. Test Planning**
- **Define Objectives:** Set clear performance goals
- **Identify Scenarios:** Determine critical user journeys
- **Set Baselines:** Establish current performance metrics
- **Plan Resources:** Allocate testing infrastructure

### **2. Test Execution**
- **Start Small:** Begin with low load and gradually increase
- **Monitor Resources:** Track server and network metrics
- **Record Results:** Capture detailed performance data
- **Analyze Bottlenecks:** Identify performance issues

### **3. Results Analysis**
- **Compare Metrics:** Compare against performance requirements
- **Identify Trends:** Look for performance patterns
- **Root Cause Analysis:** Investigate performance issues
- **Optimization Recommendations:** Suggest improvements

### **4. Continuous Monitoring**
- **Production Monitoring:** Monitor API performance in production
- **Alerting:** Set up performance alerts
- **Trend Analysis:** Track performance over time
- **Capacity Planning:** Plan for future growth

---

## 💡 Best Practices

### **1. Test Environment**
- **Production-like:** Use environment similar to production
- **Isolated Testing:** Avoid interference from other tests
- **Data Management:** Use realistic test data
- **Network Conditions:** Test under various network conditions

### **2. Test Data**
- **Realistic Data:** Use data that represents real usage
- **Data Volume:** Test with appropriate data volumes
- **Data Cleanup:** Clean up test data after tests
- **Data Isolation:** Ensure test data doesn't affect production

### **3. Monitoring**
- **Real-time Monitoring:** Monitor performance during tests
- **Resource Monitoring:** Track server and database metrics
- **Error Tracking:** Monitor and analyze errors
- **Performance Baselines:** Establish and track baselines

### **4. Reporting**
- **Clear Metrics:** Present performance metrics clearly
- **Trend Analysis:** Show performance trends over time
- **Actionable Insights:** Provide recommendations for improvement
- **Stakeholder Communication:** Share results with relevant teams

### **5. Optimization**
- **Identify Bottlenecks:** Find performance bottlenecks
- **Optimize Queries:** Improve database and API queries
- **Caching Strategy:** Implement appropriate caching
- **Load Balancing:** Distribute load effectively

**Remember:** API performance testing is about ensuring your API can handle expected load while maintaining good response times and reliability. Focus on realistic scenarios, proper monitoring, and continuous improvement.

Happy performance testing! 🚀
