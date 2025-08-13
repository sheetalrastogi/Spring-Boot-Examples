# Related Tutorials

* [Using PowerMock with JUnit and Mockito](https://howtodoinjava.com/java/library/mock-testing-using-powermock-with-junit-and-mockito/)

* Input Method Editor
----------------------

IME - stands for Input Method Editor. Currently seems like this is only supported in Linux platform and Firefox browser.

When working with Chinese/Japanese or multi-byte characters that needs to input by Selenium in linux, you have to use input framework like IBus and the engines implemented on IBus like anthy (Japanese), pinyin (Chinese).


The following code example looks for anthy engine to input Japanese characters on a linux machine.

  @NeedsFreshDriver
  @Ignore(value = {IE, CHROME, FIREFOX},
          reason = "Not implemented on anything other than Firefox/Linux at the moment.")
  @NotYetImplemented(HTMLUNIT)
  @Test
  public void testShouldBeAbleToActivateIMEEngine() throws InterruptedException {
    assumeTrue("IME is supported on Linux only.",
               TestUtilities.getEffectivePlatform().is(Platform.LINUX));

    driver.get(pages.formPage);

    WebElement input = driver.findElement(By.id("working"));

    // Activate IME. By default, this keycode activates IBus input for Japanese.
    WebDriver.ImeHandler ime = driver.manage().ime();

    List<String> engines = ime.getAvailableEngines();
    String desiredEngine = "anthy";

    if (!engines.contains(desiredEngine)) {
      System.out.println("Desired engine " + desiredEngine + " not available, skipping test.");
      return;
    }

    ime.activateEngine(desiredEngine);

    int totalWaits = 0;
    while (!ime.isActivated() && (totalWaits < 10)) {
      Thread.sleep(500);
      totalWaits++;
    }
    assertTrue("IME Engine should be activated.", ime.isActivated());
    assertEquals(desiredEngine, ime.getActiveEngine());

    // Send the Romaji for "Tokyo". The space at the end instructs the IME to convert the word.
    input.sendKeys("toukyou ");
    input.sendKeys(Keys.ENTER);

    String elementValue = input.getAttribute("value");

    ime.deactivate();
    assertFalse("IME engine should be off.", ime.isActivated());

    // IME is not present. Don't fail because of that. But it should have the Romaji value
    // instead.
    assertTrue("The elemnt's value should either remain in Romaji or be converted properly."
        + " It was:" + elementValue, elementValue.equals(tokyo));
  }


Source:  https://stackoverflow.com/questions/26588441/what-exactly-does-ime-in-selenium-do/35068710#35068710


