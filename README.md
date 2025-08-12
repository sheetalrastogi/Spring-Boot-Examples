# RestAssured examples

import io.restassured.RestAssured;
import io.restassured.response.Response;
import org.testng.asserts.SoftAssert;
import org.testng.annotations.Test;

public class RestAssuredSoftAssertExample {

    @Test
    public void testWithSoftAssert() {
        SoftAssert softAssert = new SoftAssert();

        Response response = RestAssured.given()
            .get("https://jsonplaceholder.typicode.com/todos/1");

        softAssert.assertEquals(response.getStatusCode(), 200, "Status Code");
        softAssert.assertTrue(response.getBody().asString().contains("userId"), "Body should contain userId");
        softAssert.assertTrue(response.getBody().asString().contains("title"), "Body should contain title");
        softAssert.assertNotNull(response.jsonPath().get("completed"), "Completed field should not be null");

        // Report all assertion failures at once
        softAssert.assertAll();
    }
}
