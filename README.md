# hellocucumber

## Cucumber Integration

1) Add the cucumber dependency to the `pom.xml` file:
```xml
<dependency>
    <groupId>io.cucumber</groupId>
    <artifactId>cucumber-java</artifactId>
    <version>7.20.1</version>
    <scope>test</scope>
</dependency>
```

2) Create a new directory `src/test/resources/hellocucumber` and add a new file `is_it_friday_yet.feature`:
```gherkin
Feature: Is it Friday yet?                      # This is the feature definition (not executed)
  Everybody wants to know when it's Friday      # This is the feature description (not executed)

  Scenario: Sunday isn't Friday                 # This is the scenario definition (i.e. a test case)
    Given today is Sunday                      
    When I ask whether it's Friday yet         
    Then I should be told "Nope"               

  Scenario: Friday is Friday
    Given today is Friday
    When I ask whether it's Friday yet
    Then I should be told "Yessir"

  Scenario Outline: Today is or is not Friday   # Alternative way to define scenarios
    Given today is "<day>"                      # Here day is a variable
    When I ask whether it's Friday yet
    Then I should be told "<answer>"            # Same here

    Examples:                                   # This is a table of examples
      | day            | answer |               # Which are used to fill in the variables
      | Sunday         | Nope   |
      | Friday         | Yessir |
      | anything else! | Nope   |
```

3) Create a new directory `src/test/java/hellocucumber` and add a new file `RunCucumberTest.java`:
```java
package hellocucumber;

import org.junit.platform.suite.api.ConfigurationParameter;
import org.junit.platform.suite.api.IncludeEngines;
import org.junit.platform.suite.api.SelectPackages;
import org.junit.platform.suite.api.Suite;

import static io.cucumber.junit.platform.engine.Constants.PLUGIN_PROPERTY_NAME;

@Suite
@IncludeEngines("cucumber")
@SelectPackages("hellocucumber")
@ConfigurationParameter(key = PLUGIN_PROPERTY_NAME, value = "pretty")
public class RunCucumberTest {
}
```

4) Create a new directory `src/test/java/hellocucumber` and add a new file `StepDefinitions.java`:
```java
package hellocucumber;

import io.cucumber.java.en.*;

import static org.junit.jupiter.api.Assertions.*;

class IsItFriday {
    static String isItFriday(String today) {
        if (today.equals("Friday"))
            return "Yessir";
        return "Nope";
    }
}

public class StepDefinitions {
    private String today;
    private String actualAnswer;

    @Given("today is Sunday")
    public void today_is_sunday() {
        today = "Sunday";
    }

    @Given("today is Friday")
    public void todayIsFriday() {
        today = "Friday";
    }

    @Given("today is {string}")
    public void today_is(String today) {
        this.today = today;
    }

    @When("I ask whether it's Friday yet")
    public void i_ask_whether_it_s_friday_yet() {
        actualAnswer = IsItFriday.isItFriday(today);
    }

    @Then("I should be told {string}")
    public void i_should_be_told(String expectedAnswer) {
        assertEquals(expectedAnswer, actualAnswer);
    }

}
```

These methods are not automatically created, but when running ```mvn test``` with only the ```is_it_fryday_yet.feature``` file,
the methods with an empty body are shown in the console. You can copy them and paste them in the ```StepDefinitions.java``` file 
and fill them with code that turns the phrase into concrete actions.

5) Run the tests with ```mvn test```.

### Refactoring!
The class `IsItFriday` should not be in the `StepDefinitions` class. It should be contained, where the production
code is located.

## Jira Plugin for Requirement Management
TODO