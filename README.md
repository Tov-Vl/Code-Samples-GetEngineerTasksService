# Code-Samples-GetEngineerTasksService

Code samples for the C#/.NET Backend Developer position from the project GetEngineerTasksService I've worked on.

GetEngineerTasksService is a service built on ASP.NET Core MVC that implements the REST's GET method to get a list of mount engineer tasks from WFM (Workforce Management System) by his login and the date range.

## Files description

**WFM.GetEngineerTasksService**:
- `Program` - configuring service's `Startup`, replacing `IServiceProviderFactory` with Unity Container, configuring logging (NLog), then bulding and starting the service.
- `Startup` - configuring HTTP request pipeline, adding dependencies and interceptors into provided DI container.
- Controllers
  - `EngineerTasksController` - a controller handling the endpoint requests.

**WFM.GetEngineerTasksServiceLib**:
- EngineerTasks
  - Models
    - `EngineerTasks` - the controller's return type.
    - `TaskDto` - an engineer's task info (ID, task type, status etc.).
  - `CorrDateEngineerTasksRepository` - an `IEngineerTasksRepository` decorator, replacing an unspecified `dateTo` parameter with `dateFrom`.
  - `EngineerTasksRepository` - base `IEngineerTasksRepository` repository, implementing the main logic of returning mount engineer's tasks via the `GetEngineerTasks` method.
  - `ExceptionHandlerEngineerTasksRepository` - `IEngineerTasksRepository` decorator, handling `EngineerNotFoundException`.
- Helpers
  - `EngineerNotFoundException` - an `Exception` derived class used when there is no engineer in the database with the specified login.
- Mapper - classes, implementing one-to-one mapping logic between internal WFM classes (used by the `SoAssignmentRepository` class to map the intermediate result to the `GetSoAssignments` method return type).
- Options - classes, encapsulating WFM's endpoint URLs, and classes for reading these settings from appsettings.json via the `IConfiguration` class.
- SoRepositories
  - `CorrUnavailabilitiesSoTaskRepository` - an `ISoTaskRepository` decorator for excluding the tasks with the wrong key from the `GetTasks`'s method input parameter.
  - `SoAssignmentRepository` - an `ISoAssignmentRepository` repository returning WFM's `Assignment` object - an object representing a task assigned to the specified engineer - by engineer ID and date range.
  - `SoEngineerRepository` - an `ISoEngineerRepository` repository returning WFM's `Engineer` object by the engineer's name.
  - `SoEngineerRepositoryFactoryExtension` - an `UnityContainerExtension` derived class, implementing the caching functionality of the returned `Engineer` object for all the `ISoEngineerRepository` `GetEngineer`'s invocations during a single request.
  - `SoTaskRepository` - an `ISoTaskRepository` repository returning WFM's `Task`s array by an array of task keys.
  - `ValidatingSoEngineerRepository` - an `ISoEngineerRepository` repository throwing `EngineerNotFoundException` exception if an `Engineer` with the specified name was not found.
