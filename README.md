EnsembleWorkflow
==============
Restful web API for InterSystems Ensemble Workflow

## Installation
1. Import and compile this project.
2. Create a web-application for REST in the Portal Management System (for ex. /csp/workflow/rest). Set dispatch class to Workflow.REST, Authentication methods to 'Unauthorized' and 'Password'.
3. (Optional) add Workflow package mapping if you need to query another namespace.


##Requests

These are the possible requests to web application (add param `?Namespace={Desired Namespace}` to query another namespace):

| URL                         | Type | Body (JSON)                 | Response  | Description                    |
|-----------------------------|------|-----------------------------|-----------|--------------------------------|
| tasks                       | GET  |                             | JSON      | Get unassigned tasks or tasks assigned to current user |
| tasks/:id                   | GET  |                             | JSON      | EnsLib.Workflow.Worklist object|
| tasks/:id                   | POST |{EnsLib.Workflow.Worklist object}|JSON   | Processing of modified object  |
| test                        | GET  |                             | JSON      | Test info                      |


## Prerequisites

If you work with Ensemble Workflow it is recommended to familiaze yourself with [EnsLib.Workflow](http://docs.intersystems.com/latest/csp/documatic/%25CSP.Documatic.cls?PAGE=CLASS&LIBRARY=ENSLIB&PACKAGE=1&CLASSNAME=EnsLib.Workflow) package. Here's quick primer on the main classes:

| Class                            | Description                                                                                                                                                                                                                                                                                                    | 
|----------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------| 
| EnsLib.Workflow.ActionDefinition | Defines the set of available actions that a user can make within a Workflow application. Users can extend this list.                                                                                                                                                                                           | 
| EnsLib.Workflow.Engine           | Provides the core APIs for the Ensemble Workflow system.                                                                                                                                                                                                                                                       | 
| EnsLib.Workflow.FormEngine       | Provides the API for managing form associated with Workflow tasks.                                                                                                                                                                                                                                             | 
| EnsLib.Workflow.Operation        | This Business Operation manages the delegation of workflow tasks to the Ensemble Workflow Engine.                                                                                                                                                                                                              | 
| EnsLib.Workflow.RoleDefinition   | Defines a workflow role and its members.                                                                                                                                                                                                                                                                       | 
| EnsLib.Workflow.RoleMembership   | Manages the many-to-many relationship of workflow Users and Roles. Each instance represents the association of a specific User with a specific Role.                                                                                                                                                           | 
| EnsLib.Workflow.TaskRequest      | A task is a specialized request for a user action (it is used as part of a Workflow application).                                                                                                                                                                                                              | 
| EnsLib.Workflow.TaskResponse     | Response from a Workflow Task request. The Workflow Engine creates an instance of TaskResponse object as soon it receives a TaskRequest. This object is used to maintain the status of the task while it is under the control of the Workflow Engine. It also serves as the final response returned by the Workflow Engine when the task is complete.| 
| EnsLib.Workflow.TaskStatus       | Holds Task Status details used by Workflow Engine to manage tasks.                                                                                                                                                                                                                                             | 
| EnsLib.Workflow.UserDefinition   | Defines a workflow user. Typically the user name matches a system user name but this is not required. Workflow users that are not registered system users will not be able to log into the Workflow portal and check the status of their worklist.                                                             |  
| EnsLib.Workflow.Worklist         | Represents a worklist item associated with a User within a Workflow application.                                                                                                                                                                                                                               | 


## GET tasks 

It's the basic and first requst you need to execute after logging in (by authentication method you configured for REST web application). It returns basic information unassigned tasks or tasks assigned to current user, here's how it looks like:

```
{
   "children":[
      {
         "ID":"383015||user4",
         "New":"",
         "Priority":3,
         "Subject":"Task subject",
         "Message":"some additional message",
         "TaskId":383015,
         "RoleName":"Role",
         "AssignedTo":"user4",
         "TimeCreated":"2016-12-07 22:09:03.599",
         "Age":"00w 0d 16h 32m 30s",
         "UserName":"user4"
      },
      {
         "ID":"383011||user4",
         "New":"*",
         "Priority":1,
         "Subject":"Task subject",
         "Message":"some additional message",
         "TaskId":383011,
         "RoleName":"Role",
         "AssignedTo":"",
         "TimeCreated":"2016-12-07 20:43:21.627",
         "Age":"00w 0d 17h 58m 12s",
         "UserName":"user4"
      }
   ],
   "total":2
}
```
Here we can see two tasks, fist assigned to a current user (user4) and second one unassigned to the user. Object properties are 

| Property    | Description                                                                        | Value                                                  | 
|-------------|------------------------------------------------------------------------------------|--------------------------------------------------------| 
| ID          | Id of this EnsLib.Workflow.Worklist                                                |                                                        | 
| New         | Has the user seen this item yet?                                                   | * for no (new, never seen), "" for yes (not new, seen) | 
| Priority    | Priority of the requested Task                                                     | 1 is highest                                           | 
| Subject     | Short summary of this task                                                         |                                                        | 
| Message     | Detailed message body for this task                                                | First 25 symbols only                                  | 
| TaskId      | Id of EnsLib.Workflow.TaskResponse object                                          |                                                        | 
| RoleName    | The workflow role (EnsLib.Workflow.RoleDefinition)                                 |                                                        | 
| AssignedTo  | Name of the user that has currently has ownership of the associated task (if any). | Either current user or no one                          | 
| TimeCreated | Creation time                                                                      | UTC timestamp                                          | 
| Age         | Age of this item                                                                   |                                                        | 
| UserName    | Current workflow user  (EnsLib.Workflow.UserDefinition)                            | Current user                                           | 
