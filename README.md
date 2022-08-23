
# clean-code-csharp

## Table of Contents

1. [Introduction](#introduction)
2. [Formatting](#formatting)
3. [Comments](#comments)
4. [Variables](#variables)
5. [Functions](#functions)
6. [Classes](#classes)
7. [SOLID](#solid)
8. [Testing](#testing)
9. [Error Handling](#error-handling)

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


## **Formatting**

- Formatting is subjective. Like many rules herein, there is no hard and fast
rule that you must follow.
- The main point is to have a consistent style between all the developers.
- There are tons of tools to automate this.

### Function size

You should not have to scroll horizontally or vertically to read the function.

**Bad:**
```csharp
public async Task RecomputeCPTs(IStainsBillingDataAccess stainsBillingDataAccess, bool fromLoad)
{
	try
	{
		logger.Trace("Start.");
		if ((isAutoBillStainsForBilledCases || !caseBilledChecker.IsCaseBilled()) && isAutoBillStains && !flagValueGetter.GetFlagValue("Disable Auto Billing"))
		{
			//remove all cpts related to stains that are not billed
			Dictionary<string, int> billedCpts = new Dictionary<string, int>();
			var stainsBillingModels = getStainsBillingModels();
			foreach (var billing in stainsBillingModels.SelectMany(s => s.Billings))
			{
				if (!string.IsNullOrWhiteSpace(billing.CPTCode) &&
					await stainsBillingDataAccess.IsCPTRelatedToStain(CPTViewModel.GetCPTOnly(billing.CPTCode), isChangeCPTForRepeatedValues))
				{
					if (!billing.Billed && !(billing.CPTCodeCredits ?? false))
					{
						billing.CPTCode = "";
						billing.Origin = null;
					}
					else if (!(billing.CPTCodeCredits ?? false))
					{
						var Code = CPTViewModel.GetCPTOnly(billing.CPTCode);
						var Multiplier = CPTViewModel.GetMultiplier(billing.CPTCode);
						if (billedCpts.ContainsKey(Code)) billedCpts[Code] = billedCpts[Code] + Multiplier;
						else billedCpts.Add(Code, Multiplier);
					}
				}
			}
			//re-add all related cpts
			List<string> lstHEStains = new List<string>();
			foreach (var subCase in stainsBillingModels)
			{
				bool heStainsExists = false;
				foreach (var specialProcedure in subCase.SpecialProcedures)
				{
					if (!string.IsNullOrWhiteSpace(specialProcedure.LabelDescription))
					{
						if (isHEBillOnceOption)
						{
							if (IsHEStain(specialProcedure.LabelDescription))
							{
								if (heStainsExists) continue;
								else
								{
									heStainsExists = true;
								}
							}
						}
						await AddStainCPT(specialProcedure.LabelDescription, subCase, fromLoad, billedCpts, stainsBillingDataAccess);
					}
				}
			}
		}
		logger.Trace("Success End.");
	}
	catch (Exception exception)
	{
		logger.Error(exception);
		logger.Trace("End, Failed Unkown Error: " + exception.Message);
	}
}
```

### Nested statements
-   A method should only have 1 level of nesting at most.

**Bad**

```csharp
if(condition1) {
    if(condition2) {
        if(condition3) {
            return 'Working properly!';
        } else {
            return 'Third thing broken.';
        }
    } else {
        return 'Second thing broken.';
    }
} else {
    return 'First thing broken.';
}
```

**Good**

```csharp
if(!condition1) {
    return 'First thing broken.';
}
if(!condition2) {
    return 'Second thing broken.';
}
if(!condition3) {
    return 'Third thing broken.';
}

return 'Working properly!';
```

### Use consistent capitalization

- private fields should have camelCase
- everything else should have PascalCase

**Bad:**

```csharp
private int DAYS_IN_WEEK = 7;
private int DaysInMonth = 30;

public string name { get; set; }

void eraseDatabase() {}
void Restore_database() {}

class animal {}
class Alpacawool {}
```

**Good:**

```csharp
private int daysInWeek = 7;
private int daysInMonth = 30;

public string Name { get; set; }

void EraseDatabase() {}
void RestoreDatabase() {}

class Animal {}
class AlpacaWool {}
```

**[⬆ back to top](#table-of-contents)**

### Function callers and callees should be close

- If a function calls another, keep those functions vertically close in the source file. 
- Ideally, keep the caller right above the callee. We tend to read code from top-to-bottom, like a newspaper.

**Bad:**

```csharp
class PerformanceReview {
  public PerformanceReview(Employee employee) {
    this.employee = employee;
  }

  private Employee LookupPeers() {
    return db.Lookup(this.employee, "peers");
  }

  private Employee LookupManager() {
    return db.Lookup(this.employee, "manager");
  }

  private Review[] GetPeerReviews() {
    var peers = this.LookupPeers();
    // ...
  }

  public Review[] PerfReview() {
	return this.GetPeerReviews()
	  .Union(this.GetManagerReview())
	  .Union(this.GetSelfReview())
	  .ToArray();
  }
  
  private Review GetManagerReview() {
    var manager = this.LookupManager();
    // ...
  }

  private Review GetSelfReview() {
    // ...
  }
}
```

**Good:**

```csharp
class PerformanceReview {
  public PerformanceReview(Employee employee) {
    this.employee = employee;
  }

  public Review[] GetPerfReview() {
	return this.GetPeerReviews()
	  .Union(this.GetManagerReview())
	  .Union(this.GetSelfReview())
	  .ToArray();
  }

  private Review[] GetPeerReviews() {
    var peers = this.LookupPeers();
    // ...
  }

  private Employee LookupPeers() {
    return db.Lookup(this.employee, "peers");
  }

  private Review GetManagerReview() {
    var manager = this.LookupManager();
    // ...
  }

  private Employee LookupManager() {
    return db.Lookup(this.employee, "manager");
  }

  private Review GetSelfReview() {
    // ...
  }
}
```

**[⬆ back to top](#table-of-contents)**

## **Comments**

### Only comment things that have business logic complexity.

- Comments are an apology, not a requirement. 
- Good code _mostly_ documents itself.
- Don’t Use a Comment When You Can Use a Function or a Variable.
- Don't document non public code.

**Bad:**

```csharp
// Check to see if the employee is eligible for full benefits
if ((employee.flags & HOURLY_FLAG) && (employee.age > 65))
```

**Good:**

```csharp
if (employee.isEligibleForFullBenefits())
```

**[⬆ back to top](#table-of-contents)**

### Don't leave commented out code in your code

GIT exists for a reason. Leave old code in the history.

### Don't have journal comments

Remember, use GIT!

**Bad:**

```csharp
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
string Combine(string a, string b) {
  return a + b;
}
```

**[⬆ back to top](#table-of-contents)**

## **Variables**

### Use meaningful and pronounceable variable names

- We will read more code than we will ever write.
- It's important that the code we do write is readable and searchable. 
- By _not_ naming variables meaningfully, we hurt our readers and future selves.

**Bad:**

```csharp
var yyyymmdstr = DateTime.Now.ToString("YYYY/MM/DD");
```

**Good:**

```csharp
var currentDate = DateTime.Now.ToString("YYYY/MM/DD");
```

**Another Example**

**Bad**

What does this do?
```csharp
string sec2str(int s) {
    var d = Math.Floor(s / 86400);
    var hs = s % 86400;
    var h = Math.Floor(hs / 3600);
    var ms = hs % 3600;
    var m = Math.Floor(ms / 60);
    var rs = ms % 60;
    var s = Math.Ceil(rs);    
    return $"{d}:{h}:{m}:{s}";
}
```

**Better**

Ok, but how does it work?
```csharp
string SecondsToDateTimeString(int s) {
    var d = Math.Floor(s / 86400);
    var hs = s % 86400;
    var h = Math.Floor(hs / 3600);
    var ms = hs % 3600;
    var m = Math.Floor(ms / 60);
    var rs = ms % 60;
    var s = Math.Ceil(rs);    
    return $"{d}:{h}:{m}:{s}";
}
```

**Even Better**

Starting to understand, but what are d,hs,h,ms,m, ...?
```csharp
const int secondsInAMinute = 60;
const int secondsInAnHour = 60 * secondsInAMinute;
const int secondsInADay = 24 * secondsInAnHour;

string SecondsToDateTimeString(int s) {
    var d = Math.Floor(s / secondsInADay );
    var hs = s % secondsInADay ;
    var h = Math.Floor(hs / secondsInAnHour );
    var ms = hs % secondsInAnHour ;
    var m = Math.Floor(ms / secondsInAMinute );
    var rs = ms % secondsInAMinute ;
    var s = Math.Ceil(rs);    
    return $"{d}:{h}:{m}:{s}";
}
```

**Even more Better**

```csharp
const int secondsInAMinute = 60;
const int secondsInAnHour = 60 * secondsInAMinute;
const int secondsInADay = 24 * secondsInAnHour;

string SecondsToDateTimeString(int inputSeconds) {
    var days = Math.Floor(seconds / secondsInADay);
    var hourSeconds = seconds % secondsInADay;
    var hours = Math.Floor(hourSeconds / secondsInAnHour);
    var minuteSeconds = hourSeconds % secondsInAnHour;
    var minutes = Math.Floor(minuteSeconds / secondsInAMinute);
    var remainingSeconds = minuteSeconds % secondsInAMinute;
    var seconds = Math.Ceil(remainingSeconds);    

    return $"{days}:{hours}:{minutes}:{seconds}";
}
```

**[⬆ back to top](#table-of-contents)**

### Consistency: Use the same vocabulary for the same type of variable

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

### Functions names should be verbs:

-   Functions always do an action.
-   Actions are represented by verbs.    
-   Helps distinguishing between functions and classes.

**Bad:**

```csharp
bool AlphanumericChecker(char c)
string TextParser(string text)
```

**Good:**

```csharp
bool IsAlphanumeric(char c)
string ParseText(string text)
```

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

### Arguments

-   A function that needs no argument is the best type of functions.
-   The more the argument number increase, the harder the function is to understand and maintain.
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
- When you isolate a function to one action, it can be refactored easily and will read much cleaner.
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

### Law of Demeter

- "Each unit should only talk to its friends; don't talk to strangers."
- When you have more than one level of abstraction your function is usually doing too much.
- Splitting up functions leads to reusability and easier testing.

**Bad:**

```csharp
House CreateHouse(int nbWalls, int wallSize) {
	var house = new House();
	house.DigFoundations();
	for(int i = 0 ; i < nbWalls ; i++) {
		var wall = new Wall();
		for(int i = 0 ; i < wall.BrickCount ; i++) {
			// `House` can talk to its friend `Wall` but not the stranger `Brick`.
			wall.Bricks.Add(new Brick(wall.BrickSize));
		}
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
	for(int i = 0 ; i < wall.BrickCount ; i++) {
		wall.Bricks.Add(new Brick(wall.BrickSize));
	}
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
bool IsNodeNotPresent(Node node) {
  // ...
}

if (!IsNodeNotPresent(Node node)) {
  // ...
}
```

**Good:**

```csharp
bool IsNodePresent(Node node) {
  // ...
}

if (IsNodePresent(Node node)) {
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

### Prefer Exceptions to Returning Error Codes

-   When you need an error code, Prefix the method with ‘Try’ to indicate that it can fail without throwing an exception.

**Bad**

```csharp
CommandResult SendCommand(){
    var result = Send(command);
    if(result == ERROR) {
        ShowMessage('An fatal error has occurred!');
  return result;
    }
    if(result == INVALID) {
        ShowMessage('Invalid command');
  return result;
    }
    return result;
}
```

**Good**

```csharp
void SendCommand() {
    try {
        Send(command);
    } catch(ValidationException ex) {
        ShowMessage('Do you want to overwrite?');
    }
}
```

**[⬆ back to top](#table-of-contents)**

### Never catch without throwing

**Bad**

```csharp
try {
	//...
}
catch (Exception ex) {
	this.logger.Log(ex);
}
```

**Good**

```csharp
try {
	//...
}
catch (Exception ex) {
	this.logger.Log(ex);
	throw;
}
```

**[⬆ back to top](#table-of-contents)**

### Never return or pass `Null`

**Bad**

```csharp
Response Do(){
	try {
		Send(null);
	} catch {
		return null;
	}
}
```

**Good**

```csharp
Response Do(){
	try {
		SendEmpty();
	} catch {
		throw;
	}
}
```

**[⬆ back to top](#table-of-contents)**

### Don't pre-optimize

- Prefer code clarity and cleanliness over presumed performance.
- Obvious optimizations are ok (ex: avoid string concatenation in loops, avoid repeated calls, etc...)
- Optimizations without measurements are almost always evil.
- For anything other than simple optimizations, it's better 

**[⬆ back to top](#table-of-contents)**

## **Classes**

### Naming Conventions
- Class Names should be nouns.
- Method Names should be verbs.
- Abstract Classes  Names should end with ‘Base’
- Interface Names should start with ‘I’
- Async Method Names should end with ‘Async’

### Use getters and setters and make all fields private.

"Why?" you might ask. Well, here's why:

- When you want to do more beyond getting an object property, you don't have to look up and change every accessor in your codebase.
- Makes adding validation simple when doing a `set`.
- Encapsulates the internal representation.
- Easy to add logging and error handling when getting and setting.

**Bad:**

```csharp
class BankAccount {
	public int balance;
	// ...
}

var account = new BankAccount();
account.balance = 100; // to log/validate/modify this action we need to find all the occurences in the entire code and modify it.
```

**Good:**

```csharp
class BankAccount {
	private int balance;
	// ...
	public int Balance { get => this.balance; set => this.balance = value; } 
}

var account = new BankAccount();
account.Balance = 100;
```
To add validation as an example:

```csharp
class BankAccount {
	private int balance;
	// ...
	public int Balance { 
		get => this.balance; 
		set {
			if(value < 0) {
				throw new Exception("Value can't be negative");
			}
			
			this.balance = value;
		} 
	} 
}
```

**[⬆ back to top](#table-of-contents)**

### Prefer composition over inheritance

Your inheritance should represents an "is-a" relationship and not a "has-a" relationship (Employee->User vs. User->UserDetails).

**Bad:**

```csharp
class Employee {
  // ...
  
  public Employee(string name, string email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Bad because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData : Employee {
  public EmployeeTaxData (string name, string email, string ssn, string salary) : base(name, email){
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Good:**

```csharp
class EmployeeTaxData {
  // ...
  
  public EmployeeTaxData(string ssn, string salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  // ...
  
  public Employee (string  name, string  email, EmployeeTaxData  taxData ) {
    this.name = name;
    this.email = email;
    this.taxData = taxData ;
  }

  // ...
}
```

**[⬆ back to top](#table-of-contents)**

## **SOLID**

### Single Responsibility Principle (SRP)

- "There should never be more than one reason for a class to change".
- It's tempting to jam-pack a class with a lot of functionality, like when you can only take one suitcase on your flight.
- The issue with this is that your class won't be conceptually cohesive and it will give it many reasons to change.
- Minimizing the amount of times you need to change a class is important.
- It's important because if too much functionality is in one class and you modify a piece of it, it can be difficult to understand how that will affect other dependent modules in your codebase.
- A good indicator for violating this principle is the number of the constructor arguments or the number of public functions that the class have.

**Bad:**

```csharp
class UserSettings {
  // ...
  
  public UserSettings(User user) {
    this.user = user;
  }

  public void ChangeSettings(Settings settings) {
    if (this.VerifyCredentials()) {
      // ...
    }
  }

  bool VerifyCredentials() {
    // ...
  }
}
```

**Good:**

```csharp
class UserAuth {
  public UserAuth(user) {
    this.user = user;
  }

  public bool VerifyCredentials() {
    // ...
  }
}

class UserSettings {
  public UserSettings(user, UserAuth auth) {
    this.user = user;
    this.auth = auth;
  }

  public void ChangeSettings(Settings settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```

**!!Warning!!**
- Don't go overboard while applying this principle.
- Different actions on the same entity should considered a single responsibility most of the time.
- CRUD operations on the same entity belong to the same class usually.

**[⬆ back to top](#table-of-contents)**

### Open/Closed Principle (OCP)

- "Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification." - This principle basically states that you should allow users to add new functionalities without changing existing code.
- A good indicator of violating this principle is when you have a `switch` case or a equivalent `if else` chain.

**Bad:**

```csharp
class HttpRequester {
  // ...
  
  constructor(string adapterType) {
    this.adapterType= adapterType;
  }

  public string Fetch(string url) {
    if (this.adapterType == "ajaxAdapter") {
      return Process(MakeAjaxCall(url));
    } else if (this.adapterType == "nodeAdapter") {
      return Process(MakeHttpCall(url));
    }
  }
}

/// ...
```

**Good:**

```csharp
class AjaxAdapter : Adapter {
  // ...
  
  public AjaxAdapter() {
    this.name = "ajaxAdapter";
  }

  public string Request(url) {
    // request and return promise
  }
}

class NodeAdapter : Adapter {
  // ...
  
  public NodeAdapter () {
    this.name = "nodeAdapter";
  }

  public string Request(url) {
    // request and return promise
  }
}

class HttpRequester {
  // ...
  
  public HttpRequester(Adapter adapter) {
    this.adapter = adapter;
  }

  public string Fetch(string url) {
    return Process(this.adapter.Request(url));
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Liskov Substitution Principle (LSP)

- "If S is a subtype of T, then objects of type T may be replaced with objects of type S without altering any
of the desirable properties of that program"
- If you have a parent class and a child class, then the base class and child class can be used interchangeably without getting incorrect results.

**Bad:**

The classic rectangle and square example.
Mathematically, a square is a rectangle, so you might do this: 
```csharp
class Rectangle {
  public Rectangle() {
    this.width = 0;
    this.height = 0;
  }

  public void SetColor(Color color) {
    // ...
  }

  public void Render(float area) {
    // ...
  }

  public void SetWidth(float width) {
    this.width = width;
  }

  public void SetHeight(float height) {
    this.height = height;
  }

  public float GetArea() {
    return this.width * this.height;
  }
}

class Square : Rectangle {
  public void SetWidth(float width) {
    this.width = width;
    this.height = width;
  }

  public void SetHeight(float height) {
    this.width = height;
    this.height = height;
  }
}

void RenderLargeRectangles(List<Rectangle> rectangles) {
  rectangles.ForEach(rectangle => {
	// flipping the width and height will change the outcome
	// using the rectangle class will require careful handling or it will produce undesirable results.
	// to avoid this, you might attempt to add a type check, but that is fixing the symptoms not the cause.
    rectangle.SetWidth(4); 
    rectangle.SetHeight(5);
    var area = rectangle.GetArea();
    rectangle.Render(area);
  });
}

var rectangles = new List<Rectangle> { new Rectangle(), new Rectangle(), new Square() };
RenderLargeRectangles(rectangles);
```

**Good:**

As opposed to Mathematics, in the OOP world, a square is not a rectangle, because it require a different set of properties to implement it.
```csharp
class Shape {
  public abstract void SetColor(Color color) {
    // ...
  }

  public void Render(float area) {
    // ...
  }
}

class Rectangle : Shape {
  public Rectangle (float width, float height) {
    this.width = width;
    this.height = height;
  }

  public override float GetArea() {
    return this.width * this.height;
  }
}

class Square : Shape {
  public Square(length) {
    this.length = length;
  }

  public override float GetArea() {
    return this.length * this.length;
  }
}

public void RenderLargeShapes(List<Shape> shapes) {
  shapes.ForEach(shape => {
    var area = shape.GetArea();
    shape.Render(area);
  });
}

var shapes = new List<Shape> { new Rectangle(4, 5), new Rectangle(4, 5), new Square(5) };
RenderLargeShapes(shapes);
```

**[⬆ back to top](#table-of-contents)**

### Interface Segregation Principle (ISP)

- "Clients should not be forced to depend upon interfaces that they do not use."
- 

**Bad:**

```csharp
interface IOrderService {
    void OrderBurger(int quantity);
    void OrderFries(int fries);
    void OrderCombo(int quantity, int fries);
}

public class BurgerOrderService : IOrderService
{
    public void OrderBurger(int quantity)
    {
        Console.WriteLine("Received order of " + quantity.ToString() + " burgers");
    }
    public void OrderFries(int fries)
    {
        throw new Exception("No fries in burger only order");
    }
    public void OrderCombo(int quantity, int fries)
    {
        throw new Exception("No combo in burger only order");
    }
}
```

**Good:**

```csharp
interface IOrderBurgerService {
    void OrderBurger(int quantity);
}
interface FriesOrderService {
    void OrderFries(int fries);
}
interface ComboOrderService {
    void OrderCombo(int fries);
}

public class BurgerOrderService : IOrderBurgerService
{
    public void OrderBurger(int quantity)
    {
        Console.WriteLine("Received order of " + quantity.ToString() + " burgers");
    }
}
```

**[⬆ back to top](#table-of-contents)**

### Dependency Inversion Principle (DIP)

This principle states two essential things:
1. High-level modules should not depend on low-level modules. Both should
   depend on abstractions.
2. Abstractions should not depend upon details. Details should depend on
   abstractions.

In friendlier terms:

 1. A class should not create it's own dependencies, they can create DTOs and simple Models though.
 2. It's better to depend upon interfaces and abstract classes then upon classes.
 3. A dependency injection framework or the `Factory` pattern should be used to inject dependencies.

**Bad:**

```csharp
public class Client { 
	private SquareLogic square; 
	
	public Client() { 
		this.square = new SqaureLogic(); 
	} 
	
	//...
}
```

**Good:**

```csharp
public class Client { 
	private ISquare square; 
	
	public Client(ISquare square) { 
		this.square = square; 
	} 
	
	//...
}
```

**[⬆ back to top](#table-of-contents)**

## **Testing**

- Testing is more important than shipping.
- If you don't have good tests, then every time you ship code you won't be sure that you didn't break anything that was working before.
- Deciding on what constitutes an adequate amount is up to the project.
- There's no excuse to not write tests. You might be faster at the beginning, but after a few days worth of work you will start having regression problems which will slow you down the further you go without tests.

### Test Pyramid

![Ideal Automated Testing Pyramid](http://64.227.102.243/wp-content/uploads/2015/10/idealautomatedtestingpyramid.png)

1.  Unit Tests
2.  Integration/API/UI Component Tests
3.  User Interface Tests
4. Manual Testing

The more high-level you get the more permutation you will have to do.
That's why the more high-level you get the fewer tests you should have.
And the more low-level you get the more permutation you need to test.

**[⬆ back to top](#table-of-contents)**
