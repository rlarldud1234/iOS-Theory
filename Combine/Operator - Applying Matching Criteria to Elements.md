# Operator - Applying Matching Criteria to Elements

## contains(_: )

> upstream publisher가 contains 파라미터의 동일한 값이 생성되면 true를 반환한다.
> 

```swift
let numbers = [1, 2, 5, 6, 4]
let publish = numbers.publisher
    .contains(4)
    .sink { print($0) }

// true
```

## contains(where: )

> where 안에 closure를 만족하는 것을 찾으면 true, 아니면 false를 반환한다.
> 

```swift
let numbers = [1, 2, 5, 6, 4]
let publish = numbers.publisher
    .contains { $0 < 3 }
    .sink { print($0) }

// true
```

## tryContains(where: )

> contains closure에서 생길 수 있는 에러를 반환할 수 있는 오퍼레이터이다.
> 

```swift
struct ExampleError: Error { }

let numbers = [1, 2, 0, 5, 6, 4]
numbers.publisher
    .tryContains {
        guard $0 != 0 else { throw ExampleError() }
        return $0 > 5
    }.sink(
        receiveCompletion: {
        print($0)
        }) {
            print($0)
        }

// error 
```

## allSatisfy(_: )

> 모든 요소가 closure에 만족한다면 true 아니면 false를 반환하는 오퍼레이터이다.
> 

```swift
let numbers = [1, 2, 0, 5, 6, 4]
numbers.publisher
    .allSatisfy { $0 > 0 }
    .sink { print($0) }

// false
```

## tryAllSatisfy(_: )

> allSatisfy에서 에러 발생할 시 에러를 반환할 수 있는 오퍼레이터이다.
> 

```swift
struct ExampleError: Error { }

let numbers = [1, 2, 0, 5, 6, 4]
numbers.publisher
    .tryAllSatisfy {
        guard $0 != 0 else { throw ExampleError() }
        return $0 <= 10
    }
    .sink(receiveCompletion: {
        print("completion: \($0)")
    }, receiveValue: {
        print("value: \($0)")
    })

// error 
```