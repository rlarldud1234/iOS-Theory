# Operator - Selecting Specific Elements

## first / last

> first는 처음의 값, last는 마지막 값을 반환하는 오퍼레이터이다.
> 

```swift
let numbers = [1, 2, 3, 5, 6]
numbers.publisher
    .first()
    .sink { print($0) }

// 1

let numbers = [1, 2, 3, 5, 6]
numbers.publisher
    .last()
    .sink { print($0) }

// 6
```

## first(where: ) / last(where: )

> where closure의 만족하는 첫 번째 / 마지막 요소를 반환하는 오퍼레이터이다.
> 

```swift
let numbers = [1, 2, 3, 5, 6]
numbers.publisher
    .first { $0 > 4 }
    .sink { print($0) }

// 5

let numbers = [1, 2, 3, 5, 6]
numbers.publisher
    .last { $0 > 4 }
    .sink { print($0) }

// 6
```

## tryFirst(where: ) / tryLast(where: )

> first(where: ) 와 last(where: )에서 발생할 수 있는 에러를 반환할 수 있다.
> 

```swift
struct ExampleError: Error { }

let numbers = [1, 2, 3, 0, 5, 6]
numbers.publisher
    .tryFirst {
        guard $0 != 0 else { throw ExampleError() }
        return $0 > 4
    }
    .sink(receiveCompletion: {
        print($0)
    }, receiveValue: {
        print($0)
    })
// failure error

struct ExampleError: Error { }

let numbers = [1, 2, 3, 0, 5, 6]
numbers.publisher
    .tryLast {
        guard $0 != 0 else { throw ExampleError() }
        return $0 > 4
    }
   .sink(receiveCompletion: {
        print($0)
    }, receiveValue: {
        print($0)
    })

// failure error
```

## output(at: )

> index를 파라미터로 받는 오퍼레이터이다. 그 index에 위치하는 요소를 반환한다.
> 

```swift
let numbers = [1, 2, 3, 0, 5, 6]
numbers.publisher
    .output(at: 4)
    .sink { print($0) }

// 5
```

## output(in: )

> range를 파라미터로 받아 range안에 있는 요소들을 반환하는 파라미터이다.
> 

```swift
let numbers = [1, 2, 3, 0, 5, 6]
numbers.publisher
    .output(in: 3...5)
    .sink { print($0) }

// 0 5 6
```