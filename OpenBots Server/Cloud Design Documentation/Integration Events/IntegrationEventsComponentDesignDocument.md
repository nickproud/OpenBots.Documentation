Author: Dairon Hernandez
Creation Date: 01/06/2021

Updated On: 3/18/2021
Update By: Nicole Carrero

**IntegrationEvents Component**

**Context**

- Problem: Users are not able easily track events that occur to entities on the server.
- Requirements: Ensure that users are able to subscribe to integration events that exist withing the server.

**Component Scope**

- In-Scope:
  - Users can choose the events that they would like to subscribe to and be notified on.
  - Users can choose to subscribe to a specific Entity and be notified of all events that occur on that Entity
  - Users can choose the TransportType for the Integration Event
- Out-of-Scope:
  - Allow Users to create new Subscription Events for themselves

**Design**

- Overview: The IntegrationEventSubscriptions can be created and updated using the IPFencingController. Users may only subscribe to events that exist in the IntegrationEvents table. When an event occurs a new IntegrationEventLog will be created and if any subscriptions exist for that event, then a new IntegrationEventSubscriptonAttempt will occur.
- Proposed Solution:
  - User Interface:
    - In the Dashboard, there will be a link to redirect the user to the different IntegrationEvent tabs.
        - System Events Tab: Will contain a list of all the IntegrationEvents that a user can subscribe to.
        - Subscriptions Tab: Will contain a list of all existing subscriptions and additionally contains a button that allows users to create new subscriptions
        - Add Subscriptions Tab: Allows users to create new subscriptions
        - Logs Tab: Displays a paginated list of all IntegrationEvents that have occured in descending order
  - IntegrationEventsController:
    - The EventsController will be a ReadOnly controller which will be used to access the data stored inside the IntegrationEvents table.
  - InegrationEventLogsController
    - The Event Logs Controller will be a ReadOnly Entity Controller which gives users access to the logs of all IntegrationEvents that have occured
  - IntegrationEventSubscriptionAttemptController
    - The Attempt Controller will be a ReadOnly controller that will be used view information on each transport attempt that was made on an Integration subscription
  - IntegrationEventSubscriptionController
    - NOTE: The current API version is 1.
    - Routes:
      - All IntegrationEventSubscriptions: [HttpGet("api/apiVersion/organizations/{organizationId}/IntegrationEventSubscription")]
        - Payloads
          - Input : Organization id
          - Output : JSON file contataining all IntegrationEventSubscriptions
      - Individual IntegrationEventSubscription details: [HttpGet("api/apiVersion/organizations/{organizationId}/IntegrationEventSubscription/{id})]
        - Payloads
          - Input : Organization id, IntegrationEventSubscription id
          - Output : JSON file of all IntegrationEventSubscription information for the specified id
      - Create an IntegrationEventSubscription: [HttpPost("api/apiVersion/organizations/{organizationId}/IntegrationEventSubscription")]
        - Payloads
          - Input : Organization id, IntegrationEventSubscription model 
          - Output : JSON file listing new IntegrationEventSubscription information
      - Update an IntegrationEventSubscription: [HttpPut("api/apiVersion/organizations/{organizationId}/IntegrationEventSubscription/{id}")]
        - Payloads
          - Input : Organization id, IntegrationEventSubscription model
          - Output : JSON file listing updated IntegrationEventSubscription information
      - Delete a IntegrationEventSubscription: [HttpDelete("api/apiVersion/organizations/{organizationId}/IntegrationEventSubscription/{id}")]
        - Payloads
          - Input : Organization id, IntegrationEventSubscription id
          - Output : 200 OK response
      - Edit IntegrationEventSubscription property: [HttpPatch("api/apiVersion/organizations/{organizationId}/IntegrationEventSubscription/{id}")]
        - Payloads
          - Input : Organization id, IntegrationEventSubscription id, and JsonPatchDocument in request body with changes
          - Output : 200 OK response
  - IntegrationEventSubscriptionAttempt Manager:
    - The IntegrationEventSubscriptionAttemptManager will inherit BaseManager and IIntegrationEventSubscriptionAttemptManager.
      - Beyond the base class and interface, IntegrationEventSubscriptionAttemptManager will implement the appropriate methods to assist IntegrationEventsController.
  - IntegrationEventLog Manager
    - The IntegrationEventLogManager will inherit BaseManager and IIntegrationEventLogManager.
      - Beyond the base class and interface, IntegrationEventLogManager will implement the appropriate methods to assist IntegrationEventLogsController.
      - If log storage is set to Azure table storage, the IntegrationEventLogManager will also use the AzureTableLoggerAdapter to add, edit, export, and delete the appropriate logs.

**Sequence Diagrams**

- [Integration Event Sequence Diagram](Sequence Diagrams/IntegrationEventSequenceDiagram.png)
- [Integration Event Subscription Diagram](Sequence Diagrams/SubscriptionSequenceDiagram.png)

**Unit Tests**

- Positive Test Cases:

- Negative Test Cases: