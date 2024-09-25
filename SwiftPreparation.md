# Swift Preparation

## Interesting things I stumble across:

**Reference Equality:** 

```swift
===
```

Compares the memory addresses of two class instances. If both references point to the same memory address (i.e., they are references to the same object), `===` returns `true`

**Default initialization of dictionaries:**

```swift
if unique1[c, default: 0] != 0 {
```

**Short notation for looping over someting and adding it to dict:**

```swift
s.forEach { dict1[$0, default: 0] += 1}
```

**Sort dictionary:**

```swift
dict.sorted {$0.1 > $1.1}
```

**Check if already inserted in set:**

```swift
if dupSet.insert(num).inserted == false {
```

**loop over elements and index:**

```swift
for (index, value) in elements.enumerated() {
```

**Trim out whitespaces at beginnign or end**

```swift
let trimmed = input.trimmingCharacters(in: .whitespaces)
```



## Macros

A **macro** in Swift is a powerful tool for metaprogramming, allowing developers to generate code, transform existing code, and perform other compile-time operations. Macros are marked with @ when applied, and they enable developers to automate repetitive tasks, enforce coding standards, and simplify codebases. However, it’s important to distinguish macros from property wrappers, as they serve different purposes within the language.



## Protocols

A **protocol** in Swift is like a blueprint that defines a set of methods, properties, or other requirements that a class, struct, or enum can adopt. Think of it as a contract: any type that conforms to a protocol agrees to implement the requirements defined by that protocol.

**Why Do We Need Protocols?**

- **Consistency**: Protocols ensure that different types provide a consistent interface. For example, if multiple types conform to a Drivable protocol, you know they will all have methods like startEngine().

- **Flexibility**: Protocols allow you to write flexible and reusable code. You can write functions or methods that work with any type that conforms to a protocol, rather than being restricted to a specific class or struct.

- **Decoupling**: Protocols help in decoupling your code. This means you can define behavior without tying it to a specific implementation, making your code more modular and easier to maintain.

**How to Set Up a Protocol in Swift**

```swift
protocol Drivable {
    func startEngine()
    func stopEngine()
}
```

2. **Conform to a Protocol**:

- To make a type conform to a protocol, you declare the protocol after the type’s name, and then implement the required methods or properties.

```swift
class Car: Drivable {
    func startEngine() {
        print("Engine started")
    }

    func stopEngine() {
        print("Engine stopped")
    }
}

struct Bicycle: Drivable {
    func startEngine() {
        print("Pedaling started")
    }

    func stopEngine() {
        print("Pedaling stopped")
    }
}
```

3. **Using Protocols in Code**:

- You can use protocols as types, which allows you to write code that works with any type that conforms to that protocol.

```swift
func testDrive(vehicle: Drivable) {
    vehicle.startEngine()
    vehicle.stopEngine()
}

let myCar = Car()
let myBike = Bicycle()

testDrive(vehicle: myCar) // Output: Engine started, Engine stopped
testDrive(vehicle: myBike) // Output: Pedaling started, Pedaling stopped
```

### **Codable**:

 A type alias for both Encodable and Decodable. When you conform your type to Codable, it automatically conforms to both Encodable and Decodable, allowing it to be encoded into and decoded from external representations like JSON or Plist.

```swift
struct User: Codable {
    var name: String
    var age: Int
}

let user = User(name: "Chirag", age: 25)

// Encoding to JSON
if let jsonData = try? JSONEncoder().encode(user) {
    print(String(data: jsonData, encoding: .utf8)!) // {"name":"Chirag","age":25}
}

// Decoding from JSON
if let jsonData = """
{"name":"Chirag","age":25}
""".data(using: .utf8), let decodedUser = try? JSONDecoder().decode(User.self, from: jsonData) {
    print(decodedUser.name) // Chirag
}
```

### **Hashable**: 

Protocol that allows your type to be used in a hashed collection, such as a Set or as a key in a Dictionary. It requires your type to provide a hash value that can be used to uniquely identify an instance. When you conform to Hashable, you typically also need to conform to Equatable because the hash value is used in combination with equality checks to determine whether two instances are the same.

```swift
struct Person: Hashable {
    var name: String
    var age: Int
}

let person1 = Person(name: "Alice", age: 30)
let person2 = Person(name: "Bob", age: 25)

let peopleSet: Set = [person1, person2] // No duplicates allowed

var peopleDict: [Person: String] = [:]
peopleDict[person1] = "Manager"
print(peopleDict[person1]!) // Manager
```

We can customize the compute of hashing:
```swift
struct Book: Hashable {
    var title: String
    var author: String
    
    func hash(into hasher: inout Hasher) {
        hasher.combine(title)
        hasher.combine(author)
    }
}
```

### **Equatable**: 

Allows you to check whether two instances of a type are considered equal. This is fundamental for comparing objects. Swift automatically synthesizes == if all properties of the type are also Equatable.

```swift
struct Point: Equatable {
    var x: Int
    var y: Int
}

let pointA = Point(x: 2, y: 3)
let pointB = Point(x: 2, y: 3)

if pointA == pointB {
    print("Points are equal") // Points are equal
}
```

We can customize this:
```swift
struct Rectangle: Equatable {
    var width: Int
    var height: Int
    
    static func ==(lhs: Rectangle, rhs: Rectangle) -> Bool {
        return lhs.width == rhs.width && lhs.height == rhs.height
    }
}
```

### **Comparable**: 

Allows your type to be compared using relational operators like <, <=, >, and >=. It requires you to implement the compareTo function.

```swift
struct Score: Comparable {
    var value: Int
    
    static func < (lhs: Score, rhs: Score) -> Bool {
        return lhs.value < rhs.value
    }
}

let score1 = Score(value: 85)
let score2 = Score(value: 92)

if score1 < score2 {
    print("score1 is less than score2") // score1 is less than score2
}
```

### **Identifiable**:

Protocol used mainly in SwiftUI, where each instance has a stable, unique identifier. It is often used when working with collections of data in SwiftUI views.

```swift
struct Task: Identifiable {
    var id = UUID()
    var name: String
}

let tasks = [Task(name: "Buy groceries"), Task(name: "Finish project")]
```

### **CustomStringConvertible**: 

Allows you to provide a custom description for your type, which is useful for debugging or logging.

```swift
struct Person: CustomStringConvertible {
    var name: String
    var age: Int
    
    var description: String {
        return "\(name), age \(age)"
    }
}

let person = Person(name: "Alice", age: 30)
print(person) // Output: Alice, age 30
```

### **Sequence and Collection**: 

Protocols that allow your custom types to behave like standard Swift sequences or collections. If you conform to these, you can iterate over your type, use it in for loops, and take advantage of other collection-related methods.

```swift
struct FibonacciSequence: Sequence, IteratorProtocol {
    var current = 0
    var nextValue = 1
    
    mutating func next() -> Int? {
        let newValue = current
        current = nextValue
        nextValue = newValue + current
        return newValue <= 100 ? newValue : nil
    }
}

let sequence = FibonacciSequence()
for number in sequence {
    print(number) // 0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89
}
```



## Decoding - Containers

There are three types of containers in Swift:

1. **Keyed Containers**: A **keyed container** is used when the data you are decoding is organized as key-value pairs, like a JSON dictionary. You access the values by their keys, and the keys are typically known at compile-time.

   ```swift
   let container = try decoder.container(keyedBy: CodingKeys.self)
   
   {
     "name": "Pizza",
     "price": 9.99
   }
   
   let name = try container.decode(String.self, forKey: .name)
   let price = try container.decode(Double.self, forKey: .price)
   ```

2. **Unkeyed Containers**: An **unkeyed container** is used when the data is an **array**, where the values are accessed by index rather than by key. You don’t need specific keys, but you process the elements in the order they appear.

   ```swift
   let container = try decoder.unkeyedContainer()
   
   ["Pizza", "Pasta", "Salad"]
   
   var container = try decoder.unkeyedContainer()
   while !container.isAtEnd {
       let dish = try container.decode(String.self)
   }
   ```

3. **Single Value Containers**: A **single value container** is used when the data is a **single value** (such as a string, number, or boolean) rather than an array or dictionary.

   ```swift
   let container = try decoder.singleValueContainer()
   
   "Pizza"
   
   let dish = try container.decode(String.self)
   ```

### Dynamic Containers

A **dynamic container** is typically a **keyed container** where the keys are not known at compile time. This happens when the keys are dynamically generated, such as when the JSON structure contains keys with indices, like "strIngredient1", "strIngredient2", etc. You cannot define these keys in advance with a static CodingKeys enum.

```swift
let dynamicContainer = try decoder.container(keyedBy: DynamicCodingKeys.self)

{
  "strIngredient1": "Tomato",
  "strIngredient2": "Cheese",
  "strIngredient3": "Basil"
}

for key in container.allKeys {
    if key.stringValue.starts(with: "strIngredient") {
        let ingredient = try container.decode(String.self, forKey: key)
    }
}
```

### Coding Keys

**CodingKeys** are used in Swift’s **Codable** protocol to map the keys in your data (e.g., JSON keys) to the properties of your Swift types. When you’re working with JSON or other encoded data formats, you often need to define how the keys in the external data map to the properties of your Swift type.

There are two types of Coding Keys in Swift:

1. **Static Coding Keys:** These are typically defined using an enum in a Decodable or Encodable type and are used when the JSON strucutre is known ahead of time.

```swift
enum CodingKeys: String, CodingKey {
  case id = "idMeal"
  case name = "strMeal"
  case instructions = "strInstructions"
}
```

2. **Dynamic Coding Keys:** These are useful when you don’t know the keys ahead of time (e.g., dynamically generated keys) and they are defined in a struct.

```swift
struct DynamicCodingKeys: CodingKey {
    var stringValue: String
    var intValue: Int?

    init?(stringValue: String) {
        self.stringValue = stringValue
    }

    init?(intValue: Int) {
        self.intValue = intValue
        self.stringValue = "\(intValue)"
    }
}
```



## Basics

**Signed Integers** like 

```swift
UInt32 or UInt64
```

store values from zero upwards

**Floating Points:**
takes 32bits

**Double:**
takes 64 bits --> larger precision and better range of values

**Conversion:**

> Float or Doubles are always truncated when converted to Int. 4.74 becomes 4 and -3.9 becomes -3



## Property Wrappers

### @State

**Purpose**: Manages **value-based state** for SwiftUI views. It’s used for **simple, local state** within a view that is owned by the view and resets when the view is recreated.

**Common Use Case**: Storing primitive values such as Int, String, or Bool in SwiftUI views.

```swift
struct CounterView: View {
    @State private var count = 0

    var body: some View {
        VStack {
            Text("Count: \(count)")
            Button("Increment") {
                count += 1
            }
        }
    }
}
```

### @StateObject

**Purpose**: Manages the lifecycle of an **observable reference type** (typically an ObservableObject) in SwiftUI views. It ensures that the object is only created once and remains consistent across view updates.@StateObject property wrapper don’t get destroyed and re-instantiated at times their containing view struct redraws (unlike @ObservedObject).

**Common Use Case**: Managing a view model or any ObservableObject in SwiftUI.

```swift
class ViewModel: ObservableObject {
    @Published var data: String = "Hello"
}

struct ContentView: View {
    @StateObject var viewModel = ViewModel()

    var body: some View {
        Text(viewModel.data)
    }
}
```

### @ObservedObject

**Purpose**: Observes an **ObservableObject** and automatically re-renders the view when a published property inside the object changes. Unlike @StateObject, it doesn’t manage the lifecycle unless it is passed from the outside.

**Common Use Case**: Passing in a view model or other observable objects from a parent view.

```swift
struct ChildView: View {
    @ObservedObject var viewModel: ViewModel

    var body: some View {
        Text(viewModel.data)
    }
}
```

### @EnvironmentObject

**Purpose**: Provides a **shared observable object** to many views in the SwiftUI view hierarchy. The object must be injected into the environment using the .environmentObject() modifier.

**Common Use Case**: Sharing global state (like app-wide settings or user authentication state) across multiple views without explicitly passing it down the view hierarchy.

```swift
class UserSettings: ObservableObject {
    @Published var username: String = "Guest"
}

struct ContentView: View {
    @EnvironmentObject var settings: UserSettings

    var body: some View {
        Text("Username: \(settings.username)")
    }
}

// Inject the environment object at a higher level
let settings = UserSettings()
ContentView().environmentObject(settings)
```

### @Binding

**Purpose**: Creates a **two-way binding** to a value, allowing child views to both read and modify a value stored in a parent view’s @State.

**Common Use Case**: When a child view needs to **modify state** owned by its parent view.

```swift
struct ParentView: View {
    @State private var count = 0

    var body: some View {
        CounterView(count: $count)  // Passes a binding to the child view
    }
}

struct CounterView: View {
    @Binding var count: Int

    var body: some View {
        Button("Increment") {
            count += 1  // Modifies the parent's state
        }
    }
}
```

### @Environment

**Purpose**: Reads a value from the **environment** (a shared state or configuration that can be injected into views) that SwiftUI automatically manages. This is often used for system-provided values such as color schemes, layouts, and presentation modes.

**Common Use Case**: Accessing system settings like color schemes or layout direction.

```swift
struct ContentView: View {
    @Environment(\.colorScheme) var colorScheme

    var body: some View {
        Text("Current color scheme: \(colorScheme == .dark ? "Dark" : "Light")")
    }
}
```

### @Published

Used within ObservableObject to mark properties whos changes should be published to subscribers.

### @AppStorage

**Purpose**: Provides a simple interface to read from and write to **UserDefaults**. It automatically persists values across app launches.

**Common Use Case**: For small pieces of persistent data like user preferences (e.g., theme settings, last visited page).

```swift
struct ContentView: View {
    @AppStorage("username") var username: String = "Guest"

    var body: some View {
        Text("Username: \(username)")
    }
}
```

### @SceneStorage

**Purpose**: Similar to @AppStorage, but the data is **scene-specific**, meaning it persists only for the lifetime of a SwiftUI scene (e.g., when a user moves to another screen and returns).

**Common Use Case**: Storing view-related state that needs to persist across app sessions, but within a specific view or scene.

```swift
struct ContentView: View {
    @SceneStorage("draftText") var draftText: String = ""

    var body: some View {
        TextEditor(text: $draftText)
    }
}
```

### @FocusedBinding (iOS 15+)

**Purpose**: Allows you to create a **binding to a focused value**. This is used in SwiftUI views with focus management, like text fields.

**Common Use Case**: Managing focus between views, especially in complex forms where multiple fields need to be edited.

```swift
struct ContentView: View {
    @FocusState private var isFocused: Bool

    var body: some View {
        TextField("Enter something", text: .constant(""))
            .focused($isFocused)
    }
}
```

### @MainActor

**Purpose**: Ensures that the annotated class, function, or property runs on the **main thread**, which is important for updating the UI. This is commonly used for properties in SwiftUI that need to update the view.

**Common Use Case**: To ensure view model updates or UI-related code is safely run on the main thread.

```swift
@MainActor class ViewModel: ObservableObject {
    @Published var data: String = "Loading..."
}
```

### @GestureState

**Purpose**: Manages the state of a **gesture** in SwiftUI. It helps track transient state during gesture operations, like dragging or pinching.

**Common Use Case**: Handling gestures such as drags, swipes, or taps.

```swift
struct DraggableView: View {
    @GestureState private var dragOffset: CGSize = .zero

    var body: some View {
        Circle()
            .offset(dragOffset)
            .gesture(
                DragGesture()
                    .updating($dragOffset) { value, state, _ in
                        state = value.translation
                    }
            )
    }
}
```



## Access Modifiers

![Screenshot 2024-09-10 at 1.26.16 PM](/Users/jannisgrimm/Library/Application Support/typora-user-images/Screenshot 2024-09-10 at 1.26.16 PM.png)

**Modules:** A **module** is the highest-level unit of code organization in Swift. It defines a group of related code, which can be a framework, a library, or an app.

**Files:** A **file** in Swift represents a single .swift source code file. It contains Swift code that defines types (classes, structs, etc.), functions, extensions, and more.

**Type:** In Swift, a **type** refers to a blueprint that defines the properties and behaviors of an entity. Swift supports a variety of types, including **classes**, **structs**, **enums**, **protocols**, and more. Types are fundamental building blocks used to define objects, data structures, and behaviors in Swift.



## Keywords

### Final: 

**Purpose**: Prevents a class, method, or property from being subclassed or overridden.

**Use case**: Use final when you want to lock a class or method to prevent inheritance or further modifications.

**Behavior**: A class or method marked final cannot be subclassed or overridden.

```swift
final class Animal {   // This class can't be subclassed
    final func makeSound() {  // This method can't be overridden
        print("Roar")
    }
}
```

### Lazy:

**Purpose**: Delays the initialization of a property until it’s accessed for the first time.

**Use case**: Use lazy when initializing a property is resource-intensive, or when the value is not always needed.

**Behavior**: The property is only initialized once when accessed, and its value is stored for future use.

```swift
class DataFetcher {
    lazy var data: String = {    // Initialized only when accessed
        return "Fetching data..."
    }()
}

let fetcher = DataFetcher()
print(fetcher.data)  // Triggers initialization of `data`
```

### Static:

**Purpose**: Defines type-level (class-level) properties or methods.

**Use case**: Use static to create properties or methods that are shared across all instances of a class or struct (no need to instantiate the class to access them).

**Behavior**: static members are accessed directly on the type, not on instances.

```swift
struct Calculator {
    static func add(a: Int, b: Int) -> Int {
        return a + b
    }
}

let result = Calculator.add(a: 3, b: 5)  // Access without an instance
```

### Required:

**Purpose**: Ensures that subclasses must implement the initializer.

**Use case**: Use required when you want all subclasses to implement a specific initializer.

**Behavior**: When an initializer is marked required, every subclass must implement it.

```swift
class Person {
    required init() {  // Must be implemented by subclasses
        print("Person initialized")
    }
}

class Employee: Person {
    required init() {
        super.init()
        print("Employee initialized")
    }
}
```

### Override:

**Purpose**: Indicates that a method or property is being overridden from a superclass.

**Use case**: Use override when you want to provide a custom implementation of a method or property from a superclass.

**Behavior**: Ensures that the method or property you’re overriding exists in the superclass.

```swift
class Animal {
    func makeSound() {
        print("Roar")
    }
}

class Dog: Animal {
    override func makeSound() {  // Overrides the parent implementation
        print("Bark")
    }
}

let dog = Dog()
dog.makeSound()  // Prints "Bark"
```

### Weak

**Purpose**: Used to prevent strong reference cycles in memory management when working with classes (reference types).

**Use Case**: When two objects hold references to each other (strong references), it can lead to a memory leak because neither will be deallocated. By marking one of the references as weak, you break this cycle, allowing proper deallocation.

**Behavior**: A weak reference does not increment the reference count, and it is automatically set to nil when the referenced object is deallocated. This means weak must always be an optional type.

```swift
class Person {
    var name: String
    weak var friend: Person? // Using `weak` to avoid a strong reference cycle
    
    init(name: String) {
        self.name = name
    }
}

var john: Person? = Person(name: "John")
var jane: Person? = Person(name: "Jane")

john?.friend = jane
jane?.friend = john

// Setting john and jane to nil allows them to be deallocated without a memory leak
john = nil
jane = nil
```

### Unowned

**Purpose**: Like weak, unowned is used to avoid strong reference cycles, but it has different behavior.

**Use Case**: You use unowned when the referenced object is expected to always exist during the lifetime of the referring object, meaning you do not want it to be optional.

**Behavior**: Unlike weak, unowned does not automatically set to nil when the referenced object is deallocated. If you try to access an unowned reference after the object it refers to has been deallocated, it will cause a runtime crash.

```swift
class Teacher {
    var name: String
    var student: Student?
    
    init(name: String) {
        self.name = name
    }
}

class Student {
    var name: String
    unowned var teacher: Teacher // We expect `teacher` to always exist
    
    init(name: String, teacher: Teacher) {
        self.name = name
        self.teacher = teacher
    }
}

var mrSmith = Teacher(name: "Mr. Smith")
var john = Student(name: "John", teacher: mrSmith)
```

### Inout

**Purpose**: Allows a function to modify the value of a passed-in variable. Without inout, Swift functions are pass-by-value (the function gets a copy of the variable).

**Use Case**: You use inout when you want a function to modify the original value of a variable.

```swift
func double(_ number: inout Int) {
    number *= 2
}

var value = 10
double(&value) // Use '&' to pass the variable as `inout`
print(value) // 20
```

### Defer

**Purpose**: Ensures that a block of code is executed just before the function returns, regardless of how the function exits (e.g., via normal return or an error).

**Use Case**: Useful for cleanup tasks, like closing a file or releasing resources, especially in the presence of multiple exit points.

```swift
func readFile() {
    print("Start reading file")
    defer {
        print("Close file")
    }
    print("Process file content")
    // defer block will run here before exiting the function
}

readFile()
// Output:
// Start reading file
// Process file content
// Close file
```

### Mutating

**Purpose**: Allows a method in a struct or enum to modify self or its properties.

**Use Case**: In Swift, structs and enums are value types (copied when passed), so methods normally cannot modify their instance variables unless the method is marked as mutating.

```swift
struct Counter {
    var count = 0

    mutating func increment() {
        count += 1
    }
}

var counter = Counter()
counter.increment() // count is now 1
```

### @escaping

**Purpose**: Indicates that a closure is allowed to outlive the function it was passed to. This happens commonly with asynchronous operations.

**Use Case**: When you pass a closure to a function that will run the closure after the function returns (like in network requests or animations).

```swift
func performAsyncTask(completion: @escaping () -> Void) {
    DispatchQueue.global().async {
        // Simulate async work
        completion()
    }
}
```



## Design Patterns

### MVVM (Model-View-ViewModel)

MVVM separates the UI (View) from the business logic (ViewModel), with the Model representing the data. The ViewModel exposes data and commands to the View, and changes in the Model automatically update the View.

**Use Case:**

- Best for **SwiftUI** applications due to its two-way binding capabilities.

- Great for projects that need **separation of concerns** between the UI and business logic.

**Pros:**

- Clear separation of concerns.

- ViewModel is easily testable.

- Works well with **data binding** (e.g., SwiftUI or Combine).

**Cons:**

- Can introduce **boilerplate** code.

- In **UIKit**, it requires manual binding (using closures or delegation), which adds complexity.



### VIPER (View-Interactor-Presenter-Entity-Router)

VIPER breaks down an app’s architecture into five components: View (UI), Interactor (business logic), Presenter (communication between View and Interactor), Entity (data), and Router (navigation).

**Use Case:**

- Ideal for **large-scale** apps where you need **strict separation of concerns** and **testability** is a priority.

- Enterprise apps with complex architecture.

**Pros:**

- **Highly modular** and testable.

- Encourages **separation of responsibilities**.

- Each component is isolated, making it easy to maintain.

**Cons:**

- Can be **overly complex** for simple apps.

- Introduces a lot of **boilerplate** code.

- Communication between components can be difficult to manage.



### MVC (Model-View-Controller)

MVC is a design pattern where the Controller manages the communication between the Model (data/business logic) and the View (UI). The Model handles the data, the View handles the UI, and the Controller coordinates between them.

**Use Case:**

- **Small applications** or **prototypes** where simplicity and quick iteration are important.

- Common in **UIKit** apps.

**Pros:**

- **Simple** to understand and implement.

- **Clear structure** for small applications.

**Cons:**

- Tends to lead to **Massive View Controllers**, where the Controller handles too much responsibility.

- Poor separation of concerns in larger projects.



### Coordinator

The Coordinator pattern centralizes navigation logic into a separate object called the Coordinator. View controllers delegate navigation responsibilities to the Coordinator, making them more focused on the UI.

**Use Case:**

- Great for apps with **complex navigation flows**.

- Suitable when managing multiple view controllers and routes.

**Pros:**

- Centralizes navigation logic, making view controllers **simpler** and more focused.

- **Modular** and **testable** navigation.

**Cons:**

- Can add complexity, especially in small projects.

- **Nested coordinators** can become difficult to manage.



### Clean Architecture

Clean Architecture is a layered approach that separates concerns into **business logic**, **UI**, and **data**. Each layer is isolated and can be independently tested. Data flows in a **circular dependency** pattern, where inner layers don’t depend on outer layers.

**Use Case:**

- Enterprise apps with **high complexity**, where you need clear separation between UI, business logic, and data.

- Ideal for apps that need to be **scalable** and **testable**.

**Pros:**

- Promotes **testability** and **scalability**.

- Highly **modular**.

- Strong **separation of concerns**.

**Cons:**

- Can be **over-engineered** for small or simple apps.

- Adds **initial complexity** and a learning curve.



### Redux

Redux is a **unidirectional data flow** architecture where the **state** is centralized in a single store, and state changes are triggered by actions dispatched by views. Reducers handle these actions and update the store.

**Use Case:**

- Best for apps with **complex state management** where you need **predictability** and **traceability** of state changes.

- Suitable for apps that need a **global state** accessible across many views.

**Pros:**

- **Predictable state** management.

- Centralized store makes **debugging** easier (can track state changes).

- Easy to test.

**Cons:**

- Adds **complexity** for small applications.

- Boilerplate code (action creators, reducers) can get verbose.

- Not idiomatic for iOS (more commonly used in web development).

![Screenshot 2024-09-25 at 11.52.31 AM](/Users/jannisgrimm/Library/Application Support/typora-user-images/Screenshot 2024-09-25 at 11.52.31 AM.png)

**Examples:**

https://github.com/ochococo/Design-Patterns-In-Swift

MVVM: https://medium.com/@abhilash.mathur1891/mvvm-in-ios-swift-aa1448a66fb4

Clean + MMVM: https://tech.olx.com/clean-architecture-and-mvvm-on-ios-c9d167d9f5b3 https://github.com/kudoleh/iOS-Clean-Architecture-MVVM

VIPER: https://medium.com/cr8resume/viper-architecture-for-ios-project-with-simple-demo-example-7a07321dbd29 https://github.com/infinum/iOS-VIPER-Xcode-Templates





## Architecture

1. **Separation of Concerns**:

- **Definition**: This principle means dividing your codebase into distinct sections, each responsible for a single aspect of the application (e.g., UI, business logic, data management).

- **Why it matters**: It ensures that changes in one area don’t ripple through the entire codebase. For example, changing how data is fetched should not affect the UI layer.

2. **Modularity**:

- **Definition**: Breaking down your application into small, reusable pieces or modules.

- **Why it matters**: This improves maintainability, as individual modules can be updated or replaced without affecting the rest of the app. It also encourages **code reuse** and **testability**.

3. **Testability**:

- **Definition**: Structuring your code so that it’s easy to write and execute tests.

- **Why it matters**: Writing automated tests ensures that your app behaves as expected. Architectures like **MVVM** and **VIPER** inherently make code more testable by separating logic from the UI.

4. **Single Responsibility Principle (SRP)**:

- **Definition**: Each class or module should have only one reason to change, meaning each component does one thing well.

- **Why it matters**: Following SRP keeps your code clean, focused, and easier to maintain.

5. **Decoupling**:

- **Definition**: Reducing dependencies between different components or modules in your code.

- **Why it matters**: Decoupled code is easier to refactor and update. Decoupling also enables features like dependency injection, which enhances flexibility and testing.

6. **Layered Architecture**:

- **Definition**: Dividing an app into multiple layers (e.g., UI, business logic, data) that interact with each other in a specific order.

- **Why it matters**: This ensures that each layer has a clear purpose and reduces the interdependencies between different parts of the application.



Programming Principles: https://github.com/ochococo/OOD-Principles-In-Swift

Also look into my apple interview question.



## SwiftData

**Model Container:** provides the persistent backend

- customizable with configurations
- provides schema migration options

Create a model container and specify what model types you want stored (the example below has optional custom configurations):

```swift
// Initialize with only a schema
let container = try ModelContainer(
    for: [Trip.self, LivingAccommodation.self],
    configurations: ModelConfiguration(url: URL("path"))
)

```

Or specify it in a view directly:

```swift
@main
struct TripsApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .modelContainer(
            for: [Trip.self, LivingAccommodation.self]
        )
    }
}
```

**Model Contexts:** Observe all the changes of models and provide many of the actions to operate on them

They are interface for:

- Tracking
- Fetching
- Saving
- Undoing

You generally get the context from the environment when passed in:
```swift
struct ContextView : View {
    @Environment(\.modelContext) private var context
}
```

Outside of view hierachy you can ask the model container to give you a shared main actor bound context, or you can simply instantiate new contexts for a given model container:

```swift
let context = container.mainContext
```



## Value vs Reference Types

### **Struct:** **Value Type** 

(Excel sheet), more performant and lightweight, immutable

```swift
struct SCar {
    var year: Int
    var make: String
    var color: Color
}

var mySCar = SCar(year: 2020, make: "Audi", color: .black)
var stolenSCar = mySCar

mySCar.color = .white

print(stolenSCar.color) //Prints black
```

**Copy on Assignment or Passing**

When you assign a value type to another variable or pass it to a function, it is **copied**, meaning the new variable or function gets its own separate copy of the data.

**Immutability by Default**

Value types are often used when **immutability** is desirable because any change to a value type creates a new instance, leaving the original instance unchanged.

**Thread-Safe**

Since each instance of a value type has its own independent copy of the data, there are **no risks of data races** when working with value types in multi-threaded environments.

**Common Use Case**

Value types are ideal for representing **simple, independent data** like coordinates, dimensions, or other self-contained entities.



### **Class:** **Reference Type**

 (Google sheet): supports inheritance, mutable

```swift
class CCar {
    var year: Int
    var make: String
    var color: Color
    
    //Needs initializer
    init(year: Int, make: String, color: Color) {
        self.year = year
        self.make = make
        self.color = color
    }
}

var myCar = CCar(year: 2022, make: "BMW", color: .black)
var stolenCar = myCar

myCar.color = .white

print(stolenCar.color) //Prints white
```

**Shared Reference on Assignment or Passing**:

When you assign a reference type to a new variable or pass it to a function, the **reference** to the same instance is shared, so changes made to the data through one reference will affect the original instance.

**Mutability**

Reference types can be mutated by any reference pointing to the same instance, making them useful for managing shared, mutable state.

**Not Thread-Safe by Default**

Since multiple references can point to the same instance, **concurrent access** from multiple threads can lead to **data races**. You need to use synchronization techniques (like locks or actors) to make reference types thread-safe in concurrent environments.

**Common Use Case**

Reference types are ideal for managing **shared, mutable state**, such as when working with objects in object-oriented programming or large data structures that you don’t want to copy.

## Generics

**<T: Comparable>**: One letter for the generic type, then what the type will conform to (restraint on type). 

```swift
func determineHigherValue<T: Comparable>(valueOne: T, valueTwo: T) -> T {
    if valueOne > valueTwo {
        return valueOne
    } else {
        return valueTwo
    }
}

determineHigherValue(valueOne: 1 , valueTwo: 3)
```



## Closures

**Closures** are self-contained blocks of functionality that can be passed around and used in code. (Functions that can be passed around).

**Closure Expression Syntax:**

```swift
{ (parameters) -> returnType in
  statements
}
```



```swift
struct Student {
    let name: String
    var testScore: Int
}
    
let students = [Student(name: "Luke", testScore: 88),
                Student (name: "Han", testScore: 73),
                Student (name: "Leia", testScore: 95),
                Student (name: "Chewy", testScore: 78),
                Student (name: "Obi-Wan", testScore: 65),
                Student (name:"Ahsoka", testScore: 86),
                Student (name:"Yoda", testScore: 68)]
```

**Closure:**

```swift
var topStudentsFilter: (Student) -> Bool = { student in
    return student.testScore > 80
}
```

**Function:** (for comparison)

```swift
func topStudentsFilterF(student: Student) -> Bool {
    return student.testScore > 80
}
```

Use closure: (function would work in this case as well)

```swift
let topStudents = students.filter(topStudentsFilter)
```

**Trailing closure syntax:**

When the last argument is a closure it can be omitted and jumped straight to brackets

```swift
let topStudents2 = students.filter { student in
    return student.testScore < 90
}
```

**Shorthand notation:**

```swift
let topStudents3 = students.filter { $0.testScore < 90 }

let studentRanking = topStudents.sorted { $0.testScore > $1.testScore }
```

Use @escaping when the closure needs to outlive the life of the function that called it.

@autoclosures, @escaping



## Filter, Map, Reduce

Different ways of writing simple for loops.

```swift
struct App {
    var name: String
    var cost: Double
    var users: Int
}

var appPortfolio = [App(name: "SeeMe", cost: 14.99, users: 100000),
                    App(name: "Uber", cost: 10, users: 30000000),
                    App(name: "AirBnb", cost: 10, users: 50000000),
                    App(name: "Phone", cost: 0, users: 100000000)]
```

**Filter:** pass in a condition that is true or false

```swift
let freeApps = appPortfolio.filter { $0.cost == 0 }
```

**Map/Transform:**

```swift
let appNames = appPortfolio.map { $0.name }.sorted()
```

**Reduce:** (initialResult, operation)

```swift
var sum = numbers.reduce(0, +)

var totalUsers = appPortfolio.reduce(0, { $0 + $1.users})
```

**Chaining**: 

```swift
var revenue = appPortfolio.map { $0.cost * Double($0.users) }.reduce(0, +)
```

**Compact map**: Removing nils from array

```swift
var nilNumbers = [1, nil, 2, nil, 3, nil, 4]
let nonNilNubers = nilNumbers.compactMap { $0 }
```

**Flat map**: (combining arrays into flat array)

```swift
var arrayOfArray = [[1,2,3], [4,5,6], [7,8,9]]
let singleArray = arrayOfArray.flatMap { $0 }
```



## Sets vs Arrays & Dictionaries

**Array:** ordered, O(n) lookup 

**Set**: unordered, O(1) lookup thru hashing

//Add dictionaries to this

//More info on both

.isinfirst() for arrays for instance and all operations on arrays that are important

**Operations**:

```swift
var swiftDevs: Set = ["Jannis", "Paul", "Sean", "Sameer", "Connor"]
var swiftUIDevs: Set = ["Jannis", "Paul", "Sean", "Sameer"]
var kotlinDevs: Set = ["Ramzi", "Adam", "Hannah"]
var experiencedDevs: Set = ["Paul", "Sean", "Sameer", "Ramzi"]
```

**Intersect:** (pull out overlap)

```swift
var experiencedSwiftUIDevs = swiftDevs.intersection(experiencedDevs)
```

**Subtract:** (pull out difference)

```swift
var jrSwiftDevs = swiftDevs.subtracting(experiencedDevs)
```

**Disjoint:** Check for overlap (returns true if no overlap)

```swift
swiftDevs.isDisjoint(with: kotlinDevs)
```

**Union:** Combine

```swift
swiftDevs.union(kotlinDevs)
```

**Exclusive**: only in one set

```swift
let specialists = swiftDevs.symmetricDifference(kotlinDevs)
```

**Subset**:

```swift
swiftUIDevs.isSubset(of: kotlinDevs)
```

**Superset:**

```swift
swiftDevs.isSuperset(of: swiftUIDevs)
```

**Insert, delete, find**:

```swift
swiftDevs.insert("Bao")
swiftDevs.remove("Bao")
swiftDevs.contains("Jannis")//Bool
```



## Optionals

```swift
struct User {
    var name: String
    var age: Int?
}

let user = User(name: "Jannis", age: 22)
```

**If let**: if user.age != nil -> age = user.age //Used in SwiftUI

```swift
if let age = user.age {
    print("Users age is \(age)")
} else {
    print("No age")
}
```

**Guard let**: a faster way out when it is nil //Used in functions

```swift
func checkAge(age: Int?) {
    guard let age else {
        print("Age is nil")
        return
    }
    if age > 5 {
        print("You are old")
    }
}
```

**Nil coalescing**:

```swift
let age = user.age ?? 0
```

**Force unwrapping:** never do this (95%)

```swift
let unwrappedAge = user.age!
```

**Optional chaining**:

```swift
var optionalUser: User?
var name = optionalUser?.name 
```

Makes name an optional as well since user could be nil.



## Singletons

An instance of a class that can only be created once and is globally accessible throughout your codebase.

Before creating one, ask this: is it vital that there is only a single instance of this class?

**Only choose it if you want uniqueness**

```swift
UserDefaults.standard.set(false, forKey: "isFirstUse")
```

.standard is the actual singleton

```swift
final class MySingleton {
    static let shared = MySingleton() //The actual singlton object
    
    private init() {} //This ensures it can only be initialized from here, thus making it unique
    
    func doSomething() {}
}
```

```swift
MySingleton.shared.doSomething()
```



## Dependency Injection

When you give an object what it needs, rather than having that object create or find it itself.

Interview Question: What is it? And why would you do it?

```swift
class NetworkManager {
    func fetchIngredients() {
        print("Fetching ingredients")
    }
}

class Bag {
    var items = [String]()
    
    func placeOrder() {
        print("Order placed")
    }
}
```

**Without Dependency Injection (everything is created in ViewModel):**

```swift
class BurritioViewModel {
    let networkManager = NetworkManager()
    let bag = Bag()
    
    func fetchItems() {
        networkManager.fetchIngredients()
    }
    func placeOrder() {
        bag.placeOrder()
    }
}
```

**With Dependency Injection**

```swift
class BurritioViewModel1 {
    
    let networkManager: NetworkManager
    let bag: Bag
    
    init(networkManager: NetworkManager, bag: Bag) {
        self.networkManager = networkManager
        self.bag = bag
    }
    
    func fetchItems() {
        networkManager.fetchIngredients()
    }
    func placeOrder() {
        bag.placeOrder()
    }
}
struct MealView {
    let networkManager = NetworkManager()
    let bag = Bag()
    
    //Different in SwiftUI like a navigationlink or button press
    func goToBurritoView() {
        let viewModel = BurritioViewModel1(networkManager: networkManager, bag: bag)
    }

}
```



## REST API's and Network calls

### URL Session

**URLSession** is Apple’s high-level API for **networking** tasks in Swift. It is part of the **Foundation** framework and is designed to handle all types of networking activities, such as **HTTP requests**, **data downloads**, and **file uploads**. It abstracts away much of the complexity involved in making network requests, allowing you to easily send and receive data over the internet or a local network.



NetworkManager class (Hacking with Swift)

Generic network class: https://stackoverflow.com/questions/65576832/how-can-i-make-my-networking-class-generic-in-swift



## Concurrency

use await to yield to the main actor. By using await to make an async call from the main actor, I let other work continue on the main actor while the async work happens

### Actors

### Data Races

### Thread Safety





## Fetch - Project

1. **Separation of Concerns**: Each class and protocol has a single, clear responsibility, making the code easier to manage and understand.

**Session** **Protocol**: Handles the **low-level network communication**. This protocol abstracts URLSession but can be replaced with a mock session (like in your preview).

**Network** **Protocol**: Defines the **high-level API contract** (e.g., fetchMeals, fetchMealDetails) that any network service must follow. This ensures a consistent interface for any class that provides networking, whether it’s a real service or a mock service.

**NetworkProvider** **Class**: Implements the Network protocol, coordinating **URL building, request execution**, and **data decoding**. This is where the actual networking logic lives.

**PreviewNetworkProvider** **Class**: Provides a **mock implementation** of Network for previews and testing, avoiding real network requests and instead using mock data.



2. **Testability**: You can inject mock data and simulate network behavior, making it easy to test different scenarios without real network requests.

3. **Reusability**: Each component can be reused across different parts of your app or even in other projects.

4. **Scalability**: You can easily extend your networking layer to support new APIs or handle new behaviors without breaking existing code.

5. **Flexibility**: The protocol-based design allows you to inject different implementations depending on the environment (production, testing, or preview).

6. **Maintainability**: A modular design makes it easier to update and fix specific parts of the networking layer without affecting other parts.





1. **URLSession** **Overview**:

**URLSession** is Apple’s API for managing network tasks (e.g., data tasks, download tasks, and upload tasks).

•	It operates asynchronously, ensuring network requests don’t block the main thread.

•	It provides methods for sending HTTP requests and handling responses, typically using completion handlers or Swift’s async/await.

2. **Why Use a Custom** **Session** **Protocol**:

•	The **Session** **protocol** abstracts the networking layer, allowing for **flexibility** and **testability**. It lets you replace URLSession with a mock implementation during testing or previews.

•	By making URLSession conform to the Session protocol, you decouple your code from relying directly on URLSession, making it easier to maintain, mock, or swap out in the future.

3. **data(for:delegate:)** **Method**:

•	This is an **async****/****await** method that sends a network request and awaits the response.

•	It simplifies asynchronous networking by making code more readable and reducing the need for callbacks (common with dataTask methods).

•	The method is extended in URLSession to conform to the Session protocol, allowing for seamless integration and usage across real and mock sessions.

4. **Alternatives to Using** **Session** **Protocol**:

•	Direct use of URLSession without abstraction is simpler but tightly couples your networking logic, making it harder to mock or test.

•	Using a protocol allows you to **mock network requests** easily, making unit testing more robust and reliable.





how much of fetch app uses latest SwiftUI features?

Do you still use completion handlers for instance?



Are you looking only for Swift features improving backend efficiency or also frontend design elements like detent utilizing sheets?



What is missing currently that I can bring?





