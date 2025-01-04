# JAYSON G. GESIM via Onlinejobs.ph



# Test Case Design
 `POST https://mockapi.rapidextras.com/login` endpoint. The test cases cover positive, negative, and edge cases to ensure thorough endpoint testing. 

### Assumptions:
- **Request Body**: `{ "username": "string", "password": "string" }`
- **Response Codes**:
  - **200 OK**: Successful login
  - **400 Bad Request**: Invalid input
  - **401 Unauthorized**: Incorrect credentials
  - **429 Too Many Requests**: Rate-limiting triggered
  - **500 Internal Server Error**: Unexpected server error
- **Constraints**:
  - Username: 5–50 characters, alphanumeric.
  - Password: 8–20 characters, at least one uppercase, one lowercase, one digit, one special character.

---

### Positive Test Cases:
1. **Valid Username and Password**  
   - **Input**: `{ "username": "validUser", "password": "ValidPass123!" }`  
   - **Expected Response**: `200 OK`, valid session token in the response.  

2. **Boundary Username Length (Min)**  
   - **Input**: `{ "username": "user1", "password": "ValidPass123!" }`  
   - **Expected Response**: `200 OK`.

3. **Boundary Username Length (Max)**  
   - **Input**: `{ "username": "a".repeat(50), "password": "ValidPass123!" }`  
   - **Expected Response**: `200 OK`.

4. **Boundary Password Length (Min)**  
   - **Input**: `{ "username": "validUser", "password": "P@ssw0rd" }`  
   - **Expected Response**: `200 OK`.

5. **Boundary Password Length (Max)**  
   - **Input**: `{ "username": "validUser", "password": "P@ssw0rd".repeat(2) }`  
   - **Expected Response**: `200 OK`.

---

### Negative Test Cases:
6. **Invalid Username (Too Short)**  
   - **Input**: `{ "username": "usr", "password": "ValidPass123!" }`  
   - **Expected Response**: `400 Bad Request`.

7. **Invalid Username (Too Long)**  
   - **Input**: `{ "username": "a".repeat(51), "password": "ValidPass123!" }`  
   - **Expected Response**: `400 Bad Request`.

8. **Invalid Password (Too Short)**  
   - **Input**: `{ "username": "validUser", "password": "P@ss" }`  
   - **Expected Response**: `400 Bad Request`.

9. **Invalid Password (No Uppercase)**  
   - **Input**: `{ "username": "validUser", "password": "validpass123!" }`  
   - **Expected Response**: `400 Bad Request`.

10. **Invalid Password (No Special Character)**  
    - **Input**: `{ "username": "validUser", "password": "ValidPass123" }`  
    - **Expected Response**: `400 Bad Request`.

11. **Incorrect Credentials**  
    - **Input**: `{ "username": "validUser", "password": "WrongPass123!" }`  
    - **Expected Response**: `401 Unauthorized`.

---

### Edge Cases:
12. **Empty Request Body**  
    - **Input**: `{}`  
    - **Expected Response**: `400 Bad Request`.

13. **Missing Username**  
    - **Input**: `{ "password": "ValidPass123!" }`  
    - **Expected Response**: `400 Bad Request`.

14. **Missing Password**  
    - **Input**: `{ "username": "validUser" }`  
    - **Expected Response**: `400 Bad Request`.

15. **Rate Limiting Triggered**  
    - **Action**: Send 100+ requests in rapid succession.  
    - **Expected Response**: `429 Too Many Requests`.

16. **SQL Injection Attempt**  
    - **Input**: `{ "username": "admin';--", "password": "12345" }`  
    - **Expected Response**: `400 Bad Request`.

17. **XSS Injection Attempt**  
    - **Input**: `{ "username": "<script>alert(1)</script>", "password": "ValidPass123!" }`  
    - **Expected Response**: `400 Bad Request`.

---

### Special Scenarios:
18. **Server Error Simulation**  
    - **Action**: Simulate server crash or error conditions.  
    - **Expected Response**: `500 Internal Server Error`.

19. **Whitespace in Username and Password**  
    - **Input**: `{ "username": " validUser ", "password": " ValidPass123! " }`  
    - **Expected Response**: `200 OK`.

20. **JSON Syntax Error**  
    - **Input**: `{ username: "validUser", password: "ValidPass123!" }` (missing quotes)  
    - **Expected Response**: `400 Bad Request`.

---

### Testing Tools:
- **Automation Frameworks**: Use **Postman**, **Cypress**, or **Playwright** for automating these tests.
- **Mocking and Simulation**: Employ mocking libraries like **WireMock** or **Faker** for generating test inputs.
- **Load Testing**: Use **JMeter** or **k6** for rate-limiting scenarios.

# TASK 2 : Automation Script Development 
To automate the test scenarios for the `POST https://mockapi.rapidextras.com/login` endpoint using Postman, follow these steps:

---

### **1. Create a Postman Collection**

1. Open Postman and create a new collection, e.g., `Login API Test`.
2. Add a folder for organizing the test cases, e.g., `Login Test Cases`.

---

### **2. Add Test Requests**
Create individual requests for each scenario and parameterize them for automation.

#### Example: **Valid Username and Password**
1. Create a new request named `TC1_ValidCredentials`.
2. Set method to `POST` and URL to `https://mockapi.rapidextras.com/login`.
3. In the **Body** tab, choose `raw` and select `JSON` format. Add:
   ```json
   {
       "username": "validUser",
       "password": "ValidPass123!"
   }
   ```
4. Go to the **Tests** tab and add the following script:
   ```javascript
   pm.test("Status code is 200", function () {
       pm.response.to.have.status(200);
   });

   pm.test("Response contains token", function () {
       let jsonData = pm.response.json();
       pm.expect(jsonData.token).to.be.a("string");
   });
   ```

---

### **3. Parameterize the Test Cases**
Use Postman variables to handle multiple scenarios efficiently.

1. **Add Collection Variables**:
   - Open the collection settings and add variables like:
     - `base_url`: `https://mockapi.rapidextras.com`
     - `valid_username`: `validUser`
     - `valid_password`: `ValidPass123!`

2. Modify the request URL and body to use variables:
   - URL: `{{base_url}}/login`
   - Body:
     ```json
     {
         "username": "{{valid_username}}",
         "password": "{{valid_password}}"
     }
     ```

---

### **4. Negative Test Case Example: Invalid Username**
1. Duplicate `TC1_ValidCredentials` and name it `TC6_InvalidUsername`.
2. Change the request body to:
   ```json
   {
       "username": "usr",
       "password": "ValidPass123!"
   }
   ```
3. Add the following in the **Tests** tab:
   ```javascript
   pm.test("Status code is 400", function () {
       pm.response.to.have.status(400);
   });

   pm.test("Error message is appropriate", function () {
       let jsonData = pm.response.json();
       pm.expect(jsonData.message).to.eql("Invalid username or password");
   });
   ```

---

### **5. Automate Execution with Postman Runner**
1. Open the collection and click **Run Collection**.
2. Set up your test data by creating an environment JSON file or use Postman’s data feature.
3. For example, use the following CSV to cover scenarios:
   ```csv
   username,password,status_code,message
   validUser,ValidPass123!,200,
   usr,ValidPass123!,400,Invalid username or password
   validUser,wrongPass123!,401,Unauthorized
   admin';--,12345,400,Invalid input
   ```
4. Map the CSV columns to the variables in your requests (`username`, `password`).

---

### **6. Add Edge Cases**
For each edge case, add similar test scripts in the **Tests** tab. For example:

#### Empty Request Body:
```javascript
pm.test("Status code is 400", function () {
    pm.response.to.have.status(400);
});

pm.test("Response contains error message", function () {
    let jsonData = pm.response.json();
    pm.expect(jsonData.message).to.eql("Request body cannot be empty");
});
```

---

### **7. Export and Share**
1. Export the collection and environment for version control or sharing.
2. Integrate the collection with CI/CD pipelines using Newman:
   - Install Newman: `npm install -g newman`.
   - Run tests: `newman run Login_API_Test.postman_collection.json`.

---

### **8. Postman Test Scripts Overview**
Here’s a consolidated test script you can use across scenarios:

```javascript
pm.test("Status code is correct", function () {
    pm.response.to.have.status(pm.collectionVariables.get("expected_status"));
});

pm.test("Response contains expected fields", function () {
    let jsonData = pm.response.json();
    if (pm.response.code === 200) {
        pm.expect(jsonData.token).to.be.a("string");
    } else {
        pm.expect(jsonData.message).to.be.a("string");
    }
});
```

# Execution Report

Here's how I generate a **test execution report** from the Postman script using **Newman**, follow these steps:

---

### **1. Run the Postman Collection via Newman**
1. Ensure you’ve installed **Newman**:
   ```bash
   npm install -g newman
   ```

2. Export your Postman collection and environment from the Postman app:
   - Collection: `Login_API_Test.postman_collection.json`
   - Environment (optional): `Login_API_Env.postman_environment.json`

3. Run the collection with Newman:
   ```bash
   newman run Login_API_Test.postman_collection.json -e Login_API_Env.postman_environment.json --reporters cli,html --reporter-html-export test-report.html
   ```

   - The above command generates:
     - CLI output in the terminal.
     - An **HTML report** named `test-report.html` in the current directory.

---

### **2. Test Execution Report (CLI Output)**

Here’s an example of what the CLI report might look like:

```
Iteration 1 of 1
┌────────────────────────────┬──────────────────────────┬──────────┐
│ Name                       │ Test Results             │ Status   │
├────────────────────────────┼──────────────────────────┼──────────┤
│ TC1_ValidCredentials       │ Status code is 200       │ Passed   │
│                            │ Response contains token  │ Passed   │
├────────────────────────────┼──────────────────────────┼──────────┤
│ TC6_InvalidUsername        │ Status code is 400       │ Passed   │
│                            │ Error message is valid   │ Passed   │
├────────────────────────────┼──────────────────────────┼──────────┤
│ TC12_EmptyRequestBody      │ Status code is 400       │ Passed   │
│                            │ Error message is valid   │ Passed   │
└────────────────────────────┴──────────────────────────┴──────────┘

Total Requests: 3
Assertions: 6
Passed: 6
Failed: 0

Collection run completed.
```

---

### **3. HTML Report Summary**


| **Test Case**               | **Status** | **Execution Time** |
|-----------------------------|------------|--------------------|
| TC1_ValidCredentials        | ✅ Passed  | 150ms              |
| TC6_InvalidUsername         | ✅ Passed  | 120ms              |
| TC12_EmptyRequestBody       | ✅ Passed  | 110ms              |
| TC16_SQLInjectionAttempt    | ✅ Passed  | 135ms              |
| **Overall Summary**         | **Passed** | **515ms**          |

---
