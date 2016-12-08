EnsembleWorkflow
==============
Restful web API for InterSystems Ensemble Workflow

## Installation
1. Import and compile this project.
2. Create a web-application for REST in the Portal Management System (for ex. `/csp/workflow/rest`). Set dispatch class to `Workflow.REST`, Authentication methods to 'Unauthorized' and 'Password'.
3. (Optional) Add Workflow package mapping if you need to query another namespace.


##Requests

These are the possible requests to web application (add param `?Namespace={Desired Namespace}` to query another namespace):

| URL                         | Type | Response  | Description                    |
|-----------------------------|------|-----------|--------------------------------|
| tasks                       | GET  | JSON      | Get unassigned tasks or tasks assigned to current user |
| tasks/:id                   | GET  | JSON      | EnsLib.Workflow.Worklist object|
| tasks/:id                   | POST | JSON      | Processing of modified object  |
| test                        | GET  | JSON      | Test info                      |


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
         "ID":"317||dev",
         "New":"*",
         "Priority":3,
         "Subject":"Problem reported by TestUser",
         "Message":"SampleTask",
         "TaskId":317,
         "RoleName":"Demo-Development",
         "AssignedTo":"",
         "TimeCreated":"2016-12-04 14:30:47.657",
         "Age":"00w 3d 23h 31m 58s",
         "UserName":"dev"
      },
      {
         "ID":"345||dev",
         "New":"",
         "Priority":3,
         "Subject":"Problem reported by TestUser",
         "Message":"SampleTask",
         "TaskId":345,
         "RoleName":"Demo-Development",
         "AssignedTo":"dev",
         "TimeCreated":"2016-12-04 14:30:35.647",
         "Age":"00w 3d 23h 32m 10s",
         "UserName":"dev"
      }
   ]
}
```
Here we can see two tasks, fist assigned to a current user (dev) and second one unassigned to the current user. Object properties are 

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

## GET tasks/:id

After you received main information about available tasks, you can see it in more detail, by requesting it by id (`345||dev` in example). Here's how response object looks like:

```
{
	"_class":"EnsLib.Workflow.Worklist",
	"_id":"345||dev",
	"Task": {
		"_class":"EnsLib.Workflow.TaskResponse",
		"_id":345,
		"%Action":"",
		"%Priority":3,
		"%UserName":"",
		"%UserTitle":"",
		"%UserRanking":"",
		"%RoleName":"Demo-Development",
		"%Subject":"Problem reported by TestUser",
		"%Message":"SampleTask",
		"%Actions":"Corrected,Ignored",
		"%FormTemplate":"",
		"%FormFields":"Comments",
		"%FormValues": {},
		"%Status":"Assigned",
		"%TaskStatus": {
			"_class":"EnsLib.Workflow.TaskStatus",
			"Role": {
				"_class":"EnsLib.Workflow.RoleDefinition",
				"_id":"Demo-Development",
				"Name":"Demo-Development",
				"Description":"",
				"Capacity":100
			},
			"AssignedTo":"dev",
			"TimeCreated":"2015-03-04 13:27:39.917",
			"TimeCompleted":"",
			"SessionId":291,
			"Source":"HelpDesk",
			"ResponseToken":"323|Demo.Workflow.Production",
			"IsComplete":false,
			"Request": {
				"_class":"EnsLib.Workflow.TaskRequest",
				"_id":344,
				"%Actions":"Corrected,Ignored",
				"%Subject":"Problem reported by TestUser",
				"%Message":"SampleTask",
				"%Priority":3,
				"%UserName":"",
				"%Title":"",
				"%TaskHandler":"",
				"%Command":"",
				"%FormTemplate":"",
				"%FormFields":"Comments",
				"%FormValues": {}
			}
		}
	},
	"User": {
		"_class":"EnsLib.Workflow.UserDefinition",
		"_id":"dev",
		"Name":"dev",
		"FullName":"",
		"IsActive":true
	},
	"Role":null,
	"TimeCreated":"2016-12-04 14:30:35.647",
	"IsNew":false,
	"Age":"00w 3d 23h 34m 48s"
}
```

It's just a json representation of [EnsLib.Workflow.Worklist](http://docs.intersystems.com/latest/csp/documatic/%25CSP.Documatic.cls?PAGE=CLASS&LIBRARY=ENSLIB&CLASSNAME=EnsLib.Workflow.Worklist) object.
This request provides enouth information to display task to the user.

##  POST tasks/:id

After user is done working on his task, you need to notify Workflow engine about new state of the task. To do thet, execute this request, with the json representation of EnsLib.Workflow.Worklist object (received from the previous request) as a body.
To express changes made by user, modify EnsLib.Workflow.Worklist object:

- Set `Task.%Action` property to one of %Actions values or `$Accept` to accept task, `$Relinquish` to relinquish task and `$Save` to save changes made to task
- Provide `Task.%FormValues` as an array with kays from `%FormFields` and values provided from a client

Here's an example of user completing `345||dev` task (by choosing Corrected action):

```
{  
   "_class":"EnsLib.Workflow.Worklist",
   "_id":"345||dev",
   "Task":{  
      "_class":"EnsLib.Workflow.TaskResponse",
      "_id":345,
      "%Action":"Corrected",
      "%Priority":3,
      "%UserName":"",
      "%UserTitle":"",
      "%UserRanking":"",
      "%RoleName":"Demo-Development",
      "%Subject":"Problem reported by TestUser",
      "%Message":"SampleTask",
      "%Actions":"Corrected,Ignored",
      "%FormTemplate":"",
      "%FormFields":"Comments",
      "%FormValues":{  
         "Comments":"user-made comment"
      },
      "%Status":"Assigned",
      "%TaskStatus":{  
         "_class":"EnsLib.Workflow.TaskStatus",
         "Role":{  
            "_class":"EnsLib.Workflow.RoleDefinition",
            "_id":"Demo-Development",
            "Name":"Demo-Development",
            "Description":"",
            "Capacity":100
         },
         "AssignedTo":"dev",
         "TimeCreated":"2015-03-04 13:27:39.917",
         "TimeCompleted":"",
         "SessionId":291,
         "Source":"HelpDesk",
         "ResponseToken":"323|Demo.Workflow.Production",
         "IsComplete":false,
         "Request":{  
            "_class":"EnsLib.Workflow.TaskRequest",
            "_id":344,
            "%Actions":"Corrected,Ignored",
            "%Subject":"Problem reported by TestUser",
            "%Message":"SampleTask",
            "%Priority":3,
            "%UserName":"",
            "%Title":"",
            "%TaskHandler":"",
            "%Command":"",
            "%FormTemplate":"",
            "%FormFields":"Comments",
            "%FormValues":{  
            }
         }
      }
   },
   "User":{  
      "_class":"EnsLib.Workflow.UserDefinition",
      "_id":"dev",
      "Name":"dev",
      "FullName":"",
      "IsActive":true
   },
   "Role":null,
   "TimeCreated":"2016-12-04 14:30:35.647",
   "IsNew":false,
   "Age":"00w 3d 23h 46m 40s"
}
```



