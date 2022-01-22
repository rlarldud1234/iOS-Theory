# Operator - Combining Elements from Multiple Publishers

## CombineLatest

> combineLatest는 additional publisher를 구독하고, 두 게시자로부터 output을 수신하면 closure를 실행한다.
> 

```swift
let pub1 = PassthroughSubject<Int, Never>()
let pub2 = PassthroughSubject<Int, Never>()

let cancellable = pub1
    .combineLatest(pub2) {
        return $0 * $1
    }
    .sink { print($0) }

pub1.send(2)
pub2.send(4)
pub2.send(6)
pub1.send(5)

// 8 12 30
```

최대 결합할 수 있는 수는 4개이다.

## merge

> 같은 타입의 다른 publisher를 결합하여 하나의 시퀸스를 반환해주는 오퍼레이터이다.
> 

```swift
let pub1 = PassthroughSubject<Int, Never>()
let pub2 = PassthroughSubject<Int, Never>()

let cancellable = pub1
    .merge(with: pub2)
    .sink { print($0) }

pub1.send(2)
pub2.send(4)
pub2.send(6)
pub1.send(5)

// 2 4 6 5
```

## zip

> 다른 publisher의 요소를 결합하고, 요소 쌍을 tuple로 제공하는 오퍼레이터이다.
* 두 모든 publisher에게 값이 오지 않는다면 값을 방출하지 않는다.
> 

```swift
let pub1 = PassthroughSubject<Int, Never>()
let pub2 = PassthroughSubject<Int, Never>()

let cancellable = pub1
    .zip(pub2)
    .sink { print($0) }

pub1.send(2)
pub2.send(4)
pub2.send(6)
pub1.send(5)

// (2, 4) (5, 6)
```