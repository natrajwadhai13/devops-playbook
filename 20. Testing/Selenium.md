---
title: "• Selemium"
parent: "20. Testing"
nav_order: 4
has_children: true
---


![Image](https://browserstack.wpenginepowered.com/wp-content/uploads/2021/09/Selenium-WebDriver-for-Automation-Testing.webp?utm_source=chatgpt.com)

---

# 🔹 **SELENIUM – TECHNICAL QUESTIONS**

### **1. What are Selenium locators?**

ID, Name, ClassName, Xpath, CSS Selector, LinkText, PartialLinkText, TagName.

---

### **2. What is the difference between Xpath & CSS?**

* Xpath → Can traverse both forward/backward
* CSS → Faster, cannot traverse backward

---

### **3. What are Types of Waits?**

* Implicit Wait
* Explicit Wait (WebDriverWait)
* Fluent Wait

---

### **4. How do you handle dropdowns?**

```
Select select = new Select(element);
select.selectByVisibleText("Option");
```

---

### **5. How do you handle alerts in Selenium?**

```
Alert alert = driver.switchTo().alert();
alert.accept();
```

---

### **6. What is Page Object Model (POM)?**

Design pattern where each page is represented as a **class** → helps reduce duplication & increases maintainability.

---

### **7. What is TestNG? Why do we use it?**

Test framework used for:
✔ Annotations
✔ Assertions
✔ Parallel execution
✔ Test suites

---

# 🟦  30 Selenium Scenario-Based Questions (with Answers)**

## **1. How will you handle a dynamic element whose ID keeps changing?**

Use **contains() or starts-with()** in XPath.
Example:

```
//input[contains(@id,'username')]
```

---

## **2. Dropdown is not a `<select>` tag. How will you select an option?**

Use **click()** + **list of elements**.

```
driver.findElement(locator).click();
List<WebElement> options = driver.findElements(...);
for(WebElement opt : options){
   if(opt.getText().equals("Option1")) opt.click();
}
```

---

## **3. How do you handle auto-suggestion list (like Google search)?**

Capture list → loop → click required option.

---

## **4. Page is taking time to load. What will you use?**

Use **Explicit Wait** with *ExpectedConditions*.

---

## **5. How do you check if an element is displayed?**

```
element.isDisplayed();
```

---

## **6. How do you handle StaleElementReferenceException?**

Re-locate element or use **try–catch + refresh**.

---

## **7. How do you scroll to bottom of page?**

```
((JavascriptExecutor)driver).executeScript("window.scrollTo(0, document.body.scrollHeight)");
```

---

## **8. How do you click an element that is hidden?**

Use JavaScript click:

```
js.executeScript("arguments[0].click()", element);
```

---

## **9. How do you upload a file using Selenium?**

Send path directly:

```
input.sendKeys("C:\\file.txt");
```

---

## **10. How do you download a file without clicking?**

Set **browser preferences** in ChromeOptions/FirefoxProfile.

---

## **11. How do you handle multiple windows?**

Use **getWindowHandles()** and loop through.

---

## **12. How do you switch to an iframe?**

```
driver.switchTo().frame("frameName");
```

---

## **13. How do you handle drag and drop?**

Using **Actions class**:

```
new Actions(driver).dragAndDrop(src, dest).perform();
```

---

## **14. How do you take screenshot?**

```
((TakesScreenshot)driver).getScreenshotAs(OutputType.FILE);
```

---

## **15. How do you verify tooltip text?**

Use **getAttribute("title")**.

---

## **16. How do you handle calendar date selection?**

Locate month → navigate → click date.

---

## **17. How do you handle pagination (Next Page)?**

Use loop until **Next** button is disabled.

---

## **18. How do you handle Ajax elements?**

Explicit wait until element is visible.

---

## **19. What if click() is not working?**

Use JS executor click.

---

## **20. How to verify broken links?**

Use HttpURLConnection to check response code.

---

## **21. How do you validate text present on the page?**

```
driver.getPageSource().contains("text");
```

---

## **22. How to perform mouse hover?**

```
new Actions(driver).moveToElement(element).perform();
```

---

## **23. What if the element is inside shadow DOM?**

Use JavaScript to access shadow root.
(Selenium 4 supports shadow DOM)

---

## **24. How will you handle Captcha in automation?**

Cannot automate → handle **manually OR disable in test env**.

---

## **25. How do you run tests in parallel?**

Using **TestNG XML** parallel execution.

---

## **26. How do you implement Page Object Model (POM)?**

Create separate class for each page with elements + methods.

---

## **27. How do you generate reports?**

Using **Extent Reports** or **TestNG default report**.

---

## **28. How do you validate HTTP response code in Selenium?**

Use **HttpURLConnection**.

---

## **29. How do you retry failed test cases?**

Use **TestNG IRetryAnalyzer**.

---

## **30. How do you run Selenium tests in CI/CD?**

Integrate with **Jenkins / GitHub Actions** → execute Maven commands (e.g., `mvn test`).

---