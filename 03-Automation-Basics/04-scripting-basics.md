# Scripting Basics

Scripting is a fundamental skill for QA automation. Scripts help automate repetitive tasks, process test data, and integrate different testing tools.

## 🎯 What is Scripting?

Scripting involves writing small programs to automate tasks and processes. For QA professionals, scripting is essential for:
- **Test Data Management:** Generate, clean, and validate test data
- **Environment Setup:** Automate test environment configuration
- **Process Automation:** Streamline repetitive testing tasks
- **Tool Integration:** Connect different testing tools and frameworks
- **Reporting:** Generate and format test reports

---

## 🐚 Shell Scripting (Bash/PowerShell)

### **Bash Script Example**
```bash
#!/bin/bash
# test_environment_setup.sh

# Configuration
TEST_ENV="staging"
BASE_URL="https://staging.twitter-clone.com"
LOG_FILE="setup.log"

# Function to log messages
log_message() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') - $1" | tee -a "$LOG_FILE"
}

# Check service health
check_service() {
    local service_name=$1
    local port=$2
    
    if curl -s "http://localhost:$port/health" > /dev/null; then
        log_message "✅ $service_name is running on port $port"
        return 0
    else
        log_message "❌ $service_name is not running on port $port"
        return 1
    fi
}

# Main execution
log_message "Starting test environment setup..."

# Check Docker
if ! docker info > /dev/null 2>&1; then
    log_message "❌ Docker is not running"
    exit 1
fi

# Start services
docker-compose -f docker-compose.test.yml up -d
sleep 30

# Check services
check_service "API" 3000
check_service "Database" 5432

# Setup test data
python3 scripts/setup_test_data.py --env "$TEST_ENV"

log_message "Test environment setup completed!"
```

### **PowerShell Script Example**
```powershell
# setup_test_environment.ps1

param(
    [string]$Environment = "staging",
    [string]$BaseUrl = "https://staging.twitter-clone.com"
)

function Write-Log {
    param([string]$Message)
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    Write-Host "$timestamp - $Message"
}

function Test-ServiceHealth {
    param([string]$ServiceName, [int]$Port)
    
    try {
        $response = Invoke-WebRequest -Uri "http://localhost:$Port/health" -TimeoutSec 5
        if ($response.StatusCode -eq 200) {
            Write-Log "✅ $ServiceName is running on port $Port"
            return $true
        }
    }
    catch {
        Write-Log "❌ $ServiceName is not running on port $Port"
        return $false
    }
}

# Main execution
Write-Log "Starting test environment setup..."

# Check Docker
try {
    docker info | Out-Null
    Write-Log "✅ Docker is running"
}
catch {
    Write-Log "❌ Docker is not running"
    exit 1
}

# Start services
docker-compose -f docker-compose.test.yml up -d
Start-Sleep -Seconds 30

# Check services
Test-ServiceHealth -ServiceName "API" -Port 3000
Test-ServiceHealth -ServiceName "Database" -Port 5432

Write-Log "Test environment setup completed!"
```

---

## 🐍 Python Scripting for QA

### **Test Data Management Script**
```python
#!/usr/bin/env python3
# test_data_manager.py

import json
import random
import string
import argparse
from datetime import datetime
from typing import List, Dict, Any

class TestDataManager:
    def __init__(self, output_dir: str = "test_data"):
        self.output_dir = output_dir
    
    def generate_random_string(self, length: int = 10) -> str:
        return ''.join(random.choices(string.ascii_letters + string.digits, k=length))
    
    def generate_user_data(self, count: int = 100) -> List[Dict[str, Any]]:
        users = []
        for i in range(1, count + 1):
            user = {
                "id": i,
                "username": f"testuser{i}",
                "email": f"test{i}@example.com",
                "password": f"TestPass{i}!",
                "display_name": f"Test User {i}",
                "created_at": datetime.now().isoformat()
            }
            users.append(user)
        return users
    
    def generate_tweet_data(self, count: int = 500, user_count: int = 100) -> List[Dict[str, Any]]:
        tweets = []
        for i in range(1, count + 1):
            user_id = random.randint(1, user_count)
            tweet_text = f"Test tweet {i} for automation testing. #QA #Testing"
            
            tweet = {
                "id": i,
                "user_id": user_id,
                "text": tweet_text,
                "created_at": datetime.now().isoformat(),
                "likes_count": random.randint(0, 100)
            }
            tweets.append(tweet)
        return tweets
    
    def save_data(self, filename: str, data: Any):
        filepath = f"{self.output_dir}/{filename}"
        with open(filepath, 'w', encoding='utf-8') as f:
            json.dump(data, f, indent=2, ensure_ascii=False)
        print(f"✅ Data saved to {filepath}")
    
    def generate_all_data(self, user_count: int = 100, tweet_count: int = 500):
        print(f"Generating {user_count} users and {tweet_count} tweets...")
        
        users = self.generate_user_data(user_count)
        tweets = self.generate_tweet_data(tweet_count, user_count)
        
        self.save_data("users.json", {"users": users})
        self.save_data("tweets.json", {"tweets": tweets})
        
        print("✅ All test data generated successfully!")

def main():
    parser = argparse.ArgumentParser(description="Generate test data for automation")
    parser.add_argument("--users", type=int, default=100, help="Number of users")
    parser.add_argument("--tweets", type=int, default=500, help="Number of tweets")
    parser.add_argument("--output-dir", default="test_data", help="Output directory")
    
    args = parser.parse_args()
    
    import os
    os.makedirs(args.output_dir, exist_ok=True)
    
    manager = TestDataManager(args.output_dir)
    manager.generate_all_data(args.users, args.tweets)

if __name__ == "__main__":
    main()
```

### **Test Environment Setup Script**
```python
#!/usr/bin/env python3
# setup_test_env.py

import requests
import time
import argparse
from typing import Dict

class TestEnvironmentSetup:
    def __init__(self, base_url: str, environment: str = "staging"):
        self.base_url = base_url
        self.environment = environment
        self.services = {
            "api": f"{base_url}/health",
            "database": f"{base_url}/db/health"
        }
    
    def check_service_health(self, service_name: str, url: str, timeout: int = 30) -> bool:
        try:
            response = requests.get(url, timeout=timeout)
            if response.status_code == 200:
                print(f"✅ {service_name} is healthy")
                return True
            else:
                print(f"❌ {service_name} returned status {response.status_code}")
                return False
        except requests.exceptions.RequestException as e:
            print(f"❌ {service_name} is not accessible: {e}")
            return False
    
    def wait_for_service(self, service_name: str, url: str, max_attempts: int = 30) -> bool:
        print(f"Waiting for {service_name} to be ready...")
        
        for attempt in range(max_attempts):
            if self.check_service_health(service_name, url, timeout=5):
                return True
            
            if attempt < max_attempts - 1:
                print(f"Attempt {attempt + 1}/{max_attempts}, retrying in 10 seconds...")
                time.sleep(10)
        
        print(f"❌ {service_name} failed to become ready")
        return False
    
    def setup_environment(self) -> bool:
        print(f"Setting up {self.environment} environment...")
        
        # Check all services
        all_services_ready = True
        for service_name, url in self.services.items():
            if not self.wait_for_service(service_name, url):
                all_services_ready = False
        
        if not all_services_ready:
            print("❌ Some services are not ready")
            return False
        
        print("✅ Environment setup completed successfully!")
        return True

def main():
    parser = argparse.ArgumentParser(description="Set up test environment")
    parser.add_argument("--base-url", required=True, help="Base URL of the application")
    parser.add_argument("--env", default="staging", help="Environment name")
    
    args = parser.parse_args()
    
    setup = TestEnvironmentSetup(args.base_url, args.env)
    success = setup.setup_environment()
    
    exit(0 if success else 1)

if __name__ == "__main__":
    main()
```

---

## 💻 JavaScript Scripting for Web Testing

### **Browser Automation Script**
```javascript
// browser_automation.js

const puppeteer = require('puppeteer');

class BrowserAutomation {
    constructor(baseUrl = 'https://staging.twitter-clone.com') {
        this.baseUrl = baseUrl;
        this.browser = null;
        this.page = null;
    }
    
    async initialize() {
        console.log('Initializing browser...');
        this.browser = await puppeteer.launch({
            headless: false,
            slowMo: 100
        });
        this.page = await this.browser.newPage();
        await this.page.setViewport({ width: 1280, height: 720 });
    }
    
    async navigateToHomepage() {
        console.log('Navigating to homepage...');
        await this.page.goto(this.baseUrl, { waitUntil: 'networkidle2' });
        await this.page.waitForSelector('body');
    }
    
    async login(username, password) {
        console.log(`Logging in as ${username}...`);
        
        const loginButton = await this.page.$('[data-testid="login-button"]');
        if (loginButton) {
            await loginButton.click();
        }
        
        await this.page.type('[data-testid="username-input"]', username);
        await this.page.type('[data-testid="password-input"]', password);
        await this.page.click('[data-testid="submit-button"]');
        
        await this.page.waitForSelector('[data-testid="user-profile"]', { timeout: 10000 });
        console.log('✅ Login successful');
    }
    
    async createTweet(tweetText) {
        console.log(`Creating tweet: ${tweetText.substring(0, 50)}...`);
        
        await this.page.click('[data-testid="tweet-compose-button"]');
        await this.page.type('[data-testid="tweet-textarea"]', tweetText);
        await this.page.click('[data-testid="tweet-submit-button"]');
        
        await this.page.waitForSelector('[data-testid="tweet-success-message"]', { timeout: 10000 });
        console.log('✅ Tweet created successfully');
    }
    
    async takeScreenshot(filename) {
        console.log(`Taking screenshot: ${filename}`);
        await this.page.screenshot({ path: filename, fullPage: true });
    }
    
    async close() {
        if (this.browser) {
            await this.browser.close();
            console.log('Browser closed');
        }
    }
}

// Example usage
async function runAutomationExample() {
    const automation = new BrowserAutomation();
    
    try {
        await automation.initialize();
        await automation.navigateToHomepage();
        await automation.login('testuser1', 'TestPass123!');
        await automation.createTweet('Hello from JavaScript automation! #QA #Testing');
        await automation.takeScreenshot('automation-result.png');
    } catch (error) {
        console.error('Automation failed:', error);
        await automation.takeScreenshot('error-screenshot.png');
    } finally {
        await automation.close();
    }
}

if (require.main === module) {
    runAutomationExample();
}

module.exports = BrowserAutomation;
```

---

## 🎯 Scripting Best Practices

### **1. Error Handling**
```python
# Python error handling
import logging
import sys

def setup_logging():
    logging.basicConfig(
        level=logging.INFO,
        format='%(asctime)s - %(levelname)s - %(message)s',
        handlers=[
            logging.FileHandler('script.log'),
            logging.StreamHandler(sys.stdout)
        ]
    )

def safe_execute(func, *args, **kwargs):
    try:
        return func(*args, **kwargs)
    except Exception as e:
        logging.error(f"Error executing {func.__name__}: {e}")
        return None
```

### **2. Configuration Management**
```python
# config.py
import os
import yaml
from typing import Dict, Any

class Config:
    def __init__(self, config_file: str = "config.yaml"):
        self.config_file = config_file
        self.config = self.load_config()
    
    def load_config(self) -> Dict[str, Any]:
        if os.path.exists(self.config_file):
            with open(self.config_file, 'r') as f:
                return yaml.safe_load(f)
        else:
            return self.get_default_config()
    
    def get_default_config(self) -> Dict[str, Any]:
        return {
            "environments": {
                "staging": {
                    "base_url": "https://staging.twitter-clone.com",
                    "timeout": 30
                },
                "production": {
                    "base_url": "https://twitter-clone.com",
                    "timeout": 60
                }
            }
        }
```

### **3. Logging and Reporting**
```python
# logging_setup.py
import logging
import json
from datetime import datetime
from typing import Dict, Any

class ScriptLogger:
    def __init__(self, script_name: str):
        self.script_name = script_name
        self.setup_logging()
        self.results = []
    
    def setup_logging(self):
        log_format = '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
        logging.basicConfig(
            level=logging.INFO,
            format=log_format,
            handlers=[
                logging.FileHandler(f'{self.script_name}.log'),
                logging.StreamHandler()
            ]
        )
        self.logger = logging.getLogger(self.script_name)
    
    def log_result(self, test_name: str, status: str, details: Dict[str, Any] = None):
        result = {
            "test_name": test_name,
            "status": status,
            "timestamp": datetime.now().isoformat(),
            "details": details or {}
        }
        self.results.append(result)
        
        if status == "PASS":
            self.logger.info(f"✅ {test_name}: PASS")
        else:
            self.logger.error(f"❌ {test_name}: FAIL - {details}")
    
    def generate_report(self) -> Dict[str, Any]:
        total = len(self.results)
        passed = len([r for r in self.results if r["status"] == "PASS"])
        failed = total - passed
        
        report = {
            "script_name": self.script_name,
            "execution_time": datetime.now().isoformat(),
            "summary": {
                "total": total,
                "passed": passed,
                "failed": failed,
                "pass_rate": (passed / total * 100) if total > 0 else 0
            },
            "results": self.results
        }
        
        report_file = f"{self.script_name}_report.json"
        with open(report_file, 'w') as f:
            json.dump(report, f, indent=2)
        
        self.logger.info(f"Report saved to {report_file}")
        return report
```

---

## 💡 Tips for Effective Scripting

### **1. Start Simple**
- Begin with basic scripts and gradually add complexity
- Focus on solving one problem at a time
- Test your scripts thoroughly before using in production

### **2. Use Version Control**
- Store your scripts in Git repositories
- Use meaningful commit messages
- Document your scripts with README files

### **3. Make Scripts Reusable**
- Use configuration files for different environments
- Create functions for common operations
- Design scripts to be modular and maintainable

### **4. Handle Errors Gracefully**
- Implement proper error handling
- Log errors and provide meaningful messages
- Use try-catch blocks appropriately

### **5. Document Everything**
- Add comments to explain complex logic
- Create usage examples
- Document dependencies and requirements

**Remember:** Scripting is a powerful tool for QA automation. Start with simple tasks and gradually build more complex solutions. Focus on creating scripts that solve real problems and make your testing more efficient.
