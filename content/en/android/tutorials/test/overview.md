---
title: Overview
weight: 1
type: overview
description: "In this section, you will learn more about Beagle built-in tests and tests in Beagle applications."
---

---

## Unit tests

Unit tests measure code functionality at its minor fraction or unit. Beagle has its own unit tests, and they should be frequently reviewed, updated or added to make sure important code units are tested.

## User interface tests

Beagle implements its own UI tests to make sure interface level components and their behavior work as expected. Both mobile and web platforms are tested this way and the main frameworks used are:
- [Cucumber](https://cucumber.io/)
- [Espresso](https://developer.android.com/training/testing/espresso)
- [XCTest](https://developer.apple.com/documentation/xctest)
- [Appium](http://appium.io/)
- [Cypress](https://www.cypress.io/)


## Mobile instrumented tests 

Instrumented tests, also known as instrumented unit tests, are tests that run on physical devices or emulators, and they take advantage of APIs to forward (instrument \ orchestrate) instructions from the code to the device or emulator. These tests provide more fidelity than local unit tests, but they run slower. 
On Beagle instrumented tests, sometimes a server-driven component doesn't or cannot have an ``ID``. In this case we add a widget element that refers to the component, and an ``ID``, so we can identify the component by the programmatically generated ``ID``.


## Mobile Instrumented Tests: example

In this example, it was implemented an instrumented test on the Android platform using Espresso framework. We verified the functionality selection and the value insertion of the server-driven text field component.

 ```Kotlin
@Test
    public void TestSelectTextField() {
        new BeagleRobot()
            .checkViewContainsText("Beagle Sample")
            .selectMenuOption()
            .clickOnText("TextField")
            .typeIntoTextField(0,0,"beagle")
            ;
    }
````

###  Step 1: Start the test

You start the test validating if the application was properly opened on the main page. The function ``checkViewContainsText`` validates if the header is presented on the screen. The component used here does not have an ``ID``, so you can use the text in the header (``withText(text)``) to locate the view on the main page: 

```Kotlin
public BeagleTest checkViewContainsText(String text) {
        onView(allOf(withText(text))).check(matches(isDisplayed()));
        return this;
}
````

### Step 2: Present the menu

After you locate the header, the next step is to use the function ``selectMenuOption`` to select the “hamburger” icon and present the options of the menu. Since the server-driven component doesn’t have an ``ID`` for this example, you have to use the component's placement to perform this selection. This way, you search for the text on the ContentDescription with value “More options”, and then its placement on the component's hierarchy:


```Kotlin
public BeagleTest selectMenuOption() {
        onView(allOf(withContentDescription("More options"), childAtPosition(childAtPosition(withId(R.id.action_bar), 1), 0))).perform(click());
        return this;
}
````

### Step 3: Select the component

Here, the test will click on a textfield component (located from its text). The component is a menu item and it was already loaded in the previous step.

```Kotlin
public BeagleTest clickOnText(final String text) {
        onView(allOf(withText(text), isDisplayed())).perform(click());
        return this;
}
````
### Step 4: Insert a component

You will insert a text to the text field component. Since this component doesn’t have an ``ID`` or a text, it will be located by its placement in the hierarchy of the related components. Check the function ``typeIntoTextField``:

```Kotlin
public BeagleTest typeIntoTextField(int position1, int position2, String text) {
        onView(allOf(childAtPosition(
          childAtPosition(
            withId(R.id.fragment_content), position1), position2)
          )
        ).perform(typeText(text));
                
        Espresso.closeSoftKeyboard();
        return this;
}
````

{{% alert color="success" %}}
That’s it, the test will interact with the interface and validate the component! 
{{% /alert %}}
