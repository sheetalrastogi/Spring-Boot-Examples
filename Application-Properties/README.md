# Related Tutorials

* [How to Configure Properties with Spring
  Boot](https://howtodoinjava.com/spring-boot/properties-with-spring-boot/)

  Exception hierarchy in Selenium Java:
--------------------------------------

java.lang.Exception
  └── java.lang.RuntimeException
        └── org.openqa.selenium.WebDriverException  <== Parent Exception for most of Selenium Exceptions
              ├── NoSuchElementException
              ├── NoSuchFrameException
              ├── NoSuchWindowException
              ├── NoAlertPresentException
              ├── ElementNotInteractableException -
              ├── StaleElementReferenceException -
              ├── TimeoutException -
              ├── InvalidSelectorException  -
              ├── MoveTargetOutOfBoundsException  -
              ├── SessionNotFoundException  -
              ├── JavascriptException -
              └── UnsupportedCommandException -

Examples:
=================

ConnectionClosedException / UnreachableBrowserException
---------------------------------------------------------
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.remote.UnreachableBrowserException;
import org.openqa.selenium.remote.ConnectionClosedException;

public class SeleniumConnectionClosedExample {
    public static void main(String[] args) {
        WebDriver driver = null;
        try {
            driver = new ChromeDriver();
            driver.get("https://www.example.com");
            
            // Simulate browser closure (for demonstration, you might close the browser manually here)
            // driver.quit(); // or close the browser manually
            
            // Try to use driver after browser is closed
            driver.getTitle(); // This should throw ConnectionClosedException
        } catch (ConnectionClosedException e) {
            System.out.println("Connection to the browser was unexpectedly closed!");
            System.out.println(e.getMessage());
        } catch (UnreachableBrowserException e) {
            System.out.println("Browser is unreachable!");
            System.out.println(e.getMessage());
        } finally {
            if (driver != null) {
                try {
                    driver.quit();
                } catch (Exception ignored) {}
            }
        }
    }
}



ImeActivationFailedException and ImeNotAvailableException
-----------------------------------------------------------
These exceptions, you are not able to input desired multi-byte (Chinese/Japanese/etc) characters in your test webpage, and hence it is advised to fail the test with appropriate message.

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.ime.ImeNotAvailableException;
import org.openqa.selenium.ime.InputMethodManager;

public void activateIme(WebDriver driver, String imeEngine) {
    InputMethodManager imeManager = ((HasInputDevices) driver).getInputMethodManager();
    try {
        imeManager.activateEngine(imeEngine);
        System.out.println("IME Engine activated: " + imeEngine);
    } catch (ImeNotAvailableException e) {
        System.out.println("IME Engine not available: " + imeEngine);
        System.out.println("Exception message: " + e.getMessage());
        // Handle the error, maybe fall back to default or notify user
    }
}



InsecureCertificateException
------------------------------ 
When you access a site with an invalid or self-signed SSL certificate, browsers may block access or show a security warning. Selenium WebDriver can be configured to ignore insecure certificates.

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;

public class InsecureCertExample {
    public static void main(String[] args) {
        ChromeOptions options = new ChromeOptions();
        // This tells Chrome to accept insecure certificates
        options.setAcceptInsecureCerts(true);

        WebDriver driver = new ChromeDriver(options);

        driver.get("https://self-signed.badssl.com/");
        // Interact with the page as normal

        driver.quit();
    }
}


WebDriverException or SessionNotCreatedException 
---------------------------------------------------
that occur when connecting to a remote WebDriver 

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.remote.RemoteWebDriver;
import org.openqa.selenium.remote.DesiredCapabilities;
import org.openqa.selenium.remote.RemoteWebDriverServerException;
import java.net.URL;

public class RemoteDriverExample {
    public static void main(String[] args) {
        try {
            // Connect to Selenium Grid
            WebDriver driver = new RemoteWebDriver(
                new URL("http://localhost:4444/wd/hub"), 
                DesiredCapabilities.chrome()
            );
            driver.get("https://www.example.com");
            // Perform actions...
            driver.quit();
        } catch (RemoteWebDriverServerException e) {
            System.out.println("Remote WebDriver Server Exception occurred: " + e.getMessage());
            // Handle the error, maybe retry or log
        } catch (Exception e) {
            System.out.println("Other exception: " + e.getMessage());
        }
    }
}



NoSuchElementException
------------------------
NoSuchElementException in Selenium Java is thrown when Selenium cannot find an element using the provided locator (e.g. if the element does not exist on the page).

import org.openqa.selenium.By;
import org.openqa.selenium.NoSuchElementException;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;

public void findElementExample(WebDriver driver) {
    try {
        WebElement element = driver.findElement(By.id("nonexistent-id"));
        // Use the element as needed
        element.click();
    } catch (NoSuchElementException e) {
        System.out.println("Element not found: " + e.getMessage());
        // Optionally, log the error or perform alternative actions
    }
}

NoSuchFrameException
----------------------
NoSuchFrameException in Selenium Java occurs when you attempt to switch to a frame that doesn’t exist on the page

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.NoSuchFrameException;

public void switchToFrameSafely(WebDriver driver, String frameName) {
    try {
        driver.switchTo().frame(frameName);
        System.out.println("Successfully switched to frame: " + frameName);
        // Interact with elements in the frame as needed
    } catch (NoSuchFrameException e) {
        System.out.println("Frame not found: " + frameName);
        System.out.println("Exception message: " + e.getMessage());
        // Handle the error, maybe try a different frame or take a screenshot for debugging
    }
}

NoSuchWindowException
----------------------
NoSuchWindowException in Selenium Java occurs when you try to switch to or interact with a browser window that no longer exists or was never opened.

import org.openqa.selenium.NoSuchWindowException;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class WindowHandlingExample {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        driver.get("https://example.com");

        // Open a new window (example)
        ((ChromeDriver) driver).executeScript("window.open('https://google.com', '_blank');");
        String originalWindow = driver.getWindowHandle();

        // Get all window handles
        for (String windowHandle : driver.getWindowHandles()) {
            if (!windowHandle.equals(originalWindow)) {
                driver.switchTo().window(windowHandle);
                driver.close(); // Close the window
            }
        }

        // Try switching to the closed window
        try {
            driver.switchTo().window("nonexistentWindowHandle");
        } catch (NoSuchWindowException e) {
            System.out.println("Caught NoSuchWindowException: " + e.getMessage());
        }

        driver.quit();
    }
}

NoAlertPresentException
--------------------------
NoAlertPresentException in Selenium Java occurs when you try to switch to or interact with an alert, but there is no alert present on the page

import org.openqa.selenium.Alert;
import org.openqa.selenium.NoAlertPresentException;
import org.openqa.selenium.WebDriver;

public void handleAlert(WebDriver driver) {
    try {
        // Attempt to switch to an alert
        Alert alert = driver.switchTo().alert();
        alert.accept(); // or alert.dismiss(), alert.getText(), etc.
    } catch (NoAlertPresentException e) {
        System.out.println("No alert present on the page!");
        // Optionally, handle the absence of alert here
    }
}

ElementNotInteractableException
---------------------------------
ElementNotInteractableException in Selenium Java occurs when you try to interact with an element (e.g., click, send keys) that is present in the DOM but not currently interactable (e.g., hidden, disabled, or overlapped).

import org.openqa.selenium.By;
import org.openqa.selenium.ElementNotInteractableException;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;

public void sendTextWithInteractableCheck(WebDriver driver, By locator, String text) {
    try {
        WebElement inputBox = driver.findElement(locator);
        inputBox.sendKeys(text);
    } catch (ElementNotInteractableException e) {
        System.out.println("Element is not interactable: " + locator);
        System.out.println("Exception message: " + e.getMessage());
        // Optionally, you can log, report, or try a workaround here
    }
}


StaleElementReferenceException
---------------------------------
Suppose you locate an element, then the page updates (such as after navigation, AJAX update, or DOM change), causing that element to become stale when you try to interact with it.

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.StaleElementReferenceException;

public void demonstrateStaleElement(WebDriver driver) {
    // Locate the element
    WebElement element = driver.findElement(By.id("myButton"));

    // Simulate a DOM update (for example, by refreshing the page)
    driver.navigate().refresh();

    try {
        // Try to interact with the previously located element after refresh
        element.click(); // This will throw StaleElementReferenceException
    } catch (StaleElementReferenceException e) {
        System.out.println("Caught StaleElementReferenceException!");
        // Handle exception (e.g., re-locate the element)
        element = driver.findElement(By.id("myButton"));
        element.click();
    }
}


TimeoutException
-------------------
TimeoutException in Selenium Java occurs when a command waits for a certain condition to be met but the condition isn’t satisfied within the specified time.

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.TimeoutException;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;

public void waitForElementWithTimeout(WebDriver driver, By locator) {
    WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(5)); // Wait up to 5 seconds
    try {
        WebElement element = wait.until(ExpectedConditions.visibilityOfElementLocated(locator));
        // Use the element as needed
        element.click();
    } catch (TimeoutException e) {
        System.out.println("Element was not visible within timeout!");
        System.out.println("Exception message: " + e.getMessage());
        // Additional error handling logic here
    }
}


InvalidSelectorException
-------------------------
	Syntax errors in selectors (e.g. By.cssSelector("div["))

	import org.openqa.selenium.By;
	import org.openqa.selenium.InvalidSelectorException;
	import org.openqa.selenium.WebDriver;
	import org.openqa.selenium.WebElement;

	public void findElementWithSelectorHandling(WebDriver driver, String selector) {
	    try {
	        // Example: trying to use an invalid CSS selector
	        WebElement element = driver.findElement(By.cssSelector(selector));
	        // Use the element as needed
	        element.click();
	    } catch (InvalidSelectorException e) {
	        System.out.println("Invalid selector: " + selector);
	        System.out.println("Exception message: " + e.getMessage());
	        // Optionally, log or handle the error, or try a different selector
	    }
	}

MoveTargetOutOfBoundsException
-------------------------------
	try to move the mouse to an element that is not visible or is outside the visible area of the page

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.interactions.Actions;
import org.openqa.selenium.interactions.MoveTargetOutOfBoundsException;

public void moveToElementExample(WebDriver driver) {
    try {
        // Suppose the element is outside the visible viewport
        WebElement element = driver.findElement(By.id("outOfBoundsElement"));
        
        Actions actions = new Actions(driver);
        actions.moveToElement(element).perform();
    } catch (MoveTargetOutOfBoundsException e) {
        System.out.println("Move target is out of bounds!");
        System.out.println("Exception message: " + e.getMessage());
        // Optionally: scroll to the element or handle the error
    }
}


SessionNotFoundException
-------------------------
SessionNotFoundException in Selenium Java occurs when you try to perform an operation on a WebDriver session that has already been closed or does not exist.

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.SessionNotFoundException;

public class SessionNotFoundExample {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.example.com");

        // End the session
        driver.quit();

        try {
            // This will throw SessionNotFoundException
            driver.get("https://www.google.com");
        } catch (SessionNotFoundException e) {
            System.out.println("Session not found! The WebDriver session is already closed.");
            System.out.println("Exception message: " + e.getMessage());
        }
    }
}


JavascriptException
---------------------

JavascriptException in Selenium Java occurs when there is an error executing JavaScript via Selenium’s JavascriptExecutor.

import org.openqa.selenium.JavascriptExecutor;
import org.openqa.selenium.JavascriptException;
import org.openqa.selenium.WebDriver;

public void executeJavaScriptWithHandling(WebDriver driver) {
    JavascriptExecutor js = (JavascriptExecutor) driver;
    try {
        // This will fail if 'nonExistentFunction' is not defined on the page
        js.executeScript("return nonExistentFunction();");
    } catch (JavascriptException e) {
        System.out.println("JavaScript execution failed!");
        System.out.println("Exception message: " + e.getMessage());
        // Handle the error, log it, or take corrective action
    }
}


UnsupportedCommandException
-----------------------------
UnsupportedCommandException in Selenium Java occurs when you try to execute a command that the WebDriver does not support.

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.OutputType;
import org.openqa.selenium.TakesScreenshot;
import org.openqa.selenium.remote.RemoteWebDriver;
import org.openqa.selenium.UnsupportedCommandException;

public void takeScreenshot(WebDriver driver) {
    try {
        // This may throw UnsupportedCommandException if the driver does not support screenshots
        byte[] screenshot = ((TakesScreenshot) driver).getScreenshotAs(OutputType.BYTES);
        // Use the screenshot as needed
    } catch (UnsupportedCommandException e) {
        System.out.println("The command is not supported by the current driver: " + e.getMessage());
        // Handle accordingly, e.g., log, fallback, etc.
    }
}








































Exception hierarchy in Java:
-------------------------------
java.lang.Object
   |
   +-- java.lang.Throwable
           |
           +-- java.lang.Error
           |      |
           |      +-- OutOfMemoryError
           |      +-- StackOverflowError
           |      +-- AssertionError
           |      +-- ... (other Errors)
           |
           +-- java.lang.Exception
                   |
                   +-- java.lang.RuntimeException (unchecked)
                   |      |
                   |      +-- NullPointerException
                   |      +-- IndexOutOfBoundsException
                   |      +-- ArithmeticException
                   |      +-- IllegalArgumentException
                   |      +-- ... (other RuntimeExceptions)
                   |
                   +-- IOException (checked)
                   +-- SQLException (checked)
                   +-- ... (other checked exceptions)

