# Espresso

## Theory

The core API is small, predictable, and easy to learn and yet remains open for customization. Espresso tests state expectations, interactions, and assertions clearly without the distraction of boilerplate content, custom infrastructure, or messy implementation details getting in the way.

Espresso tests run optimally fast! It lets you leave your waits, syncs, sleeps, and polls behind while it manipulates and asserts on the application UI when it is at rest.

Here’s an overview of the main components of Espresso:

* Espresso – Entry point to interactions with views (via onView and onData). Also exposes APIs that are not necessarily tied to any view (e.g. pressBack).

* ViewMatchers – A collection of objects that implement Matcher<? super View> interface. You can pass one or more of these to the onView method to locate a view within the current view hierarchy.

* ViewActions – A collection of ViewActions that can be passed to the ViewInteraction.perform() method (for example, click()).

* ViewAssertions – A collection of ViewAssertions that can be passed the ViewInteraction.check() method. Most of the time, you
will use the matches assertion, which uses a View matcher to assert the state of the currently selected view.

**Example:** 

```Java
onView(withId(R.id.my_view)) // withId(R.id.my_view) is a ViewMatcher 
.perform(click()) // click() is a ViewAction 
.check(matches(isDisplayed())); //matches (isDisplayed()) is a ViewAssertion
```

Instead of onView(...). onData(...) is used to test AdapterView.

```Java
onData(withId(R.id.my_view)) // withId(R.id.my_view) is a ViewMatcher 
.perform(click()) // click() is a ViewAction 
.check(matches(isDisplayed())); //matches (isDisplayed()) is a ViewAssertion
```

Please refer to the cheat sheet to get all available features of each ViewMatcher, ViewActions and ViewAssertions which are mentioned above.

You can use Hamcrest matchers in Espresso. It gives you more flexibility and options to test UI components. Please visit http://code.google.com/p/hamcrest/wiki/Tutorial to do a bit study on Hamcrest matchers. Take a look particularly on “A tour of common matchers” this section.


##Gradle Setup

Add the following dependencies in your build.gradle file. Please note that these libraries are always keep updating.


```Java
//Make sure to specify AndroidJUnitRunner as the default test instrumentation runner in your project.
android {
    defaultConfig {
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    // Required -- JUnit 4 framework
    testCompile 'junit:junit:4.12'
    // Optional -- Mockito framework
    testCompile 'org.mockito:mockito-core:1.10.19'
    // Optional -- Hamcrest library
    androidTestCompile 'org.hamcrest:hamcrest-library:1.3'
    // Optional -- UI testing with UI Automator
    androidTestCompile 'com.android.support.test.uiautomator:uiautomator-v18:2.1.1'
    androidTestCompile 'com.android.support.test:runner:0.4'
    androidTestCompile 'com.android.support.test:rules:0.4'
    // Optional -- UI testing with Espresso
    androidTestCompile 'com.android.support.test.espresso:espresso-core:2.2'
    // add this for intent mocking support
    androidTestCompile 'com.android.support.test.espresso:espresso-intents:2.2'
    // add this for webview testing support
    androidTestCompile 'com.android.support.test.espresso:espresso-web:2.2'
    compile 'com.android.support:appcompat-v7:23.0.1'
    compile 'com.android.support:design:23.0.1'
}
```

##Test Project

Android Studio automatically creates a test directory/package called androidTest when you create a project.  You can write your test classes under that directory.

##How to writeTest Classes?

```Java
import android.test.ActivityInstrumentationTestCase2;

@RunWith(AndroidJUnit4.class)
public class YourTestClassName {

//Common setup for all test classes

@Rule
public ActivityTestRule<Activity_You_Want_To_Test> mActivityRule =
         new ActivityTestRule<>( Activity_You_Want_To_Test.class);


//End of Common setup for all test classes


//Now you can write your test methods

@Test
public void simpleTestMethod (){

//Checking existence of views
onView(withId(R.id.txtUserID)).check(matches(isDisplayed()));


//Checking View's Text
onView(withId(R.id.txtUserID)).check(matches(withText("UserID")));
onView(withId(R.id.txtPass)).check(matches(withText("Password")));
onView(withId(R.id.btnLogin)).check(matches(withText("Login")));

//Entering value in EditText
onView(withId(R.id.edtUserID)).perform(typeText("shohrab"), closeSoftKeyboard());
onView(withId(R.id.edtPass)).perform(typeText("123456"), closeSoftKeyboard());

//Checking value in EditText
onView(withId(R.id.edtUserID)).check(matches(withText("shohrab")));
onView(withId(R.id.edtPass)).check(matches(withText("123456")));

//Perform Button Click
onView(withId(R.id.btnLogin)).perform(click());
}

@Test
public void testSetDate() {
    int year = 2015, month = 10, day = 20;

    //Perform Button Click
    onView(withId(R.id.your_date_picker_button)).perform(click());

/**Open DatePicker Dialog and set day,month and year. withClassname (..) returns a matcher that matches Views with class name matching the given matcher. Matchers is a hamcrest class. 
**/
onView(withClassName(Matchers.equalTo(DatePicker.class.getName()))).perform(PickerActions.setDate(year, month, day));

//Perform Positive Button click of DatePicker Dialog
     onView(withId(android.R.id.button1)).perform(click());

}

@Test
public void testSetTime() {
    int hour = 5, minutes = 30;

   //Perform Button Click
    onView(withId(R.id.your_time_picker_button)).perform(click());

/**Open TimePicker Dialog and set hour and minute. withClassname (..) returns a matcher that matches Views with class name matching the given matcher. Matchers is a hamcrest class. 
**/
    onView(withClassName(Matchers.equalTo(TimePicker.class.getName()))).perform(PickerActions.setTime(hour, minutes));

//Perform Positive Button click of DatePicker Dialog
onView(withId(android.R.id.button1)).perform(click());
}

@Test
public void testOverflowMenuOrOptionsMenu() {
    // Open the action bar overflow or options menu (depending if the    device has or not a hardware menu button.)
    openActionBarOverflowOrOptionsMenu(getInstrumentation().getContext());

    // Find the menu item with text "About" and click on it
    onView(withText("About")).perform(click());

    // Verify the correct item was clicked by checking the content of the status TextView
    onView(withId(R.id.status)).check(matches(withText("About")));
}

@Test
public void testClickOnListItemByPosition(){

   /** Perform click on 5th item of a ListVie. anything() is a hamcrest matcher which always matches, useful if you don't care what the object under test is.**/
    onData(anything()).atPosition(5).perform(click());

/**Match a product record with a specific ID. allof – is a hamcrest matcher which matches if all matchers match, short circuits (like Java &&). See CustomMatchers class below for withProductID method. **/ 
    onData(allOf(withProductID (1)).perform(click());


}
@Test
public void testSpinner () {
    // Click on the Spinner
    onView(withId(R.id.your_spinner_view)).perform(click());

    /** Click on the first item from the list. is – is a hamcrest matcher which is decorator to improve readability. It is equivalent to equalto().   **/
onData(allOf(is(instanceOf(String.class)))).atPosition(0).perform(click());

    
    
}

@Test
public void testPagerViewOnSwipe() {
    // Locate the ViewPager and perform a swipe left action
    onView(withId(R.id.your_pager_view_id)).perform(swipeLeft());

// Check Adapterview is displayed or not and perform click on second item. onData(allOf(withProductID(1)))
.inAdapterView(allOf(isAssignableFrom(AdapterView.class), isDisplayed()))
.perform(click());


}
//End of test methods

}

//Custom Matcher Class

public class CustomMatchers {

import org.hamcrest.Description;
import org.hamcrest.Matcher;
import org.hamcrest.TypeSafeMatcher;
import android.support.test.espresso.matcher.BoundedMatcher;
import android.view.View;
import android.widget.Adapter;
import android.widget.AdapterView;
import static org.hamcrest.Matchers.equalTo;
import static com.android.support.test.deps.guava.base.Preconditions.checkNotNull;


public static Matcher<Object> withProductID(final String id) {
    return new BoundedMatcher<Object, Product >(Product.class) {
        @Override
        protected boolean matchesSafely(Product product) {
            return id.equals(product.getId);
        }

        @Override
        public void describeTo(Description description) {
            description.appendText("with id: " + id);
        }
    };
}
}


// this class is ca be used for creating List of objects to display in a ListView
public class Product {
    private int id;
    private String name;
    private String description;

    public Book(int id, String name, String description) {
        this.id = id;
        this.name = title;
        this.description = author;
    }

    public String getName() {
        return name;
    }

    public String getDescription() {
        return description;
    }

    public int getId() {
        return id;
    }
}

```

Attention: All of your R.id.Something should be uniquie otherwise Espresso will give AmbiguousViewMatcherException exception.

##Rererences:
* http://code.google.com/p/hamcrest/wiki/Tutorial
* https://developer.android.com/training/testing/ui-testing/espresso-testing.html
* https://google.github.io/android-testing-support-library/docs/espresso/basics/index.html
* https://github.com/jordanterry/Espresso-Examples
