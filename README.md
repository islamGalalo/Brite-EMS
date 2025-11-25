# Brite-EMS Task

<sub><i>To be fully transparent, due to time constraints I used an AI tool to help with structuring and wording some parts of the documents. However, the analysis, test scenarios, and test case design are based on my own understanding and experience. The AI was only used as a support tool.</i></sub> 


### **Part 1: Requirement Review**

**A. Possible Test Scenarios:**
1.  **Positive Flow:** Creating an employee with all valid inputs.
2.  **Boundary Analysis:** Creating an employee with a 10-digit phone number and a 15-digit phone number.
3.  **Mandatory Field Check:** Attempting to save without Name, Email, Phone, or Joining Date.
4.  **Uniqueness Check:** Creating an employee with an email that already exists in the database.
5.  **Format Validation:** Entering an email without an "@" symbol or domain.
6.  **Data Type Validation:** Entering text/special characters in the Phone field.
7.  **Optional Field:** Creating an employee without entering a Department.
8.  **Search Functionality:** Searching by exact name, partial name, exact email, and partial email.
9.  **Filter Functionality:** Filtering the list by a specific Department and verifying only those employees appear.

**B. Missing or Unclear Requirements (Questions for the BA/PO):**
1.  **Input Limits:** What are the maximum character limits for Name and Email?
2.  **Date Constraints:** Are future dates allowed for "Joining Date"? Can we add employees retrospectively (past dates)?
3.  **Phone Format:** Does the system accept valid formatting characters (e.g., `+`, `-`, `()`) and strip them, or must the user type strictly digits?
4.  **Search Behavior:** Should the search be case-sensitive?
5.  **Department Field:** Is this a free-text field or a dropdown menu? If text, are there restricted values?
6.  **Pagination:** How many records are displayed per page in the Employee List?

---

### **Part 2 & 3: Test Case Design & Execution**

*Note: As per instructions, the **Actual Result** and **Status** reflect the simulated execution where Duplicate Emails are allowed (Bug) and Partial Search fails (Bug).*

| ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **TC01** | Verify successful employee creation with all valid data | User is on "Create Employee" page | 1. Enter Name: "John Doe"<br>2. Enter Email: "john.doe@test.com"<br>3. Enter Phone: "1234567890"<br>4. Select Dept: "IT"<br>5. Select Date<br>6. Click Save | Employee created successfully; Redirect to List page. | Employee created successfully. | **PASS** |
| **TC02** | Verify error message when mandatory fields are empty | User is on "Create Employee" page | 1. Leave Name, Email, Phone blank.<br>2. Enter Dept: "HR"<br>3. Click Save | System displays "Required field" error messages. | System displayed errors for mandatory fields. | **PASS** |
| **TC03** | Verify system rejects invalid email formats | User is on "Create Employee" page | 1. Enter Name: "Jane"<br>2. Enter Email: "jane.com" (missing @)<br>3. Fill other valid data.<br>4. Click Save | Error message: "Invalid email format". | Error message displayed. | **PASS** |
| **TC04** | **Verify system prevents duplicate emails** | User "john.doe@test.com" already exists | 1. Enter Name: "John Two"<br>2. Enter Email: "john.doe@test.com"<br>3. Fill other valid data.<br>4. Click Save | **Error message: "Email already exists". Employee NOT created.** | **System accepted the duplicate email. Employee created.** | **FAIL** |
| **TC05** | Verify Phone field accepts only numbers | User is on "Create Employee" page | 1. Enter Phone: "12345ABCDE"<br>2. Fill other valid data.<br>3. Click Save | Error message: "Phone must contain only numbers". | Error message displayed. | **PASS** |
| **TC06** | Verify Phone field length constraints (Boundary) | User is on "Create Employee" page | 1. Try Phone: "12345" (Too short)<br>2. Try Phone: "1234567890123456" (Too long)<br>3. Click Save | Error message: "Phone must be 10-15 digits". | Error message displayed. | **PASS** |
| **TC07** | Verify employee creation without Department (Optional) | User is on "Create Employee" page | 1. Fill all mandatory fields.<br>2. Leave Department blank.<br>3. Click Save | Employee created successfully. | Employee created successfully. | **PASS** |
| **TC08** | Verify Employee List page displays data | Employees exist in the system | 1. Navigate to Employee List page. | Table displays Name, Email, Dept, etc. | Table loaded correctly. | **PASS** |
| **TC09** | **Verify Search by Partial Name** | Employee "Michael Scott" exists | 1. In search bar, type "Mich"<br>2. Click Search | **Row with "Michael Scott" is displayed.** | **No results found.** | **FAIL** |
| **TC10** | Verify Search by Exact Name | Employee "Michael Scott" exists | 1. In search bar, type "Michael Scott"<br>2. Click Search | Row with "Michael Scott" is displayed. | Row with "Michael Scott" is displayed. | **PASS** |
| **TC11** | Verify Filter by Department | Multiple employees in IT and HR exist | 1. Select "IT" from Department filter. | Only employees with Department "IT" are shown. | Only IT employees shown. | **PASS** |

---

### **Part 4: Bug Reporting**

#### **Bug Report 1**
**Bug ID:** BUG-001
**Title:** System allows creation of employees with duplicate email addresses.
**Description:** The requirements state that "Email must be unique." However, the system currently allows a user to create a new employee record using an email address that is already assigned to another existing employee.
**Steps to Reproduce:**
1. Navigate to the "Create Employee" page.
2. Create an employee with Email: `test@domain.com` and save.
3. Refresh the page or click "Create New".
4. Enter different Name details but enter the SAME Email: `test@domain.com`.
5. Fill remaining mandatory fields.
6. Click "Save".
**Expected Result:** The system should display a validation error: "Email already exists" and prevent saving.
**Actual Result:** The system saves the new employee, resulting in two records with the same email.
**Severity:** High (Violates database integrity and business rules).
**Priority:** High (Must be fixed before release).

***

#### **Bug Report 2**
**Bug ID:** BUG-002
**Title:** Search functionality does not return results for partial keyword matches.
**Description:** The Employee List search bar only returns results when the exact full name is entered. Users cannot find employees by typing the first name or a substring of the name.
**Steps to Reproduce:**
1. Navigate to the Employee List page.
2. Identify an existing employee (e.g., Name: "Sarah Jones").
3. In the search bar, type "Sarah".
4. Press Enter/Click Search.
**Expected Result:** The table should filter to show "Sarah Jones".
**Actual Result:** The table displays "No records found" or remains empty.
**Severity:** Medium (Usability issue).
**Priority:** Medium (Impedes workflow but workarounds exist via exact typing).

Here is the automation script rewritten using **Selenium WebDriver with Java**.

### Prerequisites
*   **Java Development Kit (JDK)** installed.
*   **IDE** (Eclipse, IntelliJ IDEA).
*   **Selenium Dependencies** (If using Maven, add `selenium-java` to your `pom.xml`).
*   **ChromeDriver** (Selenium 4.6+ manages this automatically; otherwise, download the driver matching your Chrome version).

