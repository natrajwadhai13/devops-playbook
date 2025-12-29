---
title: "• MANUAL TESTING"
parent: "20. Testing"
nav_order: 3
has_children: true
---


# ✅ **18 Technical QA Interview Questions & Answers**


## 🔹 **MANUAL TESTING – TECHNICAL QUESTIONS**

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