# Operator - Applying Mathematical Operations on Elements

## count

> publisher안의 아이템의 갯수를 세주는 오퍼레이터
> 

```swift
let publisher = (0...10).publisher
		.count()
		.sink { print($0) }

// 11
```

## max

> publisher의 아이템 중 가장 큰 값을 고른다
> 

```swift
let publisher = (0...10).publisher
		.max()
		.sink { print($0) }

// 10
```

## max(by:)

> Swift Standard Library의 max(by:)랑 똑같다.
> 

```swift
enum Rank: Int {
    case ace = 1, two, three, four, five, six, seven, eight, nine, ten, jack, queen, king
}

let cards: [Rank] = [.five, .eight, .king, .ace, .jack]

let publisher = cards.publisher
    .max {
        return $0.rawValue < $1.rawValue
    }
    .sink { print($0) }

// king
```

## reyMax(by:)

```swift
struct ExampleError: Error {}

let publisher = [0, 6, 7, 8].publisher
    .tryMax {
        if( $0 % 2 != 0 ) {
            throw ExampleError()
        }
        return $0 < $1
    }
    .sink(receiveCompletion: {
        print("completion: \($0)")
    }, receiveValue: {
        print("value: \($0)")
    }).cancel()
```

## min

> 가장 작은 값
> 

```swift
let numbers = [1, 2, 3, 4, 5].publisher
    .min()
    .sink(receiveValue: { print($0) })
```

## min(by:)

```swift
enum Rank: Int {
    case ace = 1, two, three, four, five, six, seven, eight, nine, ten, jack, queen, king
}

let cards: [Rank] = [.king, .queen, .eight, .five, .seven]

let cancellable = cards.publisher
    .min(by: {
        return $0.rawValue < $1.rawValue
    }).sink(receiveValue: { print($0) })
```

## tryMin(by:)

```swift
struct ExampleError: Error { }

let publisher = [1, 4, 6, 7, 8, 9].publisher
    .tryMin {
        if $0 % 2 != 0 {
            throw ExampleError()
        }
        
        return $0 < $1
    }
    .replaceError(with: 0)
    .sink(receiveCompletion: {
        print("completion: \($0)")
    }) {
        print($0)
    }
```