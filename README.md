EnsembleWorkflow
==============
Restful web API for InterSystems Ensemble Workflow

## Installation
1. Import and compile this project.
2. Create a web-application for REST in the Portal Management System (for ex. /csp/workflow/rest). Set dispatch class to Workflow.REST, Authentication methods to 'Unauthorized' and 'Password'.
3. (Optional) add Workflow packagfe mapping if you need to query another namespace.


##Requests

These are the possible requests to web application (add param ?Namespace={Desired Namespace} to query another namespace cubes):

| URL                         | Type | Body (JSON)                 | Response  | Description                    |
|-----------------------------|------|-----------------------------|-----------|--------------------------------|
| login                       | POST | { "Username":"CacheUser","Password":"CachePassword" }| JSON| Current user|
| tasks                       | GET  |                             | JSON      | Unassigned tasks or assigned to current user |
| tasks/:id                   | GET  |                             | JSON      | EnsLib.Workflow.Worklist object|
| tasks/:id                   | POST |{EnsLib.Workflow.Worklist object}|JSON   | Processing of modified object  |
| test                        | GET  |                             | JSON      | Test info                      |
