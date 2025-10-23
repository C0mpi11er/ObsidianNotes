->> get as much info about the api 
`GET /api/books HTTP/1.1 Host: example.com`

->> type of params
->> tyoe of request accepted
->> if their any limiations ...
basically just mapping the api out to get a goo knowledge of the attack surface 

->> always start by reading the human readable docs 
->> end pints too look for 
- `/api`
- `/swagger/index.html`
- `/openapi.json`
->> tip intruder can be used to find the end points 


Identifying supported HTTP methods

The HTTP method specifies the action to be performed on a resource. For example:

    GET - Retrieves data from a resource.
    PATCH - Applies partial changes to a resource.
    OPTIONS - Retrieves information on the types of request methods that can be used on a resource.

An API endpoint may support different HTTP methods. It's therefore important to test all potential methods when you're investigating API endpoints. This may enable you to identify additional endpoint functionality, opening up more attack surface.

For example, the endpoint /api/tasks may support the following methods:

    GET /api/tasks - Retrieves a list of tasks.
    POST /api/tasks - Creates a new task.
    DELETE /api/tasks/1 - Deletes a task.


note:
When testing different HTTP methods, target low-priority objects. This helps make sure that you avoid unintended consequences, for example altering critical items or creating excessive records.



API endpoints often expect data in a specific format. They may therefore behave differently depending on the content type of the data provided in a request. Changing the content type may enable you to:

- Trigger errors that disclose useful information.
- Bypass flawed defenses.
- Take advantage of differences in processing logic. For example, an API may be secure when handling JSON data but susceptible to injection attacks when dealing with XML.


