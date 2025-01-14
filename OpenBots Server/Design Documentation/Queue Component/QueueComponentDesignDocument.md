Author: Nicole Carrero
Creation Date: 8/19/2020

Updated On: 1/7/2021
Updated By: Nicole Carrero

**Queue Component**

**Context**

- Problem: Users are unable to view, create, edit, delete, and utilize queues.  A user should be able to create new queues by defining their name and retry count as well as either edit or delete existing queues from the list.  In addition, a user should be able to utilize the queues to store queue item transactions from automations and update the status of those transactions containing a priority and arbitrary data in text/JSON format.  A user should be able to view the list of queues and some basic statistics about each queue.  In addition, a user should be able to add, edit, delete, or view queue items that have been assigned to each queue and the appropriate details for each queue item.  A user should be able to attach files to queue items when necessary and edit or remove those files.
- Requirements: Ensure that a user is able to create new named queues and view the list of created queues, edit and delete existing queues, create transactions from automations associated with a queue, and view, create, edit, or delete arbitrary text/JSON data as queue item transactions.  Ensure that a user is able to attach files to a queue item and edit or delete an attached file.

**Component Scope**

- In-Scope:
  - User can create new named queues.
  - User can view, edit, or delete existing queues.
  - User can create queue item transactions from automations associated with a queue.
  - User can view, create, edit, or delete arbitrary text/JSON data as queue item transactions.
  - User can attach files to a queue item.
  - User can edit or remove a file attached to a queue item.
- Out-of-Scope:
  - User can commit, rollback, or extend queue item properties such as State, State Message, IsLocked, LockedOnUTC, LockedBy, LockedUntilUTC, etc.

**Design**

- Overview: The overall structure uses the QueuesController to allow the user to make a web request for the list of queues or to create, edit, or delete a specific queue.  The QueuesController gets called, which in turn utilizes methods in the QueueManager, then uses the QueueRepository to retrieve, add, update, or delete the appropriate data from the Server if the request is valid.  The overall structure of the QueueItemsController allows the user to make a web request to view the queue item transactions or to create, edit, or delete a transaction.  The QueueItemsController gets called, which in turn accesses the QueueItemManager, and then uses the QueueItemRepository to retrieve, create, edit, or delete the appropriate data from the Server if the request is valid.  The overall structure of the QueueItemAttachmentsController allows the user to make a web request to view the queue item attachments or to create, edit, or delete an attachment.  The QueueItemAttachmentsController gets called, which in turn accesses the QueueItemManager, and then uses the QueueItemRepository to retrieve, create, edit, or delete the appropriate data from the Server if the request is valid.  If the request is invalid for any of the above requests, the user will receive an error message stating the error that occured while trying to process the request.
- Proposed Solution:
  - User Interface:
    - In the Dashboard under Queues, there will be links to All Queue Items, Add Queue Item, All Queues, and Add Queue.
    - On the All Queue Items page, the queues will be displayed in a dropdown, which when selected, will show all queue items assigned to it in a table.
      - The headers will be: Name, State, State Message, Locked By, Start Time, Expiration Time, End Time, View, Edit, and Delete.
        - The appropriate data will be displayed underneath each heading for all queue items.
      - There will be a button on the page to allow the user to create a new queue item and add it to the list.
      - There will be a switch where the user can keep a watch for when queue items have been added, altered, or removed.
      - The user can click on each queue item to edit or delete it along with any attached files.  The user can also view the queue item details along with any attached files on a separate page.
    - On the All Queues Page, the queues will be shown in a table.
      - The headers will be: Name, Max Retry Count, Created By, Created On, View, Edit, and Delete.
        - The appropriate data will be displayed underneath each heading.
      - There will be a button on the page to allow the user to create a new queue and add it to the list.
      - The user can click on each queue to edit or delete it, or view the queue details on a separate page.
    - On the Add Queue and Add Queue Item pages, the user can input the appropriate details to create a new queue or queue item.
- Controllers:
  - QueuesController:
    - The QueuesController will make an API request to access the QueueRepository to retrieve all queues from the Server and will return that information back to the view.  The controller will utilize this same structure to add, edit, or delete a queue.
    - Routes:
      - All queues: [HttpGet("api/v1/queues")]
        - Payloads
          - Input : None
          - Output : JSON file listing all queue information
      - Queue count: [HttpGet("api/v1/queues/count")]
        - Payloads
          - Input : None
          - Output : Count of queues in the Server
      - Queue details: [HttpGet("api/v1/queues/{id}")]
        - Payloads
          - Input : None
          - Output : JSON file listing specific queue information
      - Create a queue: [HttpPost("api/v1/queues")]
        - Payloads
          - Input : Queue model data
          - Output : 200 OK response
      - Update a queue: [HttpPut("api/v1/queues/{id}")]
        - Payloads
          - Input : Queue id, Queue model data
          - Output : 200 OK response
      - Update a queue property: [HttpPatch("api/v1/queues/{id}")]
        - Payloads
          - Input : JSONPatchDocument in request body with changes
          - Output : 200 Ok response
      - Delete a queue: [HttpDelete("api/v1/queues/{id}")]
        - Payloads
          - Input : Queue id
          - Output : 200 OK response
  - QueueItemsController:
    - The QueueItemsController will make an API request and use the QueueItemManager to execute any business logic involved.  It will then access the QueueItemRepository to retrieve all queue item transactions associated with that particular queue from the Server and will return the information back to the view.  The controller will utilize this same structure to add, edit, delete, or view details of a queue item transaction.
    - Routes:
     - Queue items: [HttpGet("api/v1/queueitems")]
       - Payloads
         - Input : None
         - Output : JSON file listing queue item information
     - Queue items (view model): [HttpGet("api/v1/queueitems/view")]
       - Payloads
         - Input : None
         - Output : JsON file listing queue item view model information
     - Queue item details: [HttpGet("api/v1/queueitems/{id}")]
       - Payloads
         - Input : Queue item id
         - Output : JSON file listing specific queue item information
     - Queue items details (view model): [HttpGet("api/v1/queueitems/view/{id}")]
       - Payloads
         - Input : Queue item id
         - Output : JSON file listing specific queue item view model information
     - Queue item count: [HttpGet("api/v1/queueitems/count")]
       - Payloads
         - Input : None
         - Output: Count of queue items in the Server
     - Delete a queue item: [HttpDelete("api/v1/queueitems/{id}")]
       - Payloads
         - Input : Queue item id
         - Output : 200 OK response
     - Update queue item property: [HttpPatch("api/v1/queueitems/{id}")]
       - Payloads
         - Input : JSONPatchDocument in request body with changes
         - Output : 200 OK response
     - Create queue item (enqueue): [HttpPost("api/v1/queueitems/enqueue")]
       - Payloads
         - Input : Queue item model data
         - Output : JSON file listing new queue item view model information
     - Pick up next available queue item (dequeue): [HttpPut("api/v1/queueitems/dequeue")]
       - Payloads
         - Input: Agent id, Queue id
         - Output: JSON file listing specific queue item view model information
     - Successful queue item (commit): [HttpPut("api/v1/queueitems/commit")]
       - Payloads
         - Input : Lock transaction key
         - Output : JSON file listing updated queue item information
     - Retry/fail queue item (rollback): [HttpPut("api/v1/queueitems/rollback")]
       - Payloads
         - Input : Lock transaction key, Error code (nullable), Error message (nullable), Is fatal (default set to false)
         - Output : JSON file listing updated queue item information
     - Extend queue item: [HttpPut("api/v1/queueitems/extend")]
       - Payloads
         - Input : Lock transaction key
         - Output : JSON file listing updated queue item information
     - Edit state: [HttpPut("api/v1/queueitems/{id}/state")]
       - Payloads
         - Input : Lock transaction key, State (nullable), State message (nullable), Error code (nullable), Error message (nullable)
         - Output : JSON file listing updated queue item information
     - Edit queue item (with attachments): [HttpPut("api/v1/queueitems/{id}")]
       - Payloads
         - Input : Queue item id, Update queue item view model
         - Output : JSON file listing updated queue item view model information
  - QueueItemAttachmentsController:
    - The QueueItemAttachmentsController will make an API request and use the QueueItemManager to execute any business logic involved.  It will then access the QueueItemAttahmentRepository to retrieve all queue item attachments associated with that particular queue item from the Server and will return the information back to the view.  The controller will utilize this same structure to add, edit, delete, or view details of a queue item transaction.
    - Routes:
     - Queue item attachments: [HttpGet("api/v1/queueitems/{queueItemId}/queueitemattachments")]
       - Payloads
         - Input : None
         - Output : JSON file listing queue item attachment information
     - Queue item attachments (view model): [HttpGet("api/v1/queueitems/{queueItemId}/queueitemattachments/view")]
       - Payloads
         - Input : None
         - Output : JSON file listing queue item attachment view information
     - Queue item count: [HttpGet("api/v1/queueitems/{queueItemId}/queueitemattachments/count")]
       - Payloads
         - Input : None
         - Output : JSON file listing queue item attachment count
     - Queue item attachment details: [HttpGet("api/v1/queueitems/{queueItemId}/queueitemattachments/{id}")]
       - Payloads
         - Input : None
         - Output : JSON file listing specific queue item attachment information
     - Queue item attachments: [HttpGet("api/v1/queueitems/{queueItemId}/queueitemattachments")]
       - Payloads
         - Input : None
         - Output : JSON file listing queue item attachment information
     - Create queue item attachment (existing binary objects): [HttpPost("api/v1/queueitems/{queueItemId}/queueitemattachments/binaryobjects")]
       - Payloads
         - Input : Array of binary object ids
         - Output : JSON file listing queue item attachment information for a specific queue item
     - Create queue item attachment: [HttpPost("api/v1/queueitems/{queueItemId}/queueitemattachments")]
       - Payloads
         - Input : Files to be attached
         - Output : JSON file listing queue item attachment information for a specific queue item
     - Edit queue item attachment with file: [HttpPut("api/v1/queueitems/{queueItemId}/queueitemattachments/{id}/update")]
       - Payloads
         - Input : File to be updated
         - Output : JSON file listing specific queue item attachment information
     - Edit queue item attachment property: [HttpPatch("api/v1/queueitems/{queueItemId}/queueitemattachments/{id}")]
       - Payloads
         - Input : JSONPatchDocument in request body with changes
         - Output : 200 OK response
     - Delete all queue item attachments for a queue item: [HttpDelete("api/v1/queueitems/{queueItemId}/queueitemattachments")]
       - Payloads
         - Input : None
         - Output : 200 OK response
     - Delete queue item attachment: [HttpDelete("api/v1/queueitems/{queueItemId}/queueitemattachments/{id}")]
       - Payloads
         - Input : None
         - Output : 200 OK response
- Managers:
  - QueueManager:
    - The QueueManager will inherit BaseManager, which inherits IManager, and IQueueItemManager.
      - Beyond the base class and interfaces, QueueManager will implement the CheckReferentialIntegrity() method to assist QueuesController.
  - QueueItemManager:
    - The QueueItemManager will inherit BaseManager, which inherits IManager, and IQueueItemManager.
      - Beyond the base class and interfaces, QueueItemManager will implement the Enqueue(), Dequeue(), FindQueueItem(), FindExpiredQueueItem(), Commit(), Rollback(), Extend(), UpdateState(), GetQueueItem(), SetNewState(), SetExpiredState(), GetQueueItemsAndBinaryObjectIds(), GetQueueItemView(), AttachFiles(), and UpdateAttachedFiles() methods to assist QueueItemsController.
- Repositories:
  - QueueRepository:
    - The QueueRepository inherits from EntityRepository<Queue>, which inherits from ReadOnlyEntityRepository<Queue>, and IQueueRepository.
      - Beyond the base classes and interface, QueueRepository will retrieve all queues, add a new queue, or retrieve/edit/delete a queue by id.
  - QueueItemRepository:
    - The QueueItemRepository inherits from EntityRepository<QueueItemModel>, which inherits from ReadOnlyEntityRepository<QueueItemModel>, and IQueueItemRepository.
      - Beyond the base classes and interface, QueueItemRepository will retrieve all queue items, add a new queue item, or edit/delete a queue item by id.  It will have FindAllView() to allow the view model to display both the properties of the queue item and the corresponding binary object ids.
  - QueueItemAttachmentRepository:
    - The QueueItemAttachmentRepository inherits from EntityRepository<QueueItemAttachment> and IQueueItemAttachmentRepository.
      - Beyond the base class and interface, QueueItemAttachmentRepository will retrieve all queue item attachments, add a new queue item attachment, or edit/delete a queue item attachment by id.
- Models:
  - Queue Data Model:
    - The Queue data model will be used to view details of each queue.  It will inherit the NamedEntity class, which inherits the Entity class.
      - Beyond the base classes, Queue will have string Description and int MaxRetryCount.
  - QueueItemModel Data Model:
    - The QueueItemModel data model will be used to view details of each queue item.  It will inherit the NamedEntity class, which inherits the Entity class.
      - Beyond the base classes, QueueItemModel will have bool IsLocked, DateTime LockedOnUTC, DateTime LockedUntilUTC, Guid LockedBy, Guid QueueId, string Type, string JsonType, string DataJson, string State, string StateMessage, Guid LockTransactionKey, DateTime LockedEndTimeUTC, int RetryCount, int Priority, DateTime ExpireOnUTC, DateTime PostponeUntilUTC, string ErrorCode, string ErrorMessage, string ErrorSerialized, string Source, string Event, string ResultJSON, and long PayloadSizeInBytes.
  - QueueItemAttachment Data Model:
    - The QueueItemAttachment data model will be used to view the binary object ids that correlate with the attachments of a queue item.  It will inherit the Entity class.
      - Beyond the base class, QueueItemAttachment will have Guid QueueItemId, Guid BinaryObjectId, and long SizeInBytes.
  - Queue View Model:
    - The QueueViewModel view model will be used to view details of each queue.
      - QueueViewModel will have string Name (required), string Description, and int MaxRetryCount.
  - AllQueueItemsViewModel View Model:
    - The AllQueueItemsVieWModel view model will be used to view details of each queue item in addition to the list of related binary object ids (file attachments).  It will inherit IViewModel<QueueItemmodel, AllQueueItemsViewModel>.
      - Beyond the interface, AllQueueItemsViewModel will have string Name, Guid Id, string State, string StateMessage, bool IsLocked, Guid LockedBy, DateTime LockedOnUTC, DateTime LockedUntilUTC, DateTime LockedEndTimeUTC, DateTime ExpireOnUTC, DateTime PostponeUntilUTC, string ErrorCode, string ErrorMessage, string ErrorSerialized, string Source, string Event, string ResultJSON, List<Guid> BinaryObjectIds, Guid QueueId, DateTime CreatedOn, and long PayloadSizeInBytes.
  - QueueItemViewModel View Model:
    - The QueueItemViewModel view model will be used to view details of an individual queue item in addition to the list of related binary object ids (file attachments).  It will inherit NamedEntity, which inherits Entity, and IViewModel<QueueItemModel, QueueItemViewModel>.
      - Beyond the base classes and interface, QueueItemViewModel will have string State, string StateMessage, bool IsLocked, Guid LockedBy, DateTime LockedOnUTC, DateTime LockedUntilUTC, DateTime LockedEndTimeUTC, DateTime ExpireOnUTC, DateTime PostponeUntilUTC, string ErrorCode, string ErrorMessage, string ErrorSerialized, string Source, string Event, string ResultJSON, Guid QueueId, string Type, string JsonType, string DataJson, Guid LockTransactionKey, int RetryCount, int Priority, long PayloadSizeInBytes, and List<Guid> BinaryObjectIds.
  - UpdateQueueItemViewModel View Model:
    - The UpdateQueueItemViewModel view model will be used to update details of an individual queue item in addition to the list of related binary object ids (file attachments).  It will inherit IViewModel<QueueItemModel, UpdateQueueItemModel>.
      - Beyond the interface, UpdateQueueItemViewModel will have Guid Id, string Name, Guid QueueId, string Source, string Event, DateTime ExpireOnUTC, DateTime PostponeUntilUTC, string Type, string DataJson, string State, List<Guid> BinaryObjectIds, and IFormFile[] Files.
  - AllQueueItemAttachments View Model:
    - The AllQueueItemAttachmentsViewModel view model will be used to view details of queue item attachments associated with a particular queue item.
      - AllQueueItemAttachmentsViewModel will have Guid QueueItemId, Guid BinaryObjectId, long SizeInBytes, and string Name.

**Sequence Diagrams**

Queue
- [Queue Component Sequence Diagram](Sequence Diagrams/QueuesController/QueueComponentSequenceDiagram.png)
- [Queue Count Sequence Diagram](Sequence Diagrams/QueuesController/QueueCountSequenceDiagram.png)
- [Create Queue Sequence Diagram](Sequence Diagrams/QueuesController/CreateQueueSequenceDiagram.png)
- [Edit Queue Sequence Diagram](Sequence Diagrams/QueuesController/CreateQueueSequenceDiagram.png)
- [Edit Queue Property Sequence Diagram](Sequence Diagrams/QueuesController/EditQueueSequenceDiagram.png)
- [Queue Details Sequence Diagram](Sequence Diagrams/QueuesController/EditQueuePropertySequenceDiagram.png)
- [View Queue Sequence Diagram](Sequence Diagrams/QueuesController/QueueDetailsSequenceDiagram.png)
- [Delete Queue Sequence Diagram](Sequence Diagrams/QueuesController/DeleteQueueSequenceDiagram.png)
Queue Item
- [Queue Item Component Sequence Diagram](Sequence Diagrams/QueueItemsController/QueueItemComponentSequenceDiagram.png)
- [Queue Item Count Sequence Diagram](Sequence Diagrams/QueueItemsController/QueueItemCountSequenceDiagram.png)
- [Queue Item Details Sequence Diagram](Sequence Diagrams/QueueItemsController/QueueItemDetailsSequenceDiagram.png)
- [Enqueue Queue Item Sequence Diagram](Sequence Diagrams/QueueItemsController/CreateQueueItemSequenceDiagram.png)
- [Edit Queue Item Sequence Diagram (Dequeue, Commit, Extend, State, Edit with Attachments)](Sequence Diagrams/QueueItemsController/EditQueueItemSequenceDiagram.png)
- [Edit Queue Item Property Sequence Diagram](Sequence Diagrams/QueueItemsController/EditQueueItemPropertySequenceDiagram.png)
- [Delete Queue Item Sequence Diagram](Sequence Diagrams/QueueItemsController/DeleteQueueItemSequenceDiagram.png)
- [Rollback Queue Item Sequence Diagram](Sequence Diagrams/QueueItemsController/RollbackQueueItemSequenceDiagram.png)

Queue Item Attachments
- [Queue Item Attachment Component Sequence Diagram](Sequence Diagrams/QueueItemAttachmentsController/QueueItemAttachmentComponentSequenceDiagram.png)
- [Queue Item Attachment Count Sequence Diagram](Sequence Diagrams/QueueItemAttachmentsController/QueueItemAttachmentCountSequenceDiagram.png)
- [Queue Item Attachment Details Sequence Diagram](Sequence Diagrams/QueueItemAttachmentsController/QueueItemAttachmentDetailsSequenceDiagram.png)
- [Create Queue Item Attachment Sequence Diagram](Sequence Diagrams/QueueItemAttachmentsController/CreateQueueItemAttachmentSequenceDiagram.png)
- [Edit Queue Item Attachment Sequence Diagram](Sequence Diagrams/QueueItemAttachmentsController/EditQueueItemAttachmentSequenceDiagram.png)
- [Edit Queue Item Attachment Property Sequence Diagram](Sequence Diagrams/QueueItemAttachmentsController/EditQueueItemAttachmentPropertySequenceDiagram.png)
- [Delete Queue Item Attachment(s) Sequence Diagram](Sequence Diagrams/QueueItemAttachmentsController/DeleteQueueItemAttachmentSequenceDiagram.png)

**Unit Tests**

- Positive Test Cases: N/A
- Negative Test Cases: N/A