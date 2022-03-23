# Typescript Basics
> **Author -** Ishaan Khurana, [LinkedIn](https://www.linkedin.com/in/ishaan-khurana-46968679/)

## Objective
This tutorial covers the fundamentals of typescript - type annotations, type declarations, classes as types, object types, tuple types, intersection types, generic types

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
A type declaration, just like the name suggests, allows a developer to declare a type such as an interface, class or a type alias. These declarations allow a developer to reference the types easily without repetition. These interfaces are particularly helpful when working on API integrations. For example, let's say we have a GET endpoint `/users` that returns us a list of all users. Each user has the following properties - name, email, role, isManager. With javascript, to hit this endpoint, we will write something like:
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
