
# clean-code-csharp

## Table of Contents

1. [Introduction](#introduction)
2. [Variables](#variables)
3. [Functions](#functions)
4. [Objects and Data Structures](#objects-and-data-structures)
5. [Classes](#classes)
6. [SOLID](#solid)
7. [Testing](#testing)
8. [Concurrency](#concurrency)
9. [Error Handling](#error-handling)
10. [Formatting](#formatting)
11. [Comments](#comments)
12. [Translation](#translation)

## Introduction

Why write bad code?

-   Are you in a rush?
-   Do you try to go "fast"?
-   Do not you have time to do a good job?
-   Are you tired of work in the same program/module?
-   Does your Boss push you to finish soon?

The previous arguments could create a swamp of senseless code.  
If you say “I will back to fix it later”, “Later” usually means “never”.

-   Rushing a code will not make you finish quickly.
-   It will just delay most of the work until testing starts.
-   You will waste more time debugging.
-   You will waste other people’s time testing and re-testing.
-   The code will be fragile: any change in any part could break many another parts.
-   The code will be unmaintainable: understanding the code will be very difficult.
-   The code will not be reusable: you won't be able to save time be re-using code.

## **Variables**

### Use meaningful and pronounceable variable names

**Bad:**

```csharp
var yyyymmdstr = DateTime.Now.ToString("YYYY/MM/DD");
```

**Good:**

```csharp
var currentDate = DateTime.Now.ToString("YYYY/MM/DD");
```

**[⬆ back to top](#table-of-contents)**

### Use the same vocabulary for the same type of variable

**Bad:**

```csharp
getUserInfo();
getClientData();
getCustomerRecord();
```

**Good:**

```csharp
getUser();
getClient();
getCustomer();
```

**[⬆ back to top](#table-of-contents)**

### Avoid magic strings and numbers
-   Leads to repetition.
-   Makes understanding the code harder.
-   Changing a single value will require a lot of searching and replacing.

**Bad:**

```csharp
// What the heck is 6 and 15 for?
// What is not allowed?
if(value < 6 || value > 15) {
	return  "Not allowed";
}
```

**Good:**

```csharp
// 6 and 15 are minimum and maximum ages.
const int MinAge = 6;
const int MaxValue = 15;
const string NotAllowedAgeMessage = "Not allowed";

if(value < MinValue || value > MaxValue) {
	return NotAllowedAgeMessage;
}
```

### Meaningful Names
- We will read more code than we will ever write.
- It's important that the code we do write is readable and searchable. 
- By _not_ naming variables meaningfully, we hurt our readers and future selves.

**Bad:**

```csharp
// What the heck is 86400000 for?
var d = ms % 86400000;
```

**Good:**

```csharp
// Declare them as capitalized named constants.
const int MilliSecondsPerDay = 60 * 60 * 24 * 1000; //86400000;

var days = milliSeconds % MilliSecondsPerDay;
```

**[⬆ back to top](#table-of-contents)**

### Use explanatory variables

**Bad:**

```csharp
var address = "One Infinite Loop, Cupertino 95014";
var cityZipCodeRegex = new Regex("^[^,\\\\]+[,\\\\\\s]+(.+?)\\s*(\\d{5})?$");
saveCityZipCode(
  cityZipCodeRegex.Match(address).Captures[1],
  cityZipCodeRegex.Match(address).Captures[2]
);
```

**Good:**

```csharp
var address = "One Infinite Loop, Cupertino 95014";
var cityZipCodeRegex = new Regex("^[^,\\\\]+[,\\\\\\s]+(.+?)\\s*(\\d{5})?$");

var city = cityZipCodeRegex.Match(address).Captures[1];
var zipCode = cityZipCodeRegex.Match(address).Captures[2];

saveCityZipCode(city, zipCode);
```

**[⬆ back to top](#table-of-contents)**

### Avoid Mental Mapping

Explicit is better than implicit.

**Bad:**

```csharp
var locations = new List<string> {"Austin", "New York", "San Francisco"};
locations.ForEach(l => {
  doStuff();
  doSomeOtherStuff();
  dispatch(l);
});
```

**Good:**

```csharp
var locations = new List<string> {"Austin", "New York", "San Francisco"};
locations.ForEach(location => {
  doStuff();
  doSomeOtherStuff();
  dispatch(location);
});
```

**[⬆ back to top](#table-of-contents)**

### Don't add unneeded context

If your class/object name tells you something, don't repeat that in your variable name.

**Bad:**

```csharp
class Car {
  public string CarMaker { get; set; }
  public string CarAccord { get; set; }
  public string CarColor { get; set; }

  public void PaintCar(string color) {
    CarColor = color;
  }
};
```

**Good:**

```csharp
class Car {
  public string Maker { get; set; }
  public string Accord { get; set; }
  public string Color { get; set; }

  public void Paint(string color) {
    this.Color = color;
  }
};
```

**[⬆ back to top](#table-of-contents)**

## **Functions**

### Arguments

-   A function that needs no argument is the best type of functions.
-   The more the the argument number increase, the harder the function is to understand and maintain.
-   Too many arguments could be caused by a bad architecture and design.
-   Too many arguments could mean that the function has too many responsibilities.  
-   When adding an argument to a function, make sure you’re not breaking OOP principles.

-   Closely related arguments should become a single object, for example:

**Bad:**

```csharp
void Register(string country, string city)
float GetMagnitude(int x, int y)
```

**Good:**

```csharp
void Register(Address address)
float GetMagnitude(Point p)
```

**[⬆ back to top](#table-of-contents)**

### Functions should do one thing

- This is by far the most important rule in software engineering.
- When functions do more than one thing, they are harder to compose, test, and reason about.
- When you isolate a function to a single action, it can be refactored easily and your code will read much cleaner.
- If you take nothing else away from this guide other than this, you'll be ahead of many developers.

**Bad:**

```csharp
// This functions is responsible of iterating over all the clients.
// And should know how to find a client in the database.
// And should know how to format the email to send.
void EmailClients(int[] clientIds)
{
    foreach (var clientId in clientIds)
    {
        var client = database.Find(client => client.Id == clientId && c.IsActive);
        if (client != null)
        {
            var emailBody = "Thank you for the registration. Your reference number is " + client.RefNumber;
            SendEmail(client.Email, App.EmailFrom, emailBody);
        }
    }
}
```

**Good:**

```csharp
// This function only responsible of iterating over all the clients.
void EmailClients(int[] clientIds)
{
    foreach (var clientId in clientIds)
    {
        var client = GetClient(int clientId);
        if (client != null)
        {
            EmailClient(client);
        }
    }
}

// This function is responsible of finding a client in the database.
private Client? GetClient(int clientId)
{
    return database.Find(client => client.Id == clientId && c.IsActive);
}

// This function is responsible of creating the email to send.
private void EmailClient(int clientId)
{
    var emailBody = "Thank you for the registration. Your reference number is " + client.RefNumber;
    SendEmail(client.Email, App.EmailFrom, emailBody);
}
```

**[⬆ back to top](#table-of-contents)**

### Function names should say what they do

**Bad:**

```csharp
void AddToDate(DateTime date, int month) {
  // ...
}

var date = DateTime.Now;

// It's hard to tell from the function name what is added
AddToDate(date, 1);
```

**Good:**

```javascript
void AddMonthToDate(int month, DateTime date) {
  // ...
}

var date = DateTime.Now;
AddMonthToDate(1, date);
```

**[⬆ back to top](#table-of-contents)**

### Functions should only be one level of abstraction

- When you have more than one level of abstraction your function is usually doing too much.
- Splitting up functions leads to reusability and easier testing.

**Bad:**

```csharp
House CreateHouse(int nbWalls, int wallSize) {
	var house = new House();
	house.DigFoundations();
	
	for(int i = 0 ; i < nbWalls ; i++) {
		var wall = new Wall();
		wall.setSize(wallSize);
		wall.Build();
		wall.Paint();
		house.Walls.Add(wall);
	}
	
	return house;
}
```

**Good:**

```csharp
House CreateHouse(int nbWalls, int wallSize) {
	var house = new House();
	house.DigFoundations();
	
	for(int i = 0 ; i < nbWalls ; i++) {
		house.Wall.Add(CreateWall(wallSize));
	}

	return house;
}

Wall CreateWall(int wallSize) {
	var wall = new Wall();
	wall.setSize(wallSize);
	wall.Build();
	wall.Paint();
	return wall;
}
```

**[⬆ back to top](#table-of-contents)**

### Remove duplicate code

- Do your absolute best to avoid duplicate code.
- Duplicate code means that there's more than one place to alter something if you need to change some logic.
- Oftentimes you have duplicate code because you have several slightly different things, that share a lot in common, but their differences force you to have several functions that do mostly the same things.
- Removing duplications means creating abstractions that can do these different things with one function.
- Getting the abstraction right is critical, so it's critical to follow SOLID principles in the _Classes_ section. 
- Bad abstractions can be worse than duplicate code, so be careful! 

**Bad:**

```csharp
class Developer
{
    public float CalculateExpectedSalary() { return 1; } // duplicated
    public int GetExperience() { return 1; } // duplicated
    public string GetGithubLink() { return "github.com"; }
}

class Manager
{
    public float CalculateExpectedSalary() { return 1; } // duplicated
    public int GetExperience() { return 1; } // duplicated
    public string GetPortfolio() { return "linkedin.com"; }
}

void ShowDeveloperList(List<Developer> developers)
{
    developers.ForEach(developer =>
    {
        var expectedSalary = developer.CalculateExpectedSalary(); // duplicated
        var experience = developer.GetExperience(); // duplicated
        var githubLink = developer.GetGithubLink(); // duplicated
        var data = new RenderData {  // duplicated
	        Salary = expectedSalary,  // duplicated
	        Experience = experience,  // duplicated
	        Link = githubLink 
        };
        Render(data); // duplicated
    });
}

void ShowManagerList(List<Manager> managers)
{
    managers.ForEach(manager =>
    {
        var expectedSalary = manager.CalculateExpectedSalary(); // duplicated
        var experience = manager.GetExperience(); // duplicated
        var portfolio = manager.GetMBAProjects(); // duplicated
        var data = new RenderData {  // duplicated
	        Salary = expectedSalary,  // duplicated
	        Experience = experience,  // duplicated
	        Link = portfolio 
        };
        Render(data); // duplicated
    });
}
```

**Good:**

```csharp
// Shared logic is grouped in an abstract class.
abstract class Employee
{
    public float CalculateExpectedSalary() { return 1; }
    public int GetExperience() { return 1; }
    abstract public RenderData GetRenderData();
}

// Specific logic is kept in the concrete implementations.
class Developer : Employee
{
    public string GetGithubLink() { return "github.com"; }
    public override RenderData GetRenderData()
    {
        return new RenderData
        {
            Salary = CalculateExpectedSalary(),
            Experience = GetExperience(),
            Link = GetGithubLink()
        };
    }
}

class Manager : Employee
{
    public string GetPortfolio() { return "linkedin.com"; }
    public override RenderData GetRenderData()
    {
        return new RenderData
        {
            Salary = CalculateExpectedSalary(),
            Experience = GetExperience(),
            Link = GetPortfolio()
        };
    }
}

void ShowEmployeeList(List<Employee> employees)
{
    employees.ForEach(employee =>
    {
        var data = employee.GetRenderData();
        Render(data);
    });
}
```

**[⬆ back to top](#table-of-contents)**



**[⬆ back to top](#table-of-contents)**

### Don't use flags as function parameters

Flags tell your user that this function does more than one thing. Functions should do one thing. Split out your functions if they are following different code paths based on a boolean.

**Bad:**

```csharp
void UploadFile(File f, bool convert)
{
	if(convert) {
		File f2 = Convert(f);
		File.Save(f2);
	} else {
		File.Save(f);
	}
} 

UploadFile(f, false);//what does false stands for?
UploadFile(f, true);//what does true stands for?
```

**Good:**

```csharp
void  UploadFile(File  f)
{
	FileSystem.Save(f);
}

void  ConvertAndUploadFile(File  f)
{
	UploadFile(Convert(f));
}  

UploadFile(f);

ConvertAndUploadFile(f);
```

### Meaningful conditionals

-   ‘If’ statements conditions should contain a single boolean operation.
-   When more is needed, it should be in a separate method or variable.

**Bad:**

```csharp
if (fsm.State === "fetching" && IsEmpty(listNode)) {
  // ...
}
```

**Good:**

```csharp
if (ShouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}

bool ShouldShowSpinner(fsm, listNode) {
  return fsm.State === "fetching" && IsEmpty(listNode);
}
```

**[⬆ back to top](#table-of-contents)**

### Avoid negative conditionals

**Bad:**

```csharp
bool IsDOMNodeNotPresent(node) {
  // ...
}

if (!IsDOMNodeNotPresent(node)) {
  // ...
}
```

**Good:**

```csharp
bool IsDOMNodePresent(node) {
  // ...
}

if (IsDOMNodePresent(node)) {
  // ...
}
```

**[⬆ back to top](#table-of-contents)**

### Avoid conditionals

- Upon first hearing this, most people say, "how am I supposed to do anything without an `if` statement?"
- You can use polymorphism to achieve the same task in many cases.
- The second question is usually, "well that's great but why would I want to do that?"
- The answer is a previous clean code concept we learned: a function should only do one thing.
- When you have classes and functions that have `if` statements, you are telling your user that your function does more than one thing. Remember, just do one thing.

**Bad:**

```csharp
class Airplane {
  // ...
  GetCruisingAltitude() {
    switch (this.type) {
      case "777":
        return this.GetMaxAltitude() - this.GetPassengerCount();
      case "Air Force One":
        return this.GetMaxAltitude();
      case "Cessna":
        return this.GetMaxAltitude() - this.GetFuelExpenditure();
    }
  }
}
```

**Good:**

```csharp
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  GetCruisingAltitude() {
    return this.GetMaxAltitude() - this.GetPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  GetCruisingAltitude() {
    return this.GetMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  GetCruisingAltitude() {
    return this.GetMaxAltitude() - this.GetFuelExpenditure();
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Don't pre-optimize

- Prefer code clarity and cleanliness over presumed performance.
- Obvious optimizations are ok (ex: avoid string concatenation in loops, avoid repeated function calls, etc...)
- Optimizations without measurements are almost always evil.
- For anything other than simple optimizations, it's better 

**[⬆ back to top](#table-of-contents)**

## **Objects and Data Structures**

### Use getters and setters

Using getters and setters to access data on objects could be better than simply
looking for a property on an object. "Why?" you might ask. Well, here's an
unorganized list of reasons why:

- When you want to do more beyond getting an object property, you don't have
  to look up and change every accessor in your codebase.
- Makes adding validation simple when doing a `set`.
- Encapsulates the internal representation.
- Easy to add logging and error handling when getting and setting.
- You can lazy load your object's properties, let's say getting it from a
  server.

**Bad:**

```javascript
function makeBankAccount() {
  // ...

  return {
    balance: 0
    // ...
  };
}

const account = makeBankAccount();
account.balance = 100;
```

**Good:**

```javascript
function makeBankAccount() {
  // this one is private
  let balance = 0;

  // a "getter", made public via the returned object below
  function getBalance() {
    return balance;
  }

  // a "setter", made public via the returned object below
  function setBalance(amount) {
    // ... validate before updating the balance
    balance = amount;
  }

  return {
    // ...
    getBalance,
    setBalance
  };
}

const account = makeBankAccount();
account.setBalance(100);
```

**[⬆ back to top](#table-of-contents)**

### Make objects have private members

This can be accomplished through closures (for ES5 and below).

**Bad:**

```javascript
const Employee = function(name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**Good:**

```javascript
function makeEmployee(name) {
  return {
    getName() {
      return name;
    }
  };
}

const employee = makeEmployee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```

**[⬆ back to top](#table-of-contents)**

## **Classes**

### Prefer ES2015/ES6 classes over ES5 plain functions

It's very difficult to get readable class inheritance, construction, and method
definitions for classical ES5 classes. If you need inheritance (and be aware
that you might not), then prefer ES2015/ES6 classes. However, prefer small functions over
classes until you find yourself needing larger and more complex objects.

**Bad:**

```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error("Instantiate Animal with `new`");
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error("Instantiate Mammal with `new`");
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error("Instantiate Human with `new`");
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

**Good:**

```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() {
    /* ... */
  }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() {
    /* ... */
  }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() {
    /* ... */
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Use method chaining

This pattern is very useful in JavaScript and you see it in many libraries such
as jQuery and Lodash. It allows your code to be expressive, and less verbose.
For that reason, I say, use method chaining and take a look at how clean your code
will be. In your class functions, simply return `this` at the end of every function,
and you can chain further class methods onto it.

**Bad:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car("Ford", "F-150", "red");
car.setColor("pink");
car.save();
```

**Good:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
    // NOTE: Returning this for chaining
    return this;
  }

  setModel(model) {
    this.model = model;
    // NOTE: Returning this for chaining
    return this;
  }

  setColor(color) {
    this.color = color;
    // NOTE: Returning this for chaining
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // NOTE: Returning this for chaining
    return this;
  }
}

const car = new Car("Ford", "F-150", "red").setColor("pink").save();
```

**[⬆ back to top](#table-of-contents)**

### Prefer composition over inheritance

As stated famously in [_Design Patterns_](https://en.wikipedia.org/wiki/Design_Patterns) by the Gang of Four,
you should prefer composition over inheritance where you can. There are lots of
good reasons to use inheritance and lots of good reasons to use composition.
The main point for this maxim is that if your mind instinctively goes for
inheritance, try to think if composition could model your problem better. In some
cases it can.

You might be wondering then, "when should I use inheritance?" It
depends on your problem at hand, but this is a decent list of when inheritance
makes more sense than composition:

1. Your inheritance represents an "is-a" relationship and not a "has-a"
   relationship (Human->Animal vs. User->UserDetails).
2. You can reuse code from the base classes (Humans can move like all animals).
3. You want to make global changes to derived classes by changing a base class.
   (Change the caloric expenditure of all animals when they move).

**Bad:**

```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Bad because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Good:**

```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary);
  }
  // ...
}
```

**[⬆ back to top](#table-of-contents)**

## **SOLID**

### Single Responsibility Principle (SRP)

As stated in Clean Code, "There should never be more than one reason for a class
to change". It's tempting to jam-pack a class with a lot of functionality, like
when you can only take one suitcase on your flight. The issue with this is
that your class won't be conceptually cohesive and it will give it many reasons
to change. Minimizing the amount of times you need to change a class is important.
It's important because if too much functionality is in one class and you modify
a piece of it, it can be difficult to understand how that will affect other
dependent modules in your codebase.

**Bad:**

```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Good:**

```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}

class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Open/Closed Principle (OCP)

As stated by Bertrand Meyer, "software entities (classes, modules, functions,
etc.) should be open for extension, but closed for modification." What does that
mean though? This principle basically states that you should allow users to
add new functionalities without changing existing code.

**Bad:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    if (this.adapter.name === "ajaxAdapter") {
      return makeAjaxCall(url).then(response => {
        // transform response and return
      });
    } else if (this.adapter.name === "nodeAdapter") {
      return makeHttpCall(url).then(response => {
        // transform response and return
      });
    }
  }
}

function makeAjaxCall(url) {
  // request and return promise
}

function makeHttpCall(url) {
  // request and return promise
}
```

**Good:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    return this.adapter.request(url).then(response => {
      // transform response and return
    });
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Liskov Substitution Principle (LSP)

This is a scary term for a very simple concept. It's formally defined as "If S
is a subtype of T, then objects of type T may be replaced with objects of type S
(i.e., objects of type S may substitute objects of type T) without altering any
of the desirable properties of that program (correctness, task performed,
etc.)." That's an even scarier definition.

The best explanation for this is if you have a parent class and a child class,
then the base class and child class can be used interchangeably without getting
incorrect results. This might still be confusing, so let's take a look at the
classic Square-Rectangle example. Mathematically, a square is a rectangle, but
if you model it using the "is-a" relationship via inheritance, you quickly
get into trouble.

**Bad:**

```javascript
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width) {
    this.width = width;
    this.height = width;
  }

  setHeight(height) {
    this.width = height;
    this.height = height;
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach(rectangle => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); // BAD: Returns 25 for Square. Should be 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Good:**

```javascript
class Shape {
  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(length) {
    super();
    this.length = length;
  }

  getArea() {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach(shape => {
    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);
```

**[⬆ back to top](#table-of-contents)**

### Interface Segregation Principle (ISP)

JavaScript doesn't have interfaces so this principle doesn't apply as strictly
as others. However, it's important and relevant even with JavaScript's lack of
type system.

ISP states that "Clients should not be forced to depend upon interfaces that
they do not use." Interfaces are implicit contracts in JavaScript because of
duck typing.

A good example to look at that demonstrates this principle in JavaScript is for
classes that require large settings objects. Not requiring clients to setup
huge amounts of options is beneficial, because most of the time they won't need
all of the settings. Making them optional helps prevent having a
"fat interface".

**Bad:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.settings.animationModule.setup();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  animationModule() {} // Most of the time, we won't need to animate when traversing.
  // ...
});
```

**Good:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.options = settings.options;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.setupOptions();
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  options: {
    animationModule() {}
  }
});
```

**[⬆ back to top](#table-of-contents)**

### Dependency Inversion Principle (DIP)

This principle states two essential things:

1. High-level modules should not depend on low-level modules. Both should
   depend on abstractions.
2. Abstractions should not depend upon details. Details should depend on
   abstractions.

This can be hard to understand at first, but if you've worked with AngularJS,
you've seen an implementation of this principle in the form of Dependency
Injection (DI). While they are not identical concepts, DIP keeps high-level
modules from knowing the details of its low-level modules and setting them up.
It can accomplish this through DI. A huge benefit of this is that it reduces
the coupling between modules. Coupling is a very bad development pattern because
it makes your code hard to refactor.

As stated previously, JavaScript doesn't have interfaces so the abstractions
that are depended upon are implicit contracts. That is to say, the methods
and properties that an object/class exposes to another object/class. In the
example below, the implicit contract is that any Request module for an
`InventoryTracker` will have a `requestItems` method.

**Bad:**

```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // BAD: We have created a dependency on a specific request implementation.
    // We should just have requestItems depend on a request method: `request`
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(["apples", "bananas"]);
inventoryTracker.requestItems();
```

**Good:**

```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ["WS"];
  }

  requestItem(item) {
    // ...
  }
}

// By constructing our dependencies externally and injecting them, we can easily
// substitute our request module for a fancy new one that uses WebSockets.
const inventoryTracker = new InventoryTracker(
  ["apples", "bananas"],
  new InventoryRequesterV2()
);
inventoryTracker.requestItems();
```

**[⬆ back to top](#table-of-contents)**

## **Testing**

Testing is more important than shipping. If you have no tests or an
inadequate amount, then every time you ship code you won't be sure that you
didn't break anything. Deciding on what constitutes an adequate amount is up
to your team, but having 100% coverage (all statements and branches) is how
you achieve very high confidence and developer peace of mind. This means that
in addition to having a great testing framework, you also need to use a
[good coverage tool](https://gotwarlost.github.io/istanbul/).

There's no excuse to not write tests. There are [plenty of good JS test frameworks](https://jstherightway.org/#testing-tools), so find one that your team prefers.
When you find one that works for your team, then aim to always write tests
for every new feature/module you introduce. If your preferred method is
Test Driven Development (TDD), that is great, but the main point is to just
make sure you are reaching your coverage goals before launching any feature,
or refactoring an existing one.

### Single concept per test

**Bad:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles date boundaries", () => {
    let date;

    date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);

    date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);

    date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**Good:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles 30-day months", () => {
    const date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);
  });

  it("handles leap year", () => {
    const date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);
  });

  it("handles non-leap year", () => {
    const date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**[⬆ back to top](#table-of-contents)**

## **Concurrency**

### Use Promises, not callbacks

Callbacks aren't clean, and they cause excessive amounts of nesting. With ES2015/ES6,
Promises are a built-in global type. Use them!

**Bad:**

```javascript
import { get } from "request";
import { writeFile } from "fs";

get(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  (requestErr, response, body) => {
    if (requestErr) {
      console.error(requestErr);
    } else {
      writeFile("article.html", body, writeErr => {
        if (writeErr) {
          console.error(writeErr);
        } else {
          console.log("File written");
        }
      });
    }
  }
);
```

**Good:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**[⬆ back to top](#table-of-contents)**

### Async/Await are even cleaner than Promises

Promises are a very clean alternative to callbacks, but ES2017/ES8 brings async and await
which offer an even cleaner solution. All you need is a function that is prefixed
in an `async` keyword, and then you can write your logic imperatively without
a `then` chain of functions. Use this if you can take advantage of ES2017/ES8 features
today!

**Bad:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**Good:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

async function getCleanCodeArticle() {
  try {
    const body = await get(
      "https://en.wikipedia.org/wiki/Robert_Cecil_Martin"
    );
    await writeFile("article.html", body);
    console.log("File written");
  } catch (err) {
    console.error(err);
  }
}

getCleanCodeArticle()
```

**[⬆ back to top](#table-of-contents)**

## **Error Handling**

Thrown errors are a good thing! They mean the runtime has successfully
identified when something in your program has gone wrong and it's letting
you know by stopping function execution on the current stack, killing the
process (in Node), and notifying you in the console with a stack trace.

### Don't ignore caught errors

Doing nothing with a caught error doesn't give you the ability to ever fix
or react to said error. Logging the error to the console (`console.log`)
isn't much better as often times it can get lost in a sea of things printed
to the console. If you wrap any bit of code in a `try/catch` it means you
think an error may occur there and therefore you should have a plan,
or create a code path, for when it occurs.

**Bad:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Good:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
}
```

### Don't ignore rejected promises

For the same reason you shouldn't ignore caught errors
from `try/catch`.

**Bad:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    console.log(error);
  });
```

**Good:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    // One option (more noisy than console.log):
    console.error(error);
    // Another option:
    notifyUserOfError(error);
    // Another option:
    reportErrorToService(error);
    // OR do all three!
  });
```

**[⬆ back to top](#table-of-contents)**

## **Formatting**

Formatting is subjective. Like many rules herein, there is no hard and fast
rule that you must follow. The main point is DO NOT ARGUE over formatting.
There are [tons of tools](https://standardjs.com/rules.html) to automate this.
Use one! It's a waste of time and money for engineers to argue over formatting.

For things that don't fall under the purview of automatic formatting
(indentation, tabs vs. spaces, double vs. single quotes, etc.) look here
for some guidance.

### Use consistent capitalization

JavaScript is untyped, so capitalization tells you a lot about your variables,
functions, etc. These rules are subjective, so your team can choose whatever
they want. The point is, no matter what you all choose, just be consistent.

**Bad:**

```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const Artists = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Good:**

```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const SONGS = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const ARTISTS = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```

**[⬆ back to top](#table-of-contents)**

### Function callers and callees should be close

If a function calls another, keep those functions vertically close in the source
file. Ideally, keep the caller right above the callee. We tend to read code from
top-to-bottom, like a newspaper. Because of this, make your code read that way.

**Bad:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**Good:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**[⬆ back to top](#table-of-contents)**

## **Comments**

### Only comment things that have business logic complexity.

Comments are an apology, not a requirement. Good code _mostly_ documents itself.

**Bad:**

```javascript
function hashIt(data) {
  // The hash
  let hash = 0;

  // Length of string
  const length = data.length;

  // Loop through every character in data
  for (let i = 0; i < length; i++) {
    // Get character code.
    const char = data.charCodeAt(i);
    // Make the hash
    hash = (hash << 5) - hash + char;
    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**Good:**

```javascript
function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = (hash << 5) - hash + char;

    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Don't leave commented out code in your codebase

Version control exists for a reason. Leave old code in your history.

**Bad:**

```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Good:**

```javascript
doStuff();
```

**[⬆ back to top](#table-of-contents)**

### Don't have journal comments

Remember, use version control! There's no need for dead code, commented code,
and especially journal comments. Use `git log` to get history!

**Bad:**

```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}
```

**Good:**

```javascript
function combine(a, b) {
  return a + b;
}
```

**[⬆ back to top](#table-of-contents)**

### Avoid positional markers

They usually just add noise. Let the functions and variable names along with the
proper indentation and formatting give the visual structure to your code.

**Bad:**

```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: "foo",
  nav: "bar"
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Good:**

```javascript
$scope.model = {
  menu: "foo",
  nav: "bar"
};

const actions = function() {
  // ...
};
```
**[⬆ back to top](#table-of-contents)**
