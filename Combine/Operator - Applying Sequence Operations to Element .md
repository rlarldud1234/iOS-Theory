# Operator - Applying Sequence Operations to Elements

## drop(untilOutputFrom:)

> drop은 두번째 publisher로부터 요소를 받을 때가지 업스트림 publisher를 무시하는 오퍼레이터이다.
> 

```swift
let first = PassthroughSubject<Int, Never>()
let second = PassthroughSubject<String, Never>()
let cancellable = first
    .drop(untilOutputFrom: second)
    .sink {
        print($0)
    }
first.send(0)
first.send(3)
second.send("hello")
first.send(3)
first.send(5)
second.send("e")

// 3 5
```

## dropFirst(_ :)

> 파라미터에 들어간 개수만큼 생략하는 오퍼레이터이다.
> 

```swift
let first = [1, 3, 4, 5, 6, 7, 8, 9, 10]
first.publisher
    .dropFirst(5)
    .sink { print($0) }

// 7 8 9 10
```

## drop(while: )

> while로 들어간 closure가 false를 반환할 때까지 생략하는 오퍼레이터이다.
> 

```swift
let numbers = [-5, -9, 29, 10, 4, 3, 1]
numbers.publisher
    .drop { $0 <= 0 }
    .sink { print($0) }

// 29 10 4 3 1
```

## tryDrop(while: )

> drop에서 생긴 에러를 throw를 할 수 있는 오퍼레이터이다.
> 

```swift
struct ExampleError: Error { }

var numbers = [1, 2, 3, 4, 0, -9, 20]
let cancellable = numbers.publisher
    .tryDrop {
        guard $0 != 0 else { throw ExampleError() }
        return 0 < $0 && $0 < 100
    }.sink(receiveCompletion: {
        print("completion: \($0)")
    }, receiveValue: {
        print($0)
    })

// failure - 0이 들어가서 에러로 ExampleError
```

## append(_: )

> Publisher의 Output의 어떤 요소를 추가하는 오퍼레이터이다.
> 

```swift
let numbers = (0...10)
let cancellable = numbers.publisher
    .append(1, 3, 4, 5)
    .sink { print($0) }

// 0 1 2 3 4 5 6 7 8 9 10 1 3 4 5
```

## prepend(_: )

> append와 반대로 뒤에다 추가하지 않고 publisher Output의 어떤 요소를 앞에 추가하는 오퍼레이터이다.
> 

```swift
let numbers = (0...10)
let cancellable = numbers.publisher
    .prepend(1, 3, 4, 5)
    .sink { print($0) }

// 1 3 4 5 0 ~ 10
```

## prefix(_: )

> maxLength를 받는다. 그 개수만큼 다시 republish하는 오퍼레터이다.
> 

```swift
let numbers = (0...10)
let cancellable = numbers.publisher
    .prefix(3)
    .sink { print($0) }

// 0 1 2 
```

## prefix(while: )

> whilte로 받은 closure가 false가 될 때까지 republish하는 오퍼레이터이다.
> 

```swift
let numbers = (0...10)
let publish = numbers.publisher
    .prefix { return $0 < 5 }
    .sink { print($0) }

// 0 1 2 3 4
```

## tryPrefix(while: )

> prefix에서 생긴 error를 반환할 수 있는 오퍼레이터이다.
> 

```swift
let numbers = [1, 2, 3, 6, 7, 9 , 0, 10, 4, 6]
let publish = numbers.publisher
    .tryPrefix {
        guard $0 != 0 else { throw ExampleError() }
        return $0 < 10
    }.sink(receiveCompletion: {
        print("completion: \($0)")
    }, receiveValue: {
        print($0)
    })

// 1 2 3 6 7 9 ExampleError
```

## prefix(untilOutputFrom: )

> drop과 달리 두 번째 publisher가 요소를 보낼 때까지만 republish한다.
> 

```swift
let first = PassthroughSubject<Int, Never>()
let second = PassthroughSubject<String, Never>()

let cancellable = first
    .prefix(untilOutputFrom: second)
    .sink { print($0) }

first.send(43)
first.send(4)
second.send("gg")
first.send(40)
first.send(19)

// 43 4
```