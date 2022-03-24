# Typescript Basics
> **Author -** Ishaan Khurana, [LinkedIn](https://www.linkedin.com/in/ishaan-khurana-46968679/)

## Objective
This tutorial covers the fundamentals of typescript - type annotations, type declarations, classes as types, object types, tuple types, intersection types, generic types.

## Kinds of types
### Type Annotations
Type annotations allow a developer to explicitly define type of a variable or an expression. For example, the typescript translation of `const x = 5` would be `const x: number = 5`. Similarly, a developer can also annotate a function's parameters and return type with different types. Let's take a look at the following JS code:
```
const max = (x, y) => {
  return x > y ? x : y
}
```

In order for the code to work as intended, both x and y should be numbers and a boolean should be returned. The typescript equivalant of the above code will be:
```
const max = (x: number, y: number): boolean => {
  return x > y ? x : y
}
```

### Type Declarations
A type declaration, just like the name suggests, allows a developer to declare a type such as an interface, class or a type alias. These declarations allow a developer to reference the types easily without repetition. 

#### Interfaces
Interfaces are particularly helpful when working on API integrations. For example, let's say we have a GET endpoint `/users` that returns us a list of all users. Each user has the following properties - name, email, role, isManager. With javascript, to hit this endpoint, we will write something like:
```
const getUsers = async () => {
  const response = await axios.get(`${BACKEND_URL}/users`)
  const users = response.data
  const SWEs = []
  const analysts = []
  const managers = []
  users.forEach(user => {
    if (user.isManager) {
      managers.push(user)
    } else {
      if (user.role === `SWE`) {
        SWEs.push(user)
      }
      else if (user.role === `Analyst`) {
        analysts.push(user)
      }
    }
  })
  return users
}
```

This code looks clean. However, the lack of types make this code fallible. While working on this code, a developer in the team could misspell the property **isManager** and push their changes, assuming that there are no errors because the compiler won't complain. However, the code will produce downstream errors or glitches since the misspelled property won't exist on a **user** object. With typescript, we can define an interface for the **user** object and specify the type of response we want to retrive from axios. Additionally, we can also do some type annotations. Let's look at the typescript equivalent code:

```
// We define an interface for the response object and annotate each interface property
interface IUser {
  name: string
  email: string
  role: string
  isManager: boolean
}
const getUsers = async (): IUser[] => {
  // We define IUser[] as the generic type while invoking axios.get, this will ensure that the response data is a list of IUser objects
  const response = await axios.get<IUser[]>(`${BACKEND_URL}/users`)
  
  // type annotations here ensure that no other kind of data can be pushed into these arrays 
  const users = response.data
  const SWEs: IUser[] = []
  const analysts: IUser[] = []
  const managers: IUser[] = []
  
  users.forEach(user => {
    // compiler will throw an error if the property isManager is misspelled
    if (user.isManager) {
      managers.push(user)
    } else {
      if (user.role === `SWE`) {
        SWEs.push(user)
      }
      else if (user.role === `Analyst`) {
        analysts.push(user)
      }
    }
  })
  return users
}
```

#### Type aliases

We can also use type aliases to define complex types. For example, let's say we want to define a few variables to hold only strings that have the values "apple", "mango" or "banana". Without using type aliases, our code will get unnecessarily cluttered due to repetition of the same type annotation. For example, 
```
let fruit1: "mango" | "apple" | "banana"
let fruit2: "mango" | "apple" | "banana"
let fruit3: "mango" | "apple" | "banana"
```
To avoid repeating the fairly long type annotation, we can use a type alias to store this annotation. With type alias, the code above would look like:
```
type fruitType = "mango" | "apple" | "banana"
```

**Side note:** We used union types above to define **fruitType**. Union types are used to combine multiple types using bitwise OR operator. We're basically saying that we want our fruits to be strings and hold one of those three values.

### Classes as types
We can also add type annotations on class members such as properties and private fields. For example, 

```
class User {
  name: string
  email: string
  role: string
  isManager: boolean
  
  constructor(name: string, email: string, role: string, isManager: string) {
    this.name = name
    this.email = email
    this.role = role
    this.isManager = isManager
  }
}
```

### Nullable Fields
Let's go back to our example of doing API integration for the endpont GET `/users`. Let's add an additional property to our **IUser** interface - id. In this case, id will be a number. 

```
interface IUser {
  id: number
  name: string
  email: string
  role: string
  isManager: boolean
}
```

Now let's assume that there's two other endpoints POST `/users` and PUT `/users` that allow us to add and edit the user respectively. Let's also assume that the user is assigned an id when the record is inserted into the database. With the interface defined above, the compiler will force us to specify the id of the user even when we're trying to add a new user. To solve this problem, we can make the **id** field of the interface nullable i.e optional. This is accomplished by putting a ? next to the field's name. Objects that are annotated with this type and don't specify an id will have the id as **undefined**. Our updated interface would look like:
```
interface IUser {
  id?: number
  name: string
  email: string
  role: string
  isManager: boolean
}
```

This way, we don't have to specify the id of the user when trying to create a new record. In fact, we can conditionally hit the POST or PUT endpoint depending on whether the id of the user is defined or not. For example,

```
const handleSubmit = async (values: IUser): Promise<void> => {
  values.id ? await axios.put(`${BASE_URL}/users`, values) : await axios.put(`${BASE_URL}/users, values)
}
```

### Object types
Typescript allows a developer to annotate fields of an object. For example,

```
const obj: {name: string, age: number} = {
  name: "Ishaan",
  age: 22
}
```

### Intersection types
An intersection type, as the name suggests, is an intersection of two or more types. For example, if we want a variable to be of type **IEngineer** and also be of type **IAnalyst**, we will write something like:
```
interface IEngineer {
  // some properties
}

interface IAnalyst {
  // some properties
}

const allowOnlyEngineerAnalysts = (employee: IEngineer & IAnalyst) => {
  // DO SOMETHING HERE
}
```
