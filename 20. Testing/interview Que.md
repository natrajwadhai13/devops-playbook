---
title: "• Interview Quetion"
parent: "20. Testing"
nav_order: 3
has_children: true
---

Here are **30 Technical QA Interview Questions with clear & simple answers** — suitable for **2–3 years experience** (Manual + API + SQL + Selenium basics + Framework knowledge).

---

# ✅ **30 Technical QA Interview Questions & Answers**

![Image](https://i.ytimg.com/vi/yRokoAy9Cog/hq720.jpg?rs=AOn4CLAwv5sPF6ziR5joKwGNdG0nh2zi3w\&sqp=-oaymwEhCK4FEIIDSFryq4qpAxMIARUAAAAAGAElAADIQj0AgKJD\&utm_source=chatgpt.com)

![Image](https://media.geeksforgeeks.org/wp-content/uploads/20240529151025/Selenium-features--660.webp?utm_source=chatgpt.com)

![Image](https://images.ctfassets.net/vztl6s0hp3ro/7zfHYM9IOABrGl5bAuUNH2/2a649cfcc97356bd7708b8a1168ef34d/5-basic-software-testing-questions-1024x1024.jpg?utm_source=chatgpt.com)

---

# 🔹 **MANUAL TESTING – TECHNICAL QUESTIONS**

### **1. What are the different types of testing?**

Functional, Regression, Smoke, Sanity, UAT, Integration, System testing, Ad-hoc, Exploratory.

---

### **2. What is Boundary Value Analysis (BVA)?**

Testing values at the **boundary limits**.
Example: Input 1–10 → test 0, 1, 10, 11.

---

### **3. What is Equivalence Partitioning?**

Divide input data into groups; test **one value** from each group.

---

### **4. What is Use Case Testing?**

Testing the application based on **real-life user flows**.

---

### **5. What are Positive & Negative Test Cases?**

* Positive → Valid inputs (should pass)
* Negative → Invalid inputs (should fail gracefully)

---

### **6. What is Defect Clustering?**

Most defects are found in **few modules**.

---

### **7. What is Pairwise Testing?**

Technique to test **all combinations of input pairs**.

---

### **8. What is the difference between Verification & Validation?**

* Verification: Process check
* Validation: Actual testing of product

---

### **9. What is Test Strategy?**

High-level document defining testing approach for the whole project.

---

### **10. What is Test Plan?**

Document describing **scope, objectives, resources, schedule** of testing.

---

# 🔹 **API TESTING – TECHNICAL QUESTIONS**

### **11. What are the main HTTP methods?**

GET, POST, PUT, PATCH, DELETE.

---

### **12. What is the difference between PUT and PATCH?**

* PUT → Update full resource
* PATCH → Update partial resource

---

### **13. What is a Status Code? Give examples.**

Response code from server.
200 OK, 201 Created, 400 Bad Request, 401 Unauthorized, 404 Not Found, 500 Internal Server Error.

---

### **14. What is JSON?**

(JavaScript Object Notation) used to transfer data between client & server.

---

### **15. What is Authentication vs Authorization?**

* Authentication → Identity check
* Authorization → Access level check

---

### **16. How do you test APIs in Postman?**

By sending requests, validating:

* Status code
* Response body
* Response time
* Headers
* Authentication

---

### **17. What is an API Endpoint?**

URL where API is accessed.

---

### **18. How to validate response in Postman?**

Using **Tests** tab:

```
pm.expect(responseCode).to.equal(200);
```

---

# 🔹 **SQL – TECHNICAL QUESTIONS**

### **19. What is JOIN? Name types.**

JOIN combines data from multiple tables.
Types: INNER JOIN, LEFT JOIN, RIGHT JOIN, FULL JOIN.

---

### **20. Write a query to fetch employee name & department.**

```
SELECT emp.name, dept.dept_name 
FROM employee emp 
INNER JOIN department dept 
ON emp.dept_id = dept.id;
```

---

### **21. How do you check duplicate records?**

```
SELECT name, COUNT(*) 
FROM users 
GROUP BY name 
HAVING COUNT(*) > 1;
```

---

### **22. How do you delete a record safely?**

Use WHERE:

```
DELETE FROM employee WHERE id = 10;
```

---

### **23. How do you find the second highest salary?**

```
SELECT MAX(salary) 
FROM employee 
WHERE salary < (SELECT MAX(salary) FROM employee);
```

---

# 🔹 **SELENIUM – TECHNICAL QUESTIONS**

### **24. What are Selenium locators?**

ID, Name, ClassName, Xpath, CSS Selector, LinkText, PartialLinkText, TagName.

---

### **25. What is the difference between Xpath & CSS?**

* Xpath → Can traverse both forward/backward
* CSS → Faster, cannot traverse backward

---

### **26. What are Types of Waits?**

* Implicit Wait
* Explicit Wait (WebDriverWait)
* Fluent Wait

---

### **27. How do you handle dropdowns?**

```
Select select = new Select(element);
select.selectByVisibleText("Option");
```

---

### **28. How do you handle alerts in Selenium?**

```
Alert alert = driver.switchTo().alert();
alert.accept();
```

---

### **29. What is Page Object Model (POM)?**

Design pattern where each page is represented as a **class** → helps reduce duplication & increases maintainability.

---

### **30. What is TestNG? Why do we use it?**

Test framework used for:
✔ Annotations
✔ Assertions
✔ Parallel execution
✔ Test suites

---
