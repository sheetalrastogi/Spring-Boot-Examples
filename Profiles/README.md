# Related Tutorials

* [Spring Boot Profiles](https://howtodoinjava.com/spring-boot/spring-profiles/)

* Aug13

* Selenium Code examples:
------------------------

#1. Open a new Tab in Selenium with JavascriptExecutor:

	JavascriptExecutor js = (JavascriptExecutor) driver;
	// Open a new tab
	js.executeScript("window.open();");
	// Switch to the new tab
	ArrayList<String> tabs = new ArrayList<>(driver.getWindowHandles());
	driver.switchTo().window(tabs.get(1)); // Switch to the newly opened tab

	// Open a new window
	((JavascriptExecutor) driver).executeScript("window.open('', '_blank');");


#2. Identify all open windows

// Open a new window
((JavascriptExecutor) driver).executeScript("window.open('', '_blank');");

// Get all window handles
Set<String> windowHandles = driver.getWindowHandles();

// To work with them, you can convert to a list (for index access)
List<String> windows = new ArrayList<>(windowHandles);

// Print all window handles
for (String handle : windows) {
    System.out.println("Window handle: " + handle);
}


#3. switch to open windows / tabs

// Switch to the first window
driver.switchTo().window(windows.get(0));

// Switch to the second window (the newly opened one)
driver.switchTo().window(windows.get(1));



#4. Scroll down a webpage

JavascriptExecutor js = (JavascriptExecutor) driver;
// Scroll down by 400 pixels
js.executeScript("window.scrollBy(0,400);");
// Scroll up by 200 pixels
js.executeScript("window.scrollBy(0,-200);");


#5. Click an element using JavascriptExecutor

WebElement element = driver.findElement(By.id("myButton"));
JavascriptExecutor js = (JavascriptExecutor) driver;
js.executeScript("arguments[0].click();", element);


#6. Pagetitle using JSE

JavascriptExecutor js = (JavascriptExecutor) driver;
String title = (String) js.executeScript("return document.title;");
System.out.println("Page Title: " + title);

#7. Highlight an element

WebElement element = driver.findElement(By.id("myField"));
JavascriptExecutor js = (JavascriptExecutor) driver;
js.executeScript("arguments[0].style.border='3px solid red'", element);

#8. Open a new Tab

JavascriptExecutor js = (JavascriptExecutor) driver;
js.executeScript("window.open('https://www.google.com', '_blank');");

#9. Set the value of input field

WebElement input = driver.findElement(By.id("username"));
JavascriptExecutor js = (JavascriptExecutor) driver;
js.executeScript("arguments[0].value='testuser';", input);

#10. Get innerText of an element

WebElement element = driver.findElement(By.id("info"));
JavascriptExecutor js = (JavascriptExecutor) driver;
String innerText = (String) js.executeScript("return arguments[0].innerText;", element);
System.out.println(innerText);

#11. Refresh the browser page

JavascriptExecutor js = (JavascriptExecutor) driver;
js.executeScript("history.go(0);");


#12. Select a dropdown element using JavascriptExecutor

WebElement dropdown = driver.findElement(By.id("myDropdown")); // Replace with your dropdown's locator

JavascriptExecutor js = (JavascriptExecutor) driver;
js.executeScript("arguments[0].selectedIndex = 2;", dropdown); // Selects the third option (index starts at 0)

OR

WebElement dropdown = driver.findElement(By.id("myDropdown")); // Replace with your dropdown's locator

JavascriptExecutor js = (JavascriptExecutor) driver;
js.executeScript("arguments[0].value = 'desiredValue';", dropdown); // Replace 'desiredValue' with your option's value

OR

WebElement dropdown = driver.findElement(By.id("myDropdown"));
String optionText = "Option 3"; // Replace with your desired option text

JavascriptExecutor js = (JavascriptExecutor) driver;
js.executeScript(
    "for(var i=0; i<arguments[0].options.length; i++) {" +
    "if(arguments[0].options[i].text === arguments[1]) {" +
    "arguments[0].selectedIndex = i; break; }" +
    "}", dropdown, optionText);


Note:

After selecting via JavaScript, if your page relies on the change event, you may need to trigger it:

js.executeScript("arguments[0].dispatchEvent(new Event('change'));", dropdown);


#13. Asynchronous Script with setTimeout

JavascriptExecutor js = (JavascriptExecutor) driver;
js.executeAsyncScript(
    "var callback = arguments[arguments.length - 1];" +
    "window.setTimeout(function(){ callback('Async script completed!'); }, 2000);"
);

Key Points:

The last argument in arguments is a callback function that you must call to signal that your async script is done.

Selenium will wait until this callback is called or until the script timeout is reached.



#14. Returning a Value from Asynchronous Script

JavascriptExecutor js = (JavascriptExecutor) driver;
Object result = js.executeAsyncScript(
    "var callback = arguments[arguments.length - 1];" +
    "window.setTimeout(function(){ callback('Hello from async!'); }, 1000);"
);
System.out.println(result); // Output: Hello from async!


#15. Waiting for an AJAX Request

JavascriptExecutor js = (JavascriptExecutor) driver;
Object response = js.executeAsyncScript(
    "var callback = arguments[arguments.length - 1];" +
    "var xhr = new XMLHttpRequest();" +
    "xhr.open('GET', 'https://jsonplaceholder.typicode.com/todos/1', true);" +
    "xhr.onreadystatechange = function() {" +
    "  if (xhr.readyState == 4) {" +
    "    callback(xhr.responseText);" +
    "  }" +
    "};" +
    "xhr.send();"
);
System.out.println(response); // Output: JSON response from the API


#16.  wait for the page to load completely in Selenium Java

        WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(15));
        wait.until((ExpectedCondition<Boolean>) wd ->
            ((JavascriptExecutor) wd).executeScript("return document.readyState").equals("complete")
        );

        // Now the page is fully loaded
        // ... your further actions


#17.  Better solution for point 16 above:

To wait for the page load including all Ajax calls to complete in Selenium Java, you need to check both:

document.readyState is "complete"
All jQuery AJAX calls have finished (jQuery.active == 0)

WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(15));
wait.until((ExpectedCondition<Boolean>) wd -> {
    JavascriptExecutor js = (JavascriptExecutor) wd;
    // Check if jQuery is present
    Boolean jQueryDefined = (Boolean) js.executeScript("return typeof jQuery != 'undefined';");
    // Check document.readyState
    boolean pageLoaded = js.executeScript("return document.readyState").equals("complete");
    // Check if there are any active jQuery AJAX calls
    boolean ajaxComplete = true;
    if (jQueryDefined) {
        ajaxComplete = (Long) js.executeScript("return jQuery.active") == 0;
    }
    return pageLoaded && ajaxComplete;
});



#18. Check if JavaScript is Enabled

import org.openqa.selenium.JavascriptExecutor;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class CheckJavaScriptEnabled {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.example.com");

        boolean jsEnabled = false;
        try {
            JavascriptExecutor js = (JavascriptExecutor) driver;
            js.executeScript("return 1 + 1;");
            jsEnabled = true;
        } catch (Exception e) {
            jsEnabled = false;
        }

        if (jsEnabled) {
            System.out.println("JavaScript is enabled in the browser.");
        } else {
            System.out.println("JavaScript is NOT enabled in the browser.");
        }

        driver.quit();
    }
}


#19.  Drag an element in Selenium

       WebDriver driver = new ChromeDriver();
        driver.get("https://www.example.com");

        // Find a scrollable element (or use body for whole page)
        WebElement scrollable = driver.findElement(By.tagName("body"));

        Actions actions = new Actions(driver);
        actions.clickAndHold(scrollable)
               .moveByOffset(0, -200)  // Drag up by 200 pixels
               .release()
               .perform();


#20. drag and drop between two elements

WebElement source = driver.findElement(By.id("source"));
WebElement target = driver.findElement(By.id("target"));

Actions actions = new Actions(driver);
actions.dragAndDrop(source, target).perform();


#21. Ping the server Before starting your Selenium scripts

import java.net.HttpURLConnection;
import java.net.URL;

public boolean isNodeResponsive(String nodeUrl) {
    try {
        URL url = new URL(nodeUrl + "/status");
        HttpURLConnection conn = (HttpURLConnection) url.openConnection();
        conn.setConnectTimeout(3000); // 3 seconds
        conn.setReadTimeout(3000);
        conn.setRequestMethod("GET");
        int responseCode = conn.getResponseCode();
        return responseCode == 200;
    } catch (Exception e) {
        return false;
    }
}


#22.  Handling un-responsive Webdriver instantiation on Selenium Grid

import org.openqa.selenium.remote.RemoteWebDriver;
import org.openqa.selenium.remote.DesiredCapabilities;
import java.net.URL;
import java.util.concurrent.TimeUnit;

public class SeleniumNodeCheck {
    public static void main(String[] args) {
        try {
            // Set timeout for connection
            URL nodeUrl = new URL("http://your-grid-node:4444/wd/hub");
            DesiredCapabilities capabilities = new DesiredCapabilities();
            
            // Optional: Set timeouts for the driver (after creation)
            RemoteWebDriver driver = new RemoteWebDriver(nodeUrl, capabilities);
            driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);

            System.out.println("Node responded and RemoteWebDriver instantiated!");
            driver.quit();
        } catch (Exception e) {
            System.out.println("Node is hanged or unreachable: " + e.getMessage());
            // Optionally, log/handle or retry
        }
    }
}


#23. Explicit Timeouts

driver.manage().timeouts().pageLoadTimeout(Duration.ofSeconds(20));
driver.manage().timeouts().scriptTimeout(Duration.ofSeconds(10));
driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(5));


#24. Check Browser Health during Selenium script execution

try {
    ((JavascriptExecutor) driver).executeScript("return 1+1;");
} catch (WebDriverException e) {
    System.out.println("Browser is not responding.");
    // Optionally, restart the session
}

Example utility method:

import org.openqa.selenium.JavascriptExecutor;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebDriverException;

public class WebDriverUtils {
    /**
     * Tests if the website/browser is responsive during Selenium script execution.
     * @param driver the WebDriver instance
     * @return true if responsive, false if not responsive
     */
    public static boolean isBrowserResponsive(WebDriver driver) {
        try {
            ((JavascriptExecutor) driver).executeScript("return 1+1;");
            return true;
        } catch (WebDriverException e) {
            System.out.println("Browser is not responding: " + e.getMessage());
            // Optionally, add logic to restart the session here
            return false;
        }
    }
}

Example usage:

if (!WebDriverUtils.isBrowserResponsive(driver)) {
    // Handle recovery or restart browser session
}


#25.  ViewportDimensionsDemo

import org.openqa.selenium.Dimension;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class ViewportDimensionsDemo {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        // Get the screen dimensions using Java's Toolkit
        java.awt.Dimension screenSize = java.awt.Toolkit.getDefaultToolkit().getScreenSize();
        int screenWidth = screenSize.width;
        int screenHeight = screenSize.height;

        // Calculate 50% of screen width, keep height same or set a value
        int browserWidth = screenWidth / 2;
        int browserHeight = screenHeight; // Or set a custom value if preferred

        // Set the browser window size
        driver.manage().window().setSize(new Dimension(browserWidth, browserHeight));

        // Print browser dimensions for verification
        Dimension browserDimension = driver.manage().window().getSize();
        System.out.println("Browser viewport size: " +
            browserDimension.getWidth() + "x" +
            browserDimension.getHeight()
        );

        // Your further Selenium actions go here
        driver.get("https://www.example.com");

        // Clean up
        driver.quit();
    }
}


#26. "single screenshot of multiple pages" in Selenium Java by following these steps:

Step 1:  Take Individual Screenshots

import org.openqa.selenium.OutputType;
import org.openqa.selenium.TakesScreenshot;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

import java.io.File;
import org.apache.commons.io.FileUtils;

public class MultiPageScreenshot {
    public static void main(String[] args) throws Exception {
        WebDriver driver = new ChromeDriver();

        // First page
        driver.get("https://www.example.com/page1");
        File screenshot1 = ((TakesScreenshot) driver).getScreenshotAs(OutputType.FILE);
        FileUtils.copyFile(screenshot1, new File("page1.png"));

        // Second page
        driver.get("https://www.example.com/page2");
        File screenshot2 = ((TakesScreenshot) driver).getScreenshotAs(OutputType.FILE);
        FileUtils.copyFile(screenshot2, new File("page2.png"));

        driver.quit();
    }
}

Step 2:  Combine Screenshots into One Image (Java AWT/BufferedImage)

import javax.imageio.ImageIO;
import java.awt.image.BufferedImage;
import java.awt.Graphics;
import java.io.File;

public class CombineScreenshots {
    public static void main(String[] args) throws Exception {
        BufferedImage img1 = ImageIO.read(new File("page1.png"));
        BufferedImage img2 = ImageIO.read(new File("page2.png"));

        int width = Math.max(img1.getWidth(), img2.getWidth());
        int height = img1.getHeight() + img2.getHeight();

        BufferedImage combined = new BufferedImage(width, height, BufferedImage.TYPE_INT_ARGB);
        Graphics g = combined.getGraphics();
        g.drawImage(img1, 0, 0, null);
        g.drawImage(img2, 0, img1.getHeight(), null);

        ImageIO.write(combined, "PNG", new File("combined.png"));
    }
}


#27.  browser’s current zoom factor

Method 1:  Using devicePixelRatio (Most browsers)

import org.openqa.selenium.JavascriptExecutor;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class CheckZoomFactor {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.example.com");

        JavascriptExecutor js = (JavascriptExecutor) driver;
        // devicePixelRatio returns zoom level (1 = 100%, 1.25 = 125%, 0.75 = 75%, etc.)
        Object zoomValue = js.executeScript("return window.devicePixelRatio;");
        System.out.println("Current browser zoom factor: " + zoomValue);

        driver.quit();
    }
}

o/p:   
100% zoom usually gives 1.0
125% zoom gives 1.25
75% zoom gives 0.75



Method 2:  Alternative approach

Comparing Screen and Viewport Dimensions (Alternative)

Object zoom = js.executeScript(
    "return (window.outerWidth / window.innerWidth);"
);
System.out.println("Zoom ratio (outerWidth/innerWidth): " + zoom);


At 100% zoom, this ratio is typically close to 1.0


#28. Evaluate XPath of an element using JavascriptExecutor

import org.openqa.selenium.JavascriptExecutor;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;

// ... after initializing driver

String xpath = "//html[1]/body[1]/div[1]";
String script = "return (new XPathEvaluator())"
              + ".createExpression(arguments[0])"
              + ".evaluate(document, XPathResult.FIRST_ORDERED_NODE_TYPE).singleNodeValue;";
WebElement element = (WebElement) ((JavascriptExecutor) driver).executeScript(script, xpath);

System.out.println(element);


Working example of this

import org.openqa.selenium.JavascriptExecutor;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;

public class WebElementUtils {
    /**
     * Checks if an element exists on the webpage using XPath and JavaScript's XPathEvaluator.
     * @param driver the Selenium WebDriver instance
     * @param xpath the XPath expression to locate the element
     * @return true if the element exists, false otherwise
     */
    public static boolean elementExistsByXPath(WebDriver driver, String xpath) {
        String script = "return (new XPathEvaluator())"
                      + ".createExpression(arguments[0])"
                      + ".evaluate(document, XPathResult.FIRST_ORDERED_NODE_TYPE).singleNodeValue;";
        Object result = ((JavascriptExecutor) driver).executeScript(script, xpath);
        return result != null;
    }
}


Client:

String xpath = "//html[1]/body[1]/div[1]";
boolean exists = WebElementUtils.elementExistsByXPath(driver, xpath);
System.out.println("Element exists? " + exists);




#29.  Chrome Dev Protocol

Selenium WebDriver (from version 4.x onwards) allows you to interact with Chrome DevTools Protocol (CDP) for advanced browser automation features such as network interception, performance monitoring, emulation, etc.


==>  example of how to use Chrome DevTools in Selenium Java to capture network events

import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.devtools.DevTools;
import org.openqa.selenium.devtools.v124.network.Network;
import org.openqa.selenium.devtools.v124.network.model.Request;

public class ChromeDevToolsExample {
    public static void main(String[] args) {
        // Initialize ChromeDriver with ChromeOptions
        ChromeOptions options = new ChromeOptions();
        ChromeDriver driver = new ChromeDriver(options);

        // Create DevTools session
        DevTools devTools = driver.getDevTools();
        devTools.createSession();

        // Enable Network events
        devTools.send(Network.enable(Optional.empty(), Optional.empty(), Optional.empty()));

        // Add a listener for network requests
        devTools.addListener(Network.requestWillBeSent(), request -> {
            Request req = request.getRequest();
            System.out.println("Request URL: " + req.getUrl());
        });

        // Open a page
        driver.get("https://www.example.com");

        // Wait a bit, then quit
        try { Thread.sleep(5000); } catch (InterruptedException ignored) {}
        driver.quit();
    }
}

What this does
Launches Chrome with Selenium.
Creates a DevTools session.
Listens for network requests and prints their URLs to the console.

Other Use Cases with Chrome DevTools in Selenium
	Blocking/unblocking network requests.
	Emulating devices or network speed.
	Capturing console logs.
	Manipulating cookies and storage.
	Getting performance metrics.


#30.  Selenium Java example for blocking and unblocking network requests using the Chrome DevTools Protocol (CDP):

import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.devtools.DevTools;
import org.openqa.selenium.devtools.v124.network.Network;

import java.util.Optional;
import java.util.Arrays;

public class BlockNetworkRequestsExample {
    public static void main(String[] args) {
        // Start ChromeDriver
        ChromeOptions options = new ChromeOptions();
        ChromeDriver driver = new ChromeDriver(options);

        // Create DevTools session
        DevTools devTools = driver.getDevTools();
        devTools.createSession();

        // Enable the Network domain
        devTools.send(Network.enable(Optional.empty(), Optional.empty(), Optional.empty()));

        // Block requests for images and CSS files
        devTools.send(Network.setBlockedURLs(Arrays.asList("*.png", "*.jpg", "*.jpeg", "*.gif", "*.css")));

        // Open a page (images and CSS won't load)
        driver.get("https://www.example.com");

        // Wait for observation
        try { Thread.sleep(5000); } catch (InterruptedException ignored) {}

        // Unblock all requests (remove blocking)
        devTools.send(Network.setBlockedURLs(Arrays.asList()));

        // Reload page (now images and CSS will load)
        driver.navigate().refresh();

        try { Thread.sleep(5000); } catch (InterruptedException ignored) {}

        driver.quit();
    }
}

Explanation
The example blocks image and CSS requests using Network.setBlockedURLs.
After loading the page, it unblocks by sending an empty list, then refreshes the page.
You can block/unblock any pattern (e.g., "*analytics*", "*ads*", etc.).


#31. Block specific domain using CDP

import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.devtools.DevTools;
import org.openqa.selenium.devtools.v124.network.Network;

import java.util.Optional;
import java.util.Arrays;

public class BlockSpecificDomainsExample {
    public static void main(String[] args) {
        // Initialize ChromeDriver
        ChromeOptions options = new ChromeOptions();
        ChromeDriver driver = new ChromeDriver(options);

        // Create DevTools session
        DevTools devTools = driver.getDevTools();
        devTools.createSession();

        // Enable Network domain
        devTools.send(Network.enable(Optional.empty(), Optional.empty(), Optional.empty()));

        // Block requests to specific domains (example: google-analytics and doubleclick)
        devTools.send(Network.setBlockedURLs(Arrays.asList(
            "*://www.google-analytics.com/*",
            "*://www.googletagmanager.com/*",
            "*://www.doubleclick.net/*"
        )));

        // Open a page (requests to these domains will be blocked)
        driver.get("https://www.example.com");

        // Wait to observe the blocking
        try { Thread.sleep(5000); } catch (InterruptedException ignored) {}

        driver.quit();
    }
}


#32. capturing network events and calculating throughput in Selenium Java using CDP:

import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.devtools.DevTools;
import org.openqa.selenium.devtools.v124.network.Network;
import org.openqa.selenium.devtools.v124.network.model.LoadingFinished;
import org.openqa.selenium.devtools.v124.network.model.LoadingFailed;

import java.util.Optional;
import java.util.concurrent.atomic.AtomicLong;

public class CaptureNetworkThroughputExample {
    public static void main(String[] args) {
        // Setup ChromeDriver and DevTools
        ChromeOptions options = new ChromeOptions();
        ChromeDriver driver = new ChromeDriver(options);
        DevTools devTools = driver.getDevTools();
        devTools.createSession();

        // Enable network tracking
        devTools.send(Network.enable(Optional.empty(), Optional.empty(), Optional.empty()));

        // Throughput tracking variables
        AtomicLong totalBytes = new AtomicLong(0);
        AtomicLong failedBytes = new AtomicLong(0);

        // Listen for finished network requests
        devTools.addListener(Network.loadingFinished(), (LoadingFinished event) -> {
            totalBytes.addAndGet(event.getEncodedDataLength());
            System.out.println("Resource loaded: " + event.getRequestId() + ", bytes: " + event.getEncodedDataLength());
        });

        // Listen for failed network requests (optional)
        devTools.addListener(Network.loadingFailed(), (LoadingFailed event) -> {
            System.out.println("Resource failed to load: " + event.getRequestId());
        });

        // Start timing
        long start = System.currentTimeMillis();

        // Navigate to the target page
        driver.get("https://www.example.com");

        // Wait for a few seconds to allow network events to finish
        try { Thread.sleep(5000); } catch (InterruptedException ignored) {}

        // End timing
        long end = System.currentTimeMillis();
        long elapsedMillis = end - start;
        double seconds = elapsedMillis / 1000.0;

        // Output throughput results
        System.out.println("Total bytes loaded: " + totalBytes.get());
        System.out.println(String.format("Throughput: %.2f KB/s", (totalBytes.get() / 1024.0) / seconds));

        driver.quit();
    }
}



#33.  CaptureNetworkResourceTypeThroughputExample

import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.devtools.DevTools;
import org.openqa.selenium.devtools.v124.network.Network;
import org.openqa.selenium.devtools.v124.network.model.LoadingFinished;
import org.openqa.selenium.devtools.v124.network.model.RequestWillBeSent;
import org.openqa.selenium.devtools.v124.network.model.ResourceType;

import java.util.Optional;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.atomic.AtomicLong;

public class CaptureNetworkResourceTypeThroughputExample {
    public static void main(String[] args) {
        ChromeOptions options = new ChromeOptions();
        ChromeDriver driver = new ChromeDriver(options);
        DevTools devTools = driver.getDevTools();
        devTools.createSession();

        devTools.send(Network.enable(Optional.empty(), Optional.empty(), Optional.empty()));

        // Track requestId -> ResourceType mapping
        ConcurrentHashMap<String, ResourceType> requestResourceTypes = new ConcurrentHashMap<>();
        // Throughput tracking for specific resource types
        AtomicLong imageBytes = new AtomicLong(0);
        AtomicLong scriptBytes = new AtomicLong(0);
        AtomicLong stylesheetBytes = new AtomicLong(0);

        // Listen for requestWillBeSent to map requestId to ResourceType
        devTools.addListener(Network.requestWillBeSent(), (RequestWillBeSent event) -> {
            requestResourceTypes.put(event.getRequestId().toString(), event.getType());
        });

        // Listen for finished network requests
        devTools.addListener(Network.loadingFinished(), (LoadingFinished event) -> {
            String reqId = event.getRequestId().toString();
            ResourceType type = requestResourceTypes.getOrDefault(reqId, ResourceType.OTHER);

            long bytes = event.getEncodedDataLength();

            switch (type) {
                case IMAGE:
                    imageBytes.addAndGet(bytes);
                    System.out.println("Image loaded: " + reqId + ", bytes: " + bytes);
                    break;
                case SCRIPT:
                    scriptBytes.addAndGet(bytes);
                    System.out.println("Script loaded: " + reqId + ", bytes: " + bytes);
                    break;
                case STYLESHEET:
                    stylesheetBytes.addAndGet(bytes);
                    System.out.println("Stylesheet loaded: " + reqId + ", bytes: " + bytes);
                    break;
                default:
                    // Skip other types or collect if needed
                    break;
            }
        });

        // Start timing
        long start = System.currentTimeMillis();

        driver.get("https://www.example.com");

        // Wait to allow network events to finish
        try { Thread.sleep(5000); } catch (InterruptedException ignored) {}

        // End timing
        long end = System.currentTimeMillis();
        double seconds = (end - start) / 1000.0;

        // Output throughput results per resource type
        System.out.println("Image bytes loaded: " + imageBytes.get());
        System.out.println(String.format("Image throughput: %.2f KB/s", (imageBytes.get() / 1024.0) / seconds));
        System.out.println("Script bytes loaded: " + scriptBytes.get());
        System.out.println(String.format("Script throughput: %.2f KB/s", (scriptBytes.get() / 1024.0) / seconds));
        System.out.println("Stylesheet bytes loaded: " + stylesheetBytes.get());
        System.out.println(String.format("Stylesheet throughput: %.2f KB/s", (stylesheetBytes.get() / 1024.0) / seconds));

        driver.quit();
    }
}

What’s improved:

Tracks images, scripts, and stylesheets individually (by resource type).
Outputs bytes loaded and throughput for each type.
Uses Network.requestWillBeSent() to map each requestId to its resource type.
You can extend this approach for other resource types (e.g., XHR, FONT, etc.) by adding more cases to the switch statement.


#34. Emulate Mobile device with CDP

import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.devtools.DevTools;
import org.openqa.selenium.devtools.v124.emulation.Emulation;

import java.util.Optional;

public class EmulateMobileDeviceExample {
    public static void main(String[] args) {
        // Setup ChromeDriver
        ChromeOptions options = new ChromeOptions();
        ChromeDriver driver = new ChromeDriver(options);
        DevTools devTools = driver.getDevTools();
        devTools.createSession();

        // Emulate a mobile device (e.g., iPhone X)
        int width = 375;
        int height = 812;
        double deviceScaleFactor = 3.0;
        boolean isMobile = true;
        String userAgent = "Mozilla/5.0 (iPhone; CPU iPhone OS 13_0 like Mac OS X) " +
                "AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.0 Mobile/15E148 Safari/604.1";

        devTools.send(Emulation.setDeviceMetricsOverride(
                width,
                height,
                deviceScaleFactor,
                isMobile,
                Optional.empty(),
                Optional.empty(),
                Optional.empty(),
                Optional.empty(),
                Optional.empty(),
                Optional.empty(),
                Optional.empty(),
                Optional.empty(),
                Optional.empty()
        ));

        devTools.send(Emulation.setUserAgentOverride(
                userAgent,
                Optional.empty(),
                Optional.empty(),
                Optional.empty()
        ));

        driver.get("https://www.example.com");

        // Wait so you can observe the device emulation
        try { Thread.sleep(7000); } catch (InterruptedException ignored) {}

        driver.quit();
    }
}

How this works:

The code sets device metrics (width, height, scale factor) and marks the device as mobile.
It changes the user agent string to match a real mobile device.
This makes Chrome render the page as if viewed on a mobile device (e.g., iPhone X).

	
#35. Capturing console logs with CDP

import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.devtools.DevTools;
import org.openqa.selenium.devtools.v124.log.Log;
import org.openqa.selenium.devtools.v124.log.model.Entry;

import java.util.Optional;

public class CaptureConsoleLogsExample {
    public static void main(String[] args) {
        // Setup ChromeDriver
        ChromeOptions options = new ChromeOptions();
        ChromeDriver driver = new ChromeDriver(options);
        DevTools devTools = driver.getDevTools();
        devTools.createSession();

        // Enable console log domain
        devTools.send(Log.enable());

        // Listen for console log entries
        devTools.addListener(Log.entryAdded(), (Entry entry) -> {
            System.out.printf(
                "Log [%s]: %s%n", 
                entry.getLevel(), 
                entry.getText()
            );
        });

        // Visit a page with console logs
        driver.get("https://www.example.com");

        // Wait so logs can be captured
        try { Thread.sleep(5000); } catch (InterruptedException ignored) {}

        driver.quit();
    }
}

How this works:

Enables Chrome DevTools log domain with Log.enable().
Listens for all types of console logs (console.log, console.error, etc.).
Prints log level and message to the console.
Works for any website; for best results, use a page with console output.

#36. Emulating network speed with CDP

import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.devtools.DevTools;
import org.openqa.selenium.devtools.v124.network.Network;

import java.util.Optional;

public class EmulateNetworkSpeedExample {
    public static void main(String[] args) {
        // Setup ChromeDriver
        ChromeOptions options = new ChromeOptions();
        ChromeDriver driver = new ChromeDriver(options);
        DevTools devTools = driver.getDevTools();
        devTools.createSession();

        // Enable the network domain
        devTools.send(Network.enable(Optional.empty(), Optional.empty(), Optional.empty()));

        // Emulate network conditions (e.g., slow 3G)
        boolean offline = false;
        int latency = 400; // ms
        int downloadThroughput = 40000; // bytes/sec (~320 kbps)
        int uploadThroughput = 20000;   // bytes/sec (~160 kbps)
        Optional<Double> connectionType = Optional.empty(); // can set to Optional.of(Network.ConnectionType.CELLULAR3G)

        devTools.send(
            Network.emulateNetworkConditions(
                offline,
                latency,
                downloadThroughput,
                uploadThroughput,
                connectionType
            )
        );

        driver.get("https://www.example.com");

        // Wait to observe the effect
        try { Thread.sleep(7000); } catch (InterruptedException ignored) {}

        driver.quit();
    }
}


How this works:

Uses Network.emulateNetworkConditions to set latency, download/upload speeds, and offline mode.
You can adjust latency, downloadThroughput, and uploadThroughput for different network profiles (e.g., fast 3G, slow 4G, offline).
Optionally, set connectionType for more realistic emulation (Network.ConnectionType.CELLULAR3G, WIFI, etc.).


#37.  import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;

import java.util.HashMap;
import java.util.Map;

public class MobileEmulationTest {
    public static void main(String[] args) {
        // Set the path to chromedriver if needed
        // System.setProperty("webdriver.chrome.driver", "/path/to/chromedriver");

        ChromeOptions options = new ChromeOptions();
        Map<String, String> mobileEmulation = new HashMap<>();
        mobileEmulation.put("deviceName", "Pixel 2"); // or "iPhone X", "Nexus 5", etc.
        options.setExperimentalOption("mobileEmulation", mobileEmulation);

        WebDriver driver = new ChromeDriver(options);
        driver.get("https://www.example.com");

        // Your test code here

        driver.quit();
    }
}

==================



import org.openqa.selenium.WebDriver;
import org.openqa.selenium.remote.RemoteWebDriver;
import org.openqa.selenium.chrome.ChromeOptions;

import java.net.URL;

public class SauceLabsChromeExample {
    public static void main(String[] args) throws Exception {
        // Sauce Labs credentials
        String sauceUserName = "YOUR_SAUCE_USERNAME";
        String sauceAccessKey = "YOUR_SAUCE_ACCESS_KEY";

        // Sauce Labs URL
        String sauceURL = "https://" + sauceUserName + ":" + sauceAccessKey + "@ondemand.saucelabs.com/wd/hub";

        // Set Chrome Options
        ChromeOptions options = new ChromeOptions();
        options.setCapability("platformName", "Windows 10");
        options.setCapability("browserVersion", "latest");

        // Sauce Labs capabilities (optional, for reporting)
        options.setCapability("sauce:options", new java.util.HashMap<String, Object>() {{
            put("build", "Sample Build 001");
            put("name", "Sample Sauce Chrome Test");
        }});

        // Instantiate RemoteWebDriver with Sauce Labs URL and ChromeOptions
        WebDriver driver = new RemoteWebDriver(new URL(sauceURL), options);

        // Your test code
        driver.get("https://www.example.com");
        System.out.println(driver.getTitle());

        driver.quit();
    }
}




