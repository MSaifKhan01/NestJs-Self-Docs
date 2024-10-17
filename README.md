

# NestJS Setup Notes

### Prerequisites
- **Node.js** version 16 or higher must be installed to use NestJS, as it leverages modern JavaScript/TypeScript features available in these versions.



### Setup Commands
1. **Install Nest CLI**:  
   The Nest CLI simplifies project setup and development. Installing it globally allows you to access the `nest` command from anywhere.
   ```bash
   npm i -g @nestjs/cli
   ```

2. **Create New Project**:  
   The `nest new` command creates a new NestJS project with a default folder structure and configuration. Using the `--strict` flag enforces stricter TypeScript checks for better code quality and type safety.
   ```bash
   nest new project-name --strict
   ```

   - **Folder Structure**:  
     Once the project is created, you will have a standard folder structure that includes:
     - `src/`: Contains the application code (controllers, modules, services).
     - `test/`: Holds unit and integration test files.
     - `node_modules/`: Installed dependencies.



### Core Files Overview
NestJS organizes an application into **controllers**, **services**, and **modules** to follow the modular architecture pattern.

- **app.controller.ts**:  
  The controller handles incoming HTTP requests and returns responses to the client. Each route is mapped to a method within the controller. Here's an example of a simple GET request handler:
  ```typescript
  import { Controller, Get } from '@nestjs/common';

  @Controller()
  export class AppController {
    @Get()
    getHello(): string {
      return 'Hello World!';
    }
  }
  ```
  **Explanation**:  
  - `@Controller()`: Declares the class as a controller.
  - `@Get()`: Maps HTTP GET requests to the `getHello` method.
  - The method returns a string `Hello World!` when accessed via `GET /` route.

- **app.module.ts**:  
  The root module of the application. It imports and configures other modules and defines the components (controllers and services) available to the app.
  ```typescript
  import { Module } from '@nestjs/common';
  import { AppController } from './app.controller';
  import { AppService } from './app.service';

  @Module({
    imports: [],
    controllers: [AppController],
    providers: [AppService],
  })
  export class AppModule {}
  ```
  **Explanation**:  
  - `@Module()`: The decorator that marks this class as a module.
  - `controllers`: Declares the controller that handles incoming requests.
  - `providers`: Lists services available to the application (in this case, `AppService`).

- **app.service.ts**:  
  The service contains the business logic of the application. The controller can call methods from the service to get data or perform tasks.
  ```typescript
  import { Injectable } from '@nestjs/common';

  @Injectable()
  export class AppService {
    getHello(): string {
      return 'Hello World!';
    }
  }
  ```
  **Explanation**:  
  - `@Injectable()`: Marks this class as a service, which can be injected into other components (like controllers).
  - The service provides a method `getHello()` that returns a simple string. The controller uses this method to respond to the GET request.

- **main.ts**:  
  The entry point of the application. It bootstraps the NestJS app and starts listening on the specified port (default: 3000).
  ```typescript
  import { NestFactory } from '@nestjs/core';
  import { AppModule } from './app.module';

  async function bootstrap() {
    const app = await NestFactory.create(AppModule);
    await app.listen(3000);
  }
  bootstrap();
  ```
  **Explanation**:  
  - `NestFactory.create()`: Creates an instance of the NestJS application using the root module (`AppModule`).
  - `app.listen(3000)`: The application listens on port 3000 for incoming requests.

---

### Running the Application
To run the NestJS application, you can use the following commands:

- **Start the App**:  
  Use this command to run the application in production mode (without automatic reload).
  ```bash
  npm run start
  ```
  **Explanation**:  
  This command compiles the project and runs it using Node.js.

- **Development Mode (auto reload)**:  
  This command runs the app in development mode, automatically restarting it when files change (thanks to `ts-node-dev`).
  ```bash
  npm run start:dev
  ```
  **Explanation**:  
  This is the most useful command during development, as it speeds up the process by auto-reloading on code changes.

---

### Linting & Formatting
Linting and formatting help ensure code quality and consistency.

- **Lint and Autofix with ESLint**:  
  Run the following command to check your code for errors and automatically fix them where possible:
  ```bash
  npm run lint
  ```
  **Explanation**:  
  ESLint checks for potential issues in your TypeScript and JavaScript code, ensuring code quality.

- **Format Code with Prettier**:  
  Use this command to format your code to match the project's styling guidelines:
  ```bash
  npm run format
  ```
  **Explanation**:  
  Prettier formats your code to keep it clean and consistent according to predefined rules.

---


### Error Handling
By default, NestJS exits the process if it encounters an error during app creation with an exit code of `1`. You can modify this behavior to avoid process termination and handle errors more gracefully:
```typescript
const app = await NestFactory.create(AppModule, { abortOnError: false });
```
**Explanation**:  
- Setting `abortOnError: false` prevents the application from exiting when an error occurs during startup.

---

### Extra Tips

- **Change Port**:  
  You can modify the port the application listens on by updating the `main.ts` file:
  ```typescript
  await app.listen(4000);
  ```
  **Explanation**:  
  Changing the port in `listen()` allows you to run the application on a different port, like 4000 instead of the default 3000.

- **View App in Browser**:  
  Once the app is running, you can access it in your browser by going to `http://localhost:3000/`. If everything is set up correctly, you should see the "Hello World!" message returned by the `getHello()` method in `AppController`.
```











## **Controllers in NestJS**

Controllers handle incoming requests and send responses back to the client. Each controller is tied to specific routes, and can perform different actions.

### **Creating a Basic Controller**
Controllers are defined using **classes** and **decorators**. The `@Controller()` decorator is used to set a route prefix and link the controller to incoming requests.

Example:
```typescript
import { Controller, Get } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Get()
  findAll(): string {
    return 'This returns all cats';
  }
}
```
- `@Controller('cats')`: Sets the route prefix as `/cats`.
- `@Get()`: Handles GET requests to `/cats`.

---

### **Handling Different HTTP Methods**
Nest provides decorators for different HTTP methods, like `@Post()`, `@Put()`, etc.

Example:
```typescript
import { Controller, Post, Get } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Post()
  create(): string {
    return 'This adds a new cat';
  }

  @Get()
  findAll(): string {
    return 'This returns all cats';
  }
}
```
- `@Post()`: Handles POST requests (e.g., adding a new item).

---

### **Request and Response Objects**
You can access the full request object (e.g., headers, query params) using `@Req()` and handle custom responses with `@Res()`.

Example:
```typescript
import { Controller, Get, Req } from '@nestjs/common';
import { Request } from 'express';

@Controller('cats')
export class CatsController {
  @Get()
  findAll(@Req() request: Request): string {
    console.log(request.headers);
    return 'Request received';
  }
}
```
- `@Req()`: Accesses the request object.

---

### **Route Parameters**
You can accept dynamic data in routes, like an ID, using `@Param()`.

Example:
```typescript
import { Controller, Get, Param } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Get(':id')
  findOne(@Param('id') id: string): string {
    return `This returns cat with id ${id}`;
  }
}
```
- `@Param('id')`: Accesses the dynamic `id` from the URL.

---

### **Handling Request Payloads (DTOs)**
To handle data sent in POST requests, you use `@Body()` and Data Transfer Objects (DTOs).

Example:
```typescript
import { Controller, Post, Body } from '@nestjs/common';

class CreateCatDto {
  name: string;
  age: number;
}

@Controller('cats')
export class CatsController {
  @Post()
  create(@Body() createCatDto: CreateCatDto): string {
    return `Cat created: ${createCatDto.name}`;
  }
}
```
- `@Body()`: Gets the request body data.

---

### **Response Status Codes**
The default status code for GET requests is 200, and for POST, it's 201. You can change this using the `@HttpCode()` decorator.

Example:
```typescript
import { Controller, Post, HttpCode } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Post()
  @HttpCode(204)
  create(): string {
    return 'Cat created';
  }
}
```
- `@HttpCode(204)`: Changes the response status code to 204.

---

### **Headers and Redirection**
You can set custom headers with `@Header()` and redirect responses with `@Redirect()`.

Example for headers:
```typescript
import { Controller, Post, Header } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Post()
  @Header('Cache-Control', 'none')
  create(): string {
    return 'This adds a new cat';
  }
}
```

Example for redirection:
```typescript
import { Controller, Get, Redirect } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Get()
  @Redirect('https://nestjs.com', 301)
  redirectToDocs() {}
}
```

---

### **Wildcard Routes**
You can use wildcards like `*` to match dynamic routes.

Example:
```typescript
import { Controller, Get } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Get('ab*cd')
  findAll() {
    return 'This route uses a wildcard';
  }
}
```
- Matches `/abcd`, `/ab123cd`, etc.

---

### **Async Operations**
If your data handling is asynchronous, you can use `async`/`await` or observables.

Example with `async`:
```typescript
import { Controller, Get } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Get()
  async findAll(): Promise<any[]> {
    return [];
  }
}
```

---

### **Adding Controllers to a Module**
Controllers need to be added to a module. Here’s how you add the `CatsController` to the app module:

```typescript
import { Module } from '@nestjs/common';
import { CatsController } from './cats/cats.controller';

@Module({
  controllers: [CatsController],
})
export class AppModule {}
```

---

### **Using @Res() for Custom Responses**
You can use `@Res()` to control the response fully, but it's less recommended unless needed.

Example:
```typescript
import { Controller, Get, Res, HttpStatus } from '@nestjs/common';
import { Response } from 'express';

@Controller('cats')
export class CatsController {
  @Get()
  findAll(@Res() res: Response) {
    res.status(HttpStatus.OK).json([]);
  }
}
```

---







---

# NestJS Providers

## Overview
**Providers** are essential in NestJS, allowing different classes (like services, repositories, factories, etc.) to be injected as dependencies. This enables better organization of code and helps manage relationships between objects.

## 1. What are Providers?
Providers are classes that can be managed by Nest’s **Inversion of Control (IoC)** container. They allow the creation of instances and management of dependencies automatically.

### Key Points
- Providers can be services, factories, or other helpers.
- They help in wiring up dependencies easily.
- They are declared in a module for management.

## 2. Creating a Service
Let’s create a simple service called `CatsService` that handles storing and retrieving cat data.

### Code Example: `cats.service.ts`
```typescript
import { Injectable } from '@nestjs/common';
import { Cat } from './interfaces/cat.interface';

@Injectable() // Marks this class as a provider
export class CatsService {
  private readonly cats: Cat[] = []; // Array to store cat data

  // Method to add a new cat
  create(cat: Cat) {
    this.cats.push(cat); // Adds cat to the array
  }

  // Method to get all cats
  findAll(): Cat[] {
    return this.cats; // Returns the array of cats
  }
}
```

### Cat Interface
This interface defines the structure of a cat object.
```typescript
// interfaces/cat.interface.ts
export interface Cat {
  name: string; // Cat's name
  age: number;  // Cat's age
  breed: string; // Cat's breed
}
```

## 3. Using the Service in a Controller
Now, we will use `CatsService` in a controller to handle HTTP requests.

### Code Example: `cats.controller.ts`
```typescript
import { Controller, Get, Post, Body } from '@nestjs/common';
import { CreateCatDto } from './dto/create-cat.dto'; // DTO for creating cats
import { CatsService } from './cats.service';
import { Cat } from './interfaces/cat.interface';

@Controller('cats') // Route for cat-related requests
export class CatsController {
  constructor(private catsService: CatsService) {} // Injecting CatsService

  // Endpoint to create a new cat
  @Post()
  async create(@Body() createCatDto: CreateCatDto) {
    this.catsService.create(createCatDto); // Calls service to create cat
  }

  // Endpoint to get all cats
  @Get()
  async findAll(): Promise<Cat[]> {
    return this.catsService.findAll(); // Calls service to get all cats
  }
}
```

### Explanation
- The `CatsService` is injected into the `CatsController` via the constructor.
- Using the `private` keyword allows for easy declaration and initialization of the `catsService`.

## 4. Dependency Injection
NestJS uses **Dependency Injection (DI)** to manage how classes receive their dependencies, simplifying code organization.

### Example
```typescript
constructor(private catsService: CatsService) {} // Automatically injects CatsService
```

## 5. Provider Scopes
Providers have a lifecycle that matches the application’s runtime. They are created when the application starts and destroyed when it stops. You can also create request-scoped providers.

## 6. Custom Providers
Nest allows defining providers in various ways, including using plain values, classes, and factories (both synchronous and asynchronous). This flexibility is made possible by the IoC container.

## 7. Optional Providers
Sometimes, you may want to make a dependency optional. Use the `@Optional()` decorator to indicate that a provider may not always be resolved.

### Code Example
```typescript
import { Injectable, Optional, Inject } from '@nestjs/common';

@Injectable()
export class HttpService<T> {
  constructor(@Optional() @Inject('HTTP_OPTIONS') private httpClient: T) {} // HTTP_OPTIONS can be optional
}
```

## 8. Property-Based Injection
You can use property-based injection with the `@Inject()` decorator if needed. However, prefer constructor-based injection for better visibility of dependencies.

### Code Example
```typescript
import { Injectable, Inject } from '@nestjs/common';

@Injectable()
export class HttpService<T> {
  @Inject('HTTP_OPTIONS') // Injecting HTTP_OPTIONS at the property level
  private readonly httpClient: T;
}
```

## 9. Provider Registration
To use a provider, you must register it in a module.

### Code Example: `app.module.ts`
```typescript
import { Module } from '@nestjs/common';
import { CatsController } from './cats/cats.controller';
import { CatsService } from './cats/cats.service';

@Module({
  controllers: [CatsController], // Register the controller
  providers: [CatsService], // Register the service
})
export class AppModule {}
```

## 10. Manual Instantiation
In some cases, you may need to manually instantiate providers. You can retrieve existing instances or instantiate providers dynamically using module references.

---








---

# NestJS Modules

## 1. What is a Module?
A **Module** in NestJS is a class marked by the `@Module()` decorator. Modules help organize code into **logical units**, making your application easier to manage and scale.

### Key Points:
- Every application has at least one **root module**.
- Modules are used to group providers, controllers, and other components.
- You can create feature modules to encapsulate related functionality.

### Example:
```typescript
import { Module } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Module({
  controllers: [CatsController], // Controllers in this module
  providers: [CatsService],      // Services in this module
})
export class CatsModule {}        // The module definition
```

## 2. Root Module
The **root module** is the starting point of the application. You can import other modules into the root module.

### Example:
```typescript
import { Module } from '@nestjs/common';
import { CatsModule } from './cats/cats.module';

@Module({
  imports: [CatsModule], // Importing the CatsModule
})
export class AppModule {}  // Root module
```

## 3. Feature Modules
A **Feature Module** groups related components (like controllers and services). This helps organize code into smaller, manageable sections.

### Example:
```typescript
import { Module } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Module({
  controllers: [CatsController],
  providers: [CatsService],
})
export class CatsModule {} // Feature module for cats
```

## 4. Shared Modules
Modules are **singletons** by default, meaning their instances are shared across the application. You can share services between modules by exporting them.

### Example:
```typescript
import { Module } from '@nestjs/common';
import { CatsService } from './cats.service';

@Module({
  providers: [CatsService],
  exports: [CatsService],  // Export CatsService to share it
})
export class CatsModule {}
```



## 5. Global Modules
A **Global Module** makes its providers available globally without needing to import the module in every other module.

### Example:
```typescript
import { Module, Global } from '@nestjs/common';
import { CatsService } from './cats.service';

@Global()
@Module({
  providers: [CatsService],
  exports: [CatsService], // CatsService is available globally
})
export class CatsModule {}
```



---







---

# NestJS Middleware

## 1. What is Middleware?
Middleware is a function that runs **before** a route handler. It has access to the **request**, **response** objects, and a `next()` function that passes control to the next middleware in line.

### What Middleware Can Do:
- Execute any code.
- Modify the request or response objects.
- End the request-response cycle.
- Call `next()` to pass control to the next middleware.

---

## 2. Creating Middleware
There are two ways to create middleware:
- **Class-based Middleware** (with Dependency Injection support)
- **Functional Middleware** (simpler when no dependencies are needed)

### Class-based Middleware Example:
```typescript
import { Injectable, NestMiddleware } from '@nestjs/common';
import { Request, Response, NextFunction } from 'express';

@Injectable()
export class LoggerMiddleware implements NestMiddleware {
  use(req: Request, res: Response, next: NextFunction) {
    console.log('Request...');
    next(); // Pass control to the next middleware
  }
}
```

---

## 3. Applying Middleware
Middleware is applied using the `configure()` method in the **module** class. The module class must implement the `NestModule` interface.

### Example of Applying Middleware:
```typescript
import { Module, NestModule, MiddlewareConsumer } from '@nestjs/common';
import { LoggerMiddleware } from './common/middleware/logger.middleware';
import { CatsModule } from './cats/cats.module';

@Module({
  imports: [CatsModule],
})
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(LoggerMiddleware) // Apply middleware
      .forRoutes('cats');       // Apply to /cats route
  }
}
```

---

## 4. Restricting Middleware to Specific Methods
You can restrict middleware to specific HTTP methods (e.g., GET, POST) using `RequestMethod`.

### Example:
```typescript
import { Module, NestModule, MiddlewareConsumer, RequestMethod } from '@nestjs/common';
import { LoggerMiddleware } from './common/middleware/logger.middleware';

@Module({})
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(LoggerMiddleware)
      .forRoutes({ path: 'cats', method: RequestMethod.GET }); // Apply only to GET /cats
  }
}
```

---

## 5. Functional Middleware
Functional middleware is simpler and defined as a plain function. It's useful when you don’t need dependency injection.

### Example:
```typescript
import { Request, Response, NextFunction } from 'express';

export function logger(req: Request, res: Response, next: NextFunction) {
  console.log('Request...');
  next();
}

// Applying functional middleware
consumer.apply(logger).forRoutes('cats');
```

---

## 6. Middleware for Multiple Routes or Controllers
You can apply middleware to multiple routes, controllers, or methods.

### Example:
```typescript
consumer.apply(LoggerMiddleware).forRoutes(CatsController); // Apply to all routes in CatsController
```

---

## 7. Excluding Routes from Middleware
You can exclude certain routes from middleware using the `exclude()` method.

### Example:
```typescript
consumer
  .apply(LoggerMiddleware)
  .exclude(
    { path: 'cats', method: RequestMethod.GET }, // Exclude GET /cats
    { path: 'cats', method: RequestMethod.POST } // Exclude POST /cats
  )
  .forRoutes(CatsController);
```

---

## 8. Multiple Middleware
You can apply multiple middleware in sequence using commas.

### Example:
```typescript
consumer.apply(cors(), helmet(), logger).forRoutes(CatsController);
```

---

## 9. Global Middleware
To apply middleware globally to all routes, use the `app.use()` method in your **main.ts** file.

### Example:
```typescript
const app = await NestFactory.create(AppModule);
app.use(logger); // Apply logger to all routes globally
await app.listen(3000);
```

---





# NestJS Exception Filters Summary

### 1. **What are Exception Filters?**
NestJS has a built-in exception handling mechanism that catches unhandled errors and returns a proper response. The default filter handles `HttpException` and its subclasses, but you can create custom filters to handle other exceptions.

### 2. **Default Global Exception Filter**
When an unhandled exception occurs, Nest automatically returns a `500 Internal Server Error` response if it doesn't recognize the exception type.

Example response:
```json
{
  "statusCode": 500,
  "message": "Internal server error"
}
```

### 3. **Throwing Standard Exceptions**
Nest provides built-in exceptions like `HttpException` for handling errors in a REST/GraphQL API.

Example:
```typescript
@Get()
async findAll() {
  throw new HttpException('Forbidden', HttpStatus.FORBIDDEN);
}
```
Response:
```json
{
  "statusCode": 403,
  "message": "Forbidden"
}
```

### 4. **Custom Error Messages**
You can override the default error message and create a custom response body.

Example:
```typescript
@Get()
async findAll() {
  throw new HttpException({
    status: HttpStatus.FORBIDDEN,
    error: 'This is a custom message',
  }, HttpStatus.FORBIDDEN);
}
```
Response:
```json
{
  "status": 403,
  "error": "This is a custom message"
}
```

### 5. **Creating Custom Exceptions**
You can extend the `HttpException` class to create custom exceptions.

Example:
```typescript
export class ForbiddenException extends HttpException {
  constructor() {
    super('Forbidden', HttpStatus.FORBIDDEN);
  }
}

@Get()
async findAll() {
  throw new ForbiddenException();
}
```

### 6. **Built-in HTTP Exceptions**
Nest provides several common HTTP exceptions:
- `BadRequestException`
- `UnauthorizedException`
- `ForbiddenException`
- `NotFoundException`
- `InternalServerErrorException`

Example:
```typescript
throw new BadRequestException('Invalid input');
```

### 7. **Custom Exception Filters**
You can create custom exception filters to handle exceptions in your own way, such as logging or returning a custom response format.

Example of a custom filter that handles `HttpException`:
```typescript
import { ExceptionFilter, Catch, ArgumentsHost, HttpException } from '@nestjs/common';
import { Request, Response } from 'express';

@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter {
  catch(exception: HttpException, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const request = ctx.getRequest<Request>();
    const status = exception.getStatus();

    response
      .status(status)
      .json({
        statusCode: status,
        timestamp: new Date().toISOString(),
        path: request.url,
      });
  }
}
```

### 8. **Binding Filters**
Filters can be applied at different levels:
- **Method-scoped**: Apply to a specific method using the `@UseFilters()` decorator.
- **Controller-scoped**: Apply to an entire controller.
- **Global-scoped**: Apply to the entire application.

Example of method-scoped filter:
```typescript
@Post()
@UseFilters(new HttpExceptionFilter())
async create() {
  throw new ForbiddenException();
}
```

Example of global filter in `main.ts`:
```typescript
async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.useGlobalFilters(new HttpExceptionFilter());
  await app.listen(3000);
}
bootstrap();
```

### 9. **Catching All Exceptions**
To catch all exceptions, leave the `@Catch()` decorator parameter empty.

Example:
```typescript
@Catch()
export class AllExceptionsFilter implements ExceptionFilter {
  catch(exception: unknown, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const status = exception instanceof HttpException
      ? exception.getStatus()
      : HttpStatus.INTERNAL_SERVER_ERROR;

    response
      .status(status)
      .json({
        statusCode: status,
        timestamp: new Date().toISOString(),
        path: request.url,
      });
  }
}
```

### 10. **Extending the Global Exception Filter**
You can extend the built-in `BaseExceptionFilter` to add more functionality.

Example:
```typescript
@Catch()
export class AllExceptionsFilter extends BaseExceptionFilter {
  catch(exception: unknown, host: ArgumentsHost) {
    super.catch(exception, host);
  }
}
```

In `main.ts`:
```typescript
async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  const { httpAdapter } = app.get(HttpAdapterHost);
  app.useGlobalFilters(new AllExceptionsFilter(httpAdapter));
  await app.listen(3000);
}
bootstrap();
```



