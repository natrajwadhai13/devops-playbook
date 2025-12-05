---
title: "• API + SQL"
parent: "20. Testing"
nav_order: 5
has_children: true
---


# 🟧 30 API + SQL Technical Questions (Advanced)**

![Image](https://www.specbee.com/sites/default/files/inline-images/postman_0.jpg?utm_source=chatgpt.com)

![Image](https://media.geeksforgeeks.org/wp-content/uploads/20240502174139/Types-of-Database-Testing.webp?utm_source=chatgpt.com)

![Image](https://www.lambdatest.com/dynamic-pages/resources/images/learning-hub/rest-api-testing-ensures-these-apis-function-correctly-before-deployment.webp?utm_source=chatgpt.com)

---

# 🔹 **API Testing – 15 Advanced Questions**

## **1. What is the difference between SOAP and REST?**

REST → Lightweight, uses JSON
SOAP → Strict protocol, XML-based, heavy

---

## **2. What is Idempotent API?**

Operations that produce the same result no matter how many times you call them.
Example: GET, PUT, DELETE.

---

## **3. What is Statelessness in REST?**

Server does not store client session — every request is independent.

---

## **4. What is API Contract?**

The agreed structure of request, response, parameters, and status codes.

---

## **5. What are headers in API?**

Key-value pairs sent to server:
Content-Type, Authorization, Accept.

---

## **6. What is OAuth 2.0?**

Authorization framework (Bearer token).

---

## **7. How do you validate JSON schema in Postman?**

Using `pm.expect()` with schema validation library.

---

## **8. How do you test pagination API?**

Check: page number, size, total elements, next/previous links.

---

## **9. How do you handle tokens in Postman?**

Use **Pre-request Script** to auto-generate token.

---

## **10. What is Rate Limiting in API?**

Limits number of API calls (ex: 100 requests/min).

---

## **11. How do you test file upload API?**

Use form-data in Postman.

---

## **12. What is the difference between 401 and 403?**

401 → Unauthorized (invalid or missing token)
403 → Forbidden (token valid but access denied)

---

## **13. What is a Mock API?**

Fake API used for testing when backend is not ready.

---

## **14. What is a web hook?**

Reverse API — server sends automatic data to client.

---

## **15. What is Postman Collection Runner?**

Used to run multiple test cases with **data files (CSV/JSON)**.

---

---

# 🔹 **SQL – 15 Advanced Questions**

## **16. Write a query to fetch the 3rd highest salary.**

```
SELECT salary 
FROM employee 
ORDER BY salary DESC 
LIMIT 1 OFFSET 2;
```

---

## **17. How do you find records that exist in one table but not in another?**

```
SELECT * FROM A 
WHERE id NOT IN (SELECT id FROM B);
```

---

## **18. Write a query to count employees in each department.**

```
SELECT dept_id, COUNT(*) 
FROM employee 
GROUP BY dept_id;
```

---

## **19. How do you find duplicate email IDs?**

```
SELECT email, COUNT(*) 
FROM users 
GROUP BY email 
HAVING COUNT(*) > 1;
```

---

## **20. Write a query to update salary by 10%.**

```
UPDATE employee 
SET salary = salary * 1.10;
```

---

## **21. How do you fetch records between two dates?**

```
SELECT * FROM orders 
WHERE order_date BETWEEN '2024-01-01' AND '2024-01-31';
```

---

## **22. What is a primary key vs foreign key?**

PK → unique row ID
FK → refers to PK of another table

---

## **23. What is normalization?**

Process to remove redundancy, improve consistency.

---

## **24. What is a transaction?**

Group of SQL operations executed as a single unit:
BEGIN → COMMIT/ROLLBACK

---

## **25. ACID properties — explain.**

Atomicity, Consistency, Isolation, Durability.

---

## **26. Write a query to get employee name & manager name.**

```
SELECT e.name, m.name AS manager 
FROM employee e 
LEFT JOIN employee m 
ON e.manager_id = m.id;
```

---

## **27. Write a query to find users whose name starts with “A”.**

```
SELECT * FROM users WHERE name LIKE 'A%';
```

---

## **28. Difference between DELETE, TRUNCATE, DROP.**

DELETE → remove rows
TRUNCATE → remove all rows (fast)
DROP → delete table

---

## **29. How to check if a record exists?**

```
SELECT EXISTS(SELECT 1 FROM users WHERE id=5);
```

---

## **30. Write a query to show top 5 highest salaries.**

```
SELECT * FROM employee 
ORDER BY salary DESC 
LIMIT 5;
```

---