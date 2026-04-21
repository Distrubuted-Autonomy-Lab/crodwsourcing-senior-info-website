# Test Cases

# General Description

Testing is conducted with Playwright installed with Node.js. The tests themselves can be located in the **root/tests** folder and are installed independently of the docker containers to allow for testing of the program locally and when it is enabled on the cloud. You can adjust this by changing the **BASE_URL** field when the tests are run (including local host for running the tests locally). The tests currently do not thoroughly examine the moderator or similarity reports, but test cases for these should be created as well. Additionally, the tests are not currently automated, but can be by installing Playwright within the docker container in the future.

---

## Running the Tests

Once you ensure the Node modules are up-to-date, ensure you are in the **root** folder before running any of the following commands.

To run all tests:
```bash
BASE_URL=http://exampleurl.com npx playwright test
```

To run a specific test:
```bash
BASE_URL=http://exampleurl.com npx playwright test [test_name_file.js]
```

### manage.spec.ts

This file tests the website's navigation from the home screen to the manage page where you can submit a resource. It also tests the button to ensure it works correctly.

### public_submit.spec.ts

This file tests to make sure the form loads correctly and that input validation works correctly.