# Lesson 1 : Protocols 

Protocol
: a set of rules that define how things are done (like a contract). In programming, a protocol defines the properties and methods that an object must have to complete a task.

Computers communicate with each other using protocols like...:
    - **HTTP** > a standard that defines how websites data is communicated between 2 computers.
    - **TCP/IP** > a communication standard that defines how computers find and send data to each other.

## Objectives

    - Learn what protocols are and why they're used 
    - How to adopt and conform to Swift protocols
    - How to print custom information to the console 
    - Check whether instances of custom types are equal to, greater than, or less than each other
    - How to set up custom types so their instances can be saved to and loaded from a file 
    - How to make one type perform work for another using delegation

**When a swift protocol is adopted a promise is made to implement all the methods required by that protocol**

CustomStringConvertible 
: controls custom objects are printed to console

Equatable
: defines how instances of the same type are equal to each other 

Comparable
: defines how instances of the same type are sorted 

Codable 
: encodes the types properties as key/value pairs that can be saved between app launches 

### CustomStringConvertible 

 ~~~ swift
    class Shoe : CustomStringConvertible{
        let color : String 
        let size : Int 
        let hasLaces : Bool 

        init(color:String ,size:Int, hasLaces:Bool){
            self.color = color
            self.size = size 
            self.hasLaces = hasLaces 
        }

        // description variable is required for this protocol to depict how you want the class to print to console / it's structure
        // simple implementation
        var description : String {
            return "Shoe(color:\(color), size:\(size), hasLaces:\(hasLaces))"
        }

        //more dynamic 
        var description: String {
            let doesOrDoesNot = hasLaces ? "does" : "does not"
            return "This shoe is \(color), size \(size), and \(doesOrDoesNot) have laces."
        }
    }

    let myShoe = Shoe(color:"Green", size:8, hasLaces:false)

    // simple implementation
    print(myShoe) //--> Shoe(color: Green, size: 8, hasLaces: false)
    // dynamic implementation
    print(myShoe) //--> This shoe is Green, size 8, does not have laces.
~~~

**session.Current... > returns an instance of the current user**

### Equatable 

requires an implementation for the `==` operator on custom type with a static function that takes a (lhs) left hand side and (rhs) right hand side parameters and returns a bool that says whether the two values are equal

~~~ swift
    // basic implementation without Equatable 
    struct Employee {
        var firstName : String 
        var lastName : String 
        var jobTitle : String 
        var phoneNumber : String 
    }

    let currentEmployee = session.CurrentEmployee
    let selectedEmployee = Employee(firstName:"Michiko", lastName:"Malandro", jobTitle:"software engineer", phoneNumber:"415-555-9293")
    if currentEmployee == selectedEmployee {
        // do something like enable an edit button
    }

    // dynamic implementation with Equatable
    struct Employee : Equatable {
        ...

        static func == (lhs:Employee , rhs:Employee) -> Bool {
            return lhs.firstName == rhs.firstName  && lhs.lastName == rhs.lastName && lhs.jobTitle == rhs.jobTitle && lhs.phoneNumber == rhs.phoneNumber
        }
    }
~~~ 

Autogeneration
: when the method type compares all instance properties 

**The type must be a struct or enum. Classes do not support autogeneration. Each type property must also conform to Equatable.**

~~~ swift
    // to implement autogeneration automatically adopt the protocol without the static function 
    struct Employee: Equatable {
        var firstName : String 
        var lastName : String 
        var jobTitle : String 
        var phoneNumber : String 
    }
~~~

### Comparable 

Objects get sorted using `<, >, <=, >=`

Has 2 requirements : 
    - the type must also **adopt** Equatable 
    - the `<` operator which returns a bool for whether the lhs is less than the rhs

~~~ swift
    struct Employee : Equatable, Comparable {
        ...

        static func < (lhs:Employee, rhs:Employee) -> Bool {
            // sorts strings alphabetically 
            return lhs.lastName < rhs.lastName
        }
    }
~~~

sorted(by:)
: function to an array of sorted types  

~~~ swift
    let employees = [employee1, employee2, employee3, employee4, employee5]
    let sortedEmployees = employees.sorted(by: <)

    for employee in sortedEmployees {
        // prints ordered list of employees by last name 
        print(employee)
    }
~~~

### Codable 

Apps save user input so that the data still exists the next time the user opens the app. To save the data, the values that live in memory must be encoded to a form of data that can be written to a file.

Codable makes it simple to save data by created key/value pairs from objects property names and values that can be used to **Encoder** or **Decoder** object.

Most swift types that use the standard library already conform to Codable.

To conform to Codable custom types Codable has to be added to the type declaration the compiler will *autogenerate* the necessary implementation.

~~~ swift
    struct Employee : Equatable, Comparable, Codable {
        var firstName : String 
        var lastName : String 
        var jobTitle : String 
        var phoneNumber : String 

        static func < (lhs:Employee, rhs:Employee) -> Bool {
            // sorts strings alphabetically 
            return lhs.lastName < rhs.lastName
        }
    }
~~~

This implementation lets an Encoder or Decoder object know that the type has all the information it needs to encode an object to or decode it from a certain data type.

JSONEncoder 
: JSON data (commonly used when working with web services) is a list of key/value pairs that represent info. Can convert an object conforming to codable to JSON. Which can be encoded as Data or displayed as a String showing the list of key/value pairs 

encode(_:)
: method on JSONEncoder that is a special type of swift function that can return specific errors (throwing function).
- if there's no error, the optional will hold the expected value.
- if there's an error the optional will be nil.

Example error message : "Use of unresolved identifier JSONEncoder" --> import **Foundation** the framework in which JSONEncoder is defined.

~~~ swift
    import Foundation 

    let ben = Employee(...)
    let jsonEncoder = JSONEncoder()

    if let jsonData = try? jsonEncoder.encode(ben),
        let jsonString = String(data:jsonData, encoding:utf8) {
            print(jsonString)
        }
    // prints --> {"firstName":"Ben", "lastName":"Stott", "jobTitle":"Front Desk"...}
~~~

## Creating a Protocol 

To implement one use the protocol keyword followed by the custom name you create, define the requirements in a set of curly braces.
When requiring a property it must define whether the property is **read only** or **read/write**.
When requiring a method it needs to specify the name, parameters, and return type of the method.

read only
: can GET the variable but it CAN'T be set.

read/write 
: can GET and SET the value 

~~~ swift
    protocol FullyNamed {
        var fullName : String {get}
        func sayFullName()
    }

    // does not meet protocol 
    struct Person: FullyNamed {
        var firstName: String
        var lastName: String 
    }

    // meets protocol 
    struct Person: FullyNamed {
        var firstName: String
        var lastName: String 

        var fullName : String {
            return "\(firstName) \(lastName)"
        }
        
        func sayFullName() {
            print(fullName) 
        }
    }
~~~

If a protocol is adopted but does not meet it's requirements the code cannot be build or run your code until you address the error.

Protocols and class inheritance are two ways to adopt a shared set of properties and functionality can use a protocol to provide a default implementation just as a class can inherit an implementation from a superclass. 

## Delegation

A design pattern or common practice. Enables a class or structure to hand off , or delegate some of its responsibilities to an instance of another type. 

*Real world example : if you delegate a task you assign it to another person and expect them to handle it > your manager may delegate you to do a presentation*

There are two sides the one who's delegating the task and the one whose job it is to actually do the task.

 - there could be an object that needs a task done, but it might not be the object that actually implements the code to make it happen 
 - types that delegate implementation to other types typically do so by defining a protocol : the protcol defines the responsibilities that can be delegated and the delegate adopts the protocol to execute the actual task. 

 This is important for working with frameworks which have objects that define built in functionality but require you to provide but require you to provide behavior specific to the app.

 **UIKIT** and other IOS framworks use delegation extinsively. 
- they provide objects such as **UIApplication, UITextField, UITableView** use delegation to customize their behavior 

UIApplicationDelegate
: can define how an app responds to remote notifications 

UITextFieldDelegate
: can validate text input 

UITableViewDelegate
: can perform actions when a row is tapped ? 