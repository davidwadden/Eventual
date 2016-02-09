# Eventual

Simple implementation of an async value in swift.

## Inside your services

```swift
func getUserFromInternet() -> Eventual<User> {
  let r = Resolver<Eventual>()
  
  networkClient.get("whatever") { json in
    // do your json -> domain object
    let user: User = userFromJSON(json)
    r.resolve(user)
  }
  
  return r.eventual
}
```

## Consuming your services

```swift
let eventualUser = getUserFromInternet()
eventualUser.finally { user in 
  // now you have a user!
}
```

You can also chain without losing type safety:

```swift
let nameEventual = eventualUser.then { user in user.firstName }
nameEventual.finally { name in print(name) }
```

Or chain with other async operations:

```swift
func getReposForUser(user: User) -> Eventual<[Repo]> { ... }

let eventualRepos = getUserFromInternet().then{ user in getReposForUser(user) }
```

## Convenient functions

`join`:

```swift
join(Eventual("hi"), Eventual(23)).finally { tuple in
  print(tuple.0) // hi
  print(tuple.1) // 23
}
```