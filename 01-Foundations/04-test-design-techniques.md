# Test Design Techniques

Test design techniques help create effective test cases. Common techniques include:

## Black-box Testing
Focuses on input/output without knowing internal code.

## White-box Testing
Requires knowledge of the internal code structure.

## Grey-box Testing
Combines both black-box and white-box approaches.

## Other Techniques
- Equivalence Partitioning
- Boundary Value Analysis
- Decision Table Testing
- State Transition Testing

Using these techniques ensures thorough and efficient testing.

---

## 🔍 Black-Box Testing: Detailed Approach

Black-box testing treats the software as a "black box" - we test functionality without knowing the internal implementation. We focus on inputs, outputs, and expected behavior.

### 🎯 Black-Box Testing Techniques

#### 1. **Equivalence Partitioning**
Divide input data into valid and invalid partitions, then test one value from each partition.

#### 2. **Boundary Value Analysis**
Test values at the boundaries of input ranges (minimum, maximum, just inside/outside boundaries).

#### 3. **Decision Table Testing**
Create a table of conditions and actions to test different combinations of inputs.

#### 4. **State Transition Testing**
Test different states of the application and transitions between them.

#### 5. **Error Guessing**
Use experience to predict where errors might occur.

---

## 📱 Real-World Example: X (Twitter) Tweet Creation

### **Equivalence Partitioning Example**

**Feature:** Tweet character limit (280 characters)

**Valid Partitions:**
- Empty tweet (0 characters)
- Short tweet (1-50 characters)
- Medium tweet (51-200 characters)
- Long tweet (201-280 characters)

**Invalid Partitions:**
- Tweet with 281+ characters
- Tweet with special characters only
- Tweet with only spaces

**Test Cases:**
```markdown
| Test Case | Input | Expected Result | Partition |
|-----------|-------|-----------------|-----------|
| TC_001 | "" (empty) | Tweet posted successfully | Valid - Empty |
| TC_002 | "Hello World" | Tweet posted successfully | Valid - Short |
| TC_003 | [200 characters] | Tweet posted successfully | Valid - Medium |
| TC_004 | [280 characters] | Tweet posted successfully | Valid - Long |
| TC_005 | [281 characters] | Error: "Tweet is too long" | Invalid - Too Long |
| TC_006 | "   " (spaces only) | Error: "Tweet cannot be empty" | Invalid - Spaces Only |
```

### **Boundary Value Analysis Example**

**Feature:** Tweet character limit boundaries

**Boundary Values:**
- 0 characters (lower boundary)
- 1 character (just above lower boundary)
- 279 characters (just below upper boundary)
- 280 characters (upper boundary)
- 281 characters (just above upper boundary)

**Test Cases:**
```markdown
| Test Case | Input Length | Input | Expected Result |
|-----------|-------------|-------|-----------------|
| TC_007 | 0 | "" | Error: "Tweet cannot be empty" |
| TC_008 | 1 | "A" | Tweet posted successfully |
| TC_009 | 279 | [279 chars] | Tweet posted successfully |
| TC_010 | 280 | [280 chars] | Tweet posted successfully |
| TC_011 | 281 | [281 chars] | Error: "Tweet is too long" |
```

### **Decision Table Testing Example**

**Feature:** Tweet posting with different conditions**

**Conditions:**
- C1: User is logged in (Yes/No)
- C2: Tweet has content (Yes/No)
- C3: Tweet length ≤ 280 chars (Yes/No)
- C4: User has posting permissions (Yes/No)

**Actions:**
- A1: Allow tweet posting
- A2: Show login prompt
- A3: Show "empty tweet" error
- A4: Show "too long" error
- A5: Show "no permission" error

**Decision Table:**
```markdown
| Rule | C1 | C2 | C3 | C4 | Action |
|------|----|----|----|----|--------|
| 1 | Y | Y | Y | Y | A1 - Allow posting |
| 2 | N | Y | Y | Y | A2 - Show login prompt |
| 3 | Y | N | Y | Y | A3 - Show empty error |
| 4 | Y | Y | N | Y | A4 - Show too long error |
| 5 | Y | Y | Y | N | A5 - Show permission error |
| 6 | Y | N | N | Y | A3 - Show empty error (priority) |
| 7 | N | N | Y | Y | A2 - Show login prompt (priority) |
| 8 | N | N | N | N | A2 - Show login prompt (priority) |
```

### **State Transition Testing Example**

**Feature:** Tweet editing functionality

**States:**
- S1: Tweet posted (initial state)
- S2: Tweet being edited
- S3: Tweet saved after edit
- S4: Edit window closed

**Transitions:**
```markdown
| Current State | Event | Next State | Expected Result |
|---------------|-------|------------|-----------------|
| S1 | Click "Edit" | S2 | Edit window opens |
| S2 | Click "Save" | S3 | Tweet updated, edit window closes |
| S2 | Click "Cancel" | S1 | Edit window closes, no changes |
| S2 | 2 minutes pass | S1 | Edit window closes automatically |
| S3 | Click "Edit" | S2 | Edit window opens again |
| S1 | Click "Delete" | S4 | Tweet deleted |
```

---

## 🧪 Practical Test Cases for X (Twitter)

### **Test Case 1: Valid Tweet Creation**
```markdown
**Test ID:** TC_BB_001
**Technique:** Equivalence Partitioning
**Feature:** Tweet Creation

**Preconditions:**
- User is logged into X (Twitter)
- User has posting permissions

**Test Steps:**
1. Navigate to tweet compose area
2. Enter text: "This is a test tweet for QA learning! #testing"
3. Click "Post" button

**Expected Result:** Tweet is posted successfully and appears in timeline
**Test Data:** Valid tweet text within 280 characters
```

### **Test Case 2: Boundary Testing - Maximum Characters**
```markdown
**Test ID:** TC_BB_002
**Technique:** Boundary Value Analysis
**Feature:** Tweet Character Limit

**Preconditions:**
- User is logged into X (Twitter)

**Test Steps:**
1. Navigate to tweet compose area
2. Enter exactly 280 characters
3. Click "Post" button

**Expected Result:** Tweet is posted successfully
**Test Data:** 280-character string
```

### **Test Case 3: Invalid Input - Empty Tweet**
```markdown
**Test ID:** TC_BB_003
**Technique:** Error Guessing
**Feature:** Tweet Validation

**Preconditions:**
- User is logged into X (Twitter)

**Test Steps:**
1. Navigate to tweet compose area
2. Leave text area empty
3. Click "Post" button

**Expected Result:** Error message: "Tweet cannot be empty"
**Test Data:** Empty string
```

---

## 🎯 Best Practices for Black-Box Testing

1. **Focus on User Perspective:** Test as an end user would use the application
2. **Cover All Input Types:** Test valid, invalid, and boundary inputs
3. **Test Error Handling:** Verify appropriate error messages and recovery
4. **Consider Business Rules:** Understand and test business logic requirements
5. **Document Everything:** Record all test cases, results, and observations

---

## 📊 Black-Box vs Other Testing Types

| Aspect | Black-Box | White-Box | Grey-Box |
|--------|-----------|-----------|----------|
| **Knowledge Required** | No code knowledge | Full code knowledge | Partial code knowledge |
| **Focus** | Functionality | Code structure | Both functionality and structure |
| **Test Design** | Based on requirements | Based on code logic | Based on both |
| **User Perspective** | End-user view | Developer view | Mixed view |
| **Coverage** | Functional coverage | Code coverage | Both |

Using these techniques ensures thorough and efficient testing.
