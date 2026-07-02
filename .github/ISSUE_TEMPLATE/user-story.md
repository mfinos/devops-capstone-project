**As a** [role]  
**I need** [function]  
**So that** [benefit]  
      
### Details and Assumptions
Project overview
You have been asked by the customer account manager at your company to develop an account microservice to keep track of the customers on your e-commerce website. Since it is a microservice, it is expected to have a well-formed REST API that other microservices can call. This service initially needs to create, read, update, delete, and list customers.

You have also been told that someone else has started on this task. They have already developed the database model and a Python Flask-based REST API with an endpoint to create a customer account. You just need to plan to add the REST APIs to read, update, delete, and list accounts. Since you will be working in an online lab environment, you will need to plan your work to get that environment ready for development.

### Acceptance Criteria     
    gherkin 
    Given [some context]
    When [certain action is taken]
    Then [the outcome of action is observed]

    Feature: The product store service back-end
   **As a** Product Store Owner
   **I need** a RESTful catalog service
   **So that** I can keep track of all my products


Feature: Read Customer Account Details

  Scenario: Successfully retrieve an existing account by ID
    Given the database contains an account with ID "101" and name "Alice Smith"
    When I send a "GET" request to "/accounts/101"
    Then the response status code should be "200"
    And the response body should contain the field "name" with value "Alice Smith"
    And the response body should contain fields "email", "address", and "date_joined"

  Scenario: Attempt to retrieve a non-existent account
    Given the database does not contain an account with ID "999"
    When I send a "GET" request to "/accounts/999"
    Then the response status code should be "404"
    And the response body should contain an error message "Account with ID 999 was not found"

Feature: Update Customer Account Profile

  Scenario: Successfully update contact fields of an existing account
    Given the database contains an account with ID "102" and phone_number "123-456"
    When I send a "PUT" request to "/accounts/102" with the following JSON:
      """
      {
        "phone_number": "987-654",
        "address": "123 New Dev Street"
      }
      """
    Then the response status code should be "200"
    And the persistent database record for ID "102" should have "phone_number" updated to "987-654"

  Scenario: Attempt to update an account that does not exist
    Given the database does not contain an account with ID "888"
    When I send a "PUT" request to "/accounts/888" with any valid payload
    Then the response status code should be "404"

Feature: Delete Customer Account Record

  Scenario: Successfully delete an active customer account
    Given the database contains an account with ID "103"
    When I send a "DELETE" request to "/accounts/103"
    Then the response status code should be "204"
    And the database should no longer contain an account record with ID "103"

  Scenario: Delete an account that has already been removed or does not exist
    Given the database does not contain an account with ID "777"
    When I send a "DELETE" request to "/accounts/777"
    Then the response status code should be "204"
    And the system should safely ignore the request without throwing an internal error

Feature: List All Customer Accounts

  Scenario: Retrieve a list when multiple accounts exist
    Given the database contains "3" customer account entries
    When I send a "GET" request to "/accounts"
    Then the response status code should be "200"
    And the response body should be a JSON array containing exactly "3" account items

  Scenario: Retrieve a list when no accounts exist in the database
    Given the account database is empty
    When I send a "GET" request to "/accounts"
    Then the response status code should be "200"
    And the response body should be an empty JSON array "[]"
