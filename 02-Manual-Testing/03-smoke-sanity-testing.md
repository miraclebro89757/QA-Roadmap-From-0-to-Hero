# Smoke & Sanity Testing

Smoke and sanity testing are quick validation techniques used to determine if a build is stable enough for further testing. They act as gatekeepers to prevent wasting time on unstable builds.

## 🎯 What is Smoke Testing?

Smoke testing is a preliminary test that checks if the basic functionality of the application works. It's called "smoke testing" because if there's smoke (major issues), you don't need to look for fire (minor issues).

### **Purpose:**
- Verify that the build is stable enough for testing
- Check that critical functionality works
- Prevent wasting time on broken builds
- Provide quick feedback to development team

## 🧠 What is Sanity Testing?

Sanity testing is a focused test to verify that specific functionality or bug fixes work as expected. It's more targeted than smoke testing.

### **Purpose:**
- Verify specific changes work correctly
- Ensure bug fixes are effective
- Validate that new features function properly
- Confirm no regression in related areas

---

## 🚀 Why Smoke & Sanity Testing?

### ✅ **Benefits:**
- **Quick Feedback:** Fast results (usually 5-30 minutes)
- **Early Detection:** Catch major issues before detailed testing
- **Time Saving:** Avoid testing broken builds
- **Build Confidence:** Ensure stable foundation for testing
- **Automation Friendly:** Easy to automate and run frequently

### ❌ **Limitations:**
- **Shallow Coverage:** Only tests basic functionality
- **May Miss Issues:** Doesn't catch complex bugs
- **False Positives:** May pass even with serious issues
- **Not Comprehensive:** Should not replace detailed testing

---

## 🧪 Smoke Testing vs Sanity Testing

| Aspect | Smoke Testing | Sanity Testing |
|--------|---------------|----------------|
| **Scope** | Basic functionality | Specific functionality |
| **Timing** | After every build | After specific changes |
| **Duration** | 5-30 minutes | 10-60 minutes |
| **Focus** | Critical paths | Recent changes |
| **Automation** | Highly recommended | Recommended |
| **Frequency** | Every build | As needed |

---

## 📱 Real-World Example: X (Twitter) Smoke Testing

### **Smoke Test Suite (15 minutes)**

#### **1. Application Launch (2 minutes)**
```markdown
**Test Cases:**
- [ ] X (Twitter) application loads without errors
- [ ] Login page displays correctly
- [ ] No critical JavaScript errors in console
- [ ] Page loads within acceptable time (< 5 seconds)

**Expected Result:** Application starts successfully
**Pass/Fail Criteria:** All critical functions work
```

#### **2. User Authentication (3 minutes)**
```markdown
**Test Cases:**
- [ ] User can enter username/email
- [ ] User can enter password
- [ ] Login button is clickable
- [ ] Successful login redirects to home page
- [ ] Failed login shows appropriate error message

**Expected Result:** Authentication system works
**Pass/Fail Criteria:** Users can log in successfully
```

#### **3. Core Tweet Functionality (5 minutes)**
```markdown
**Test Cases:**
- [ ] Tweet compose area is accessible
- [ ] User can type text in tweet box
- [ ] Character counter updates correctly
- [ ] Post button is enabled with valid text
- [ ] Tweet appears in timeline after posting
- [ ] Basic tweet actions work (like, retweet)

**Expected Result:** Core tweet features work
**Pass/Fail Criteria:** Users can create and interact with tweets
```

#### **4. Navigation (3 minutes)**
```markdown
**Test Cases:**
- [ ] Home timeline loads
- [ ] Profile page is accessible
- [ ] Search functionality works
- [ ] Notifications page loads
- [ ] Settings page is accessible

**Expected Result:** Basic navigation works
**Pass/Fail Criteria:** Users can navigate between main sections
```

#### **5. Basic Responsiveness (2 minutes)**
```markdown
**Test Cases:**
- [ ] Page displays correctly on desktop
- [ ] Page displays correctly on mobile
- [ ] No horizontal scrolling issues
- [ ] Text is readable on all screen sizes

**Expected Result:** Application is responsive
**Pass/Fail Criteria:** Works on different screen sizes
```

---

## 🔍 X (Twitter) Sanity Testing Examples

### **Scenario 1: Tweet Character Limit Bug Fix**

#### **Sanity Test: Character Counter Fix (10 minutes)**
```markdown
**Background:** Bug reported that character counter doesn't update in real-time

**Test Cases:**
- [ ] Character counter starts at 280
- [ ] Counter decreases as user types
- [ ] Counter shows negative numbers when over limit
- [ ] Post button is disabled when over limit
- [ ] Counter resets when text is cleared
- [ ] Counter works with special characters and emojis

**Expected Result:** Character counter updates correctly in real-time
**Pass/Fail Criteria:** All counter behaviors work as expected
```

### **Scenario 2: New Tweet Threading Feature**

#### **Sanity Test: Tweet Threading (20 minutes)**
```markdown
**Background:** New feature to allow users to create tweet threads

**Test Cases:**
- [ ] "Add to thread" button appears after first tweet
- [ ] User can add multiple tweets to thread
- [ ] Thread tweets are linked together
- [ ] Thread tweets appear in correct order
- [ ] Individual tweets in thread can be liked/retweeted
- [ ] Thread tweets appear in user profile
- [ ] Thread tweets work in search results

**Expected Result:** Tweet threading feature works correctly
**Pass/Fail Criteria:** All threading functionality works as designed
```

---

## 🛠️ Smoke & Sanity Testing Tools

### **1. Test Management Tools**
- **Jira:** Create and track smoke/sanity test cases
- **TestRail:** Organize test suites and track execution
- **Azure DevOps:** Integrated testing with CI/CD

### **2. Automation Tools**
- **Selenium:** Web application automation
- **Cypress:** Modern web testing framework
- **Postman:** API testing automation
- **Jenkins:** CI/CD pipeline integration

### **3. Monitoring Tools**
- **Browser DevTools:** Check for JavaScript errors
- **Network Monitor:** Verify API calls and performance
- **Console Logs:** Debug application issues

---

## 📋 Smoke Testing Checklist Template

### **Pre-Smoke Testing**
- [ ] Build is deployed to test environment
- [ ] Test data is available
- [ ] Test environment is stable
- [ ] Smoke test cases are ready
- [ ] Team is notified of smoke testing

### **During Smoke Testing**
- [ ] Execute test cases systematically
- [ ] Document any failures immediately
- [ ] Check console for errors
- [ ] Verify basic functionality works
- [ ] Test on different browsers/devices

### **Post-Smoke Testing**
- [ ] Compile test results
- [ ] Report pass/fail status
- [ ] Communicate results to team
- [ ] Plan next steps based on results
- [ ] Update test cases if needed

---

## 🎯 Best Practices for Smoke & Sanity Testing

### **1. Keep Tests Simple**
- Focus on critical functionality only
- Avoid complex test scenarios
- Use clear pass/fail criteria
- Keep execution time short

### **2. Automate Where Possible**
- Automate repetitive smoke tests
- Run smoke tests in CI/CD pipeline
- Use parallel execution for speed
- Maintain automation scripts

### **3. Define Clear Criteria**
- Set specific pass/fail criteria
- Document expected behaviors
- Include performance benchmarks
- Define acceptable error rates

### **4. Regular Maintenance**
- Update test cases regularly
- Remove obsolete tests
- Add tests for new features
- Review and optimize test suite

### **5. Team Communication**
- Share results quickly
- Escalate failures immediately
- Document issues clearly
- Coordinate with development team

---

## 🔄 Integration with Development Workflow

### **CI/CD Pipeline Integration**
```yaml
# Example Jenkins Pipeline
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                // Build application
            }
        }
        stage('Smoke Test') {
            steps {
                // Run automated smoke tests
                sh 'npm run smoke-test'
            }
            post {
                failure {
                    // Notify team of smoke test failure
                    emailext subject: 'Smoke Test Failed'
                }
            }
        }
        stage('Deploy to Staging') {
            when {
                expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            steps {
                // Deploy only if smoke tests pass
            }
        }
    }
}
```

---

## 💡 Tips for Effective Smoke & Sanity Testing

1. **Start Simple:** Begin with basic functionality tests
2. **Be Consistent:** Use the same test cases every time
3. **Document Everything:** Record all results and issues
4. **Automate Early:** Automate repetitive smoke tests
5. **Communicate Results:** Share results with the team quickly
6. **Maintain Tests:** Keep test cases up to date
7. **Focus on Critical Paths:** Test what users need most
8. **Set Time Limits:** Don't spend too much time on these tests

**Remember:** Smoke and sanity testing are your first line of defense. They help ensure that your testing efforts are focused on stable, functional builds rather than broken code.
