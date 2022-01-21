# Operator - Filtering Element

## filter

> closure와 일치하는 모든 요소를 다시 publish 한다.
> 

```swift
let number = [1, 2, 3, 4, 5]
let cancellable = number.publihser
			.filter { $0 % 2 == 0 }
			.sink { print($0) }

// 2, 4
```

## tryFilter

> filter의 기능 + error 배출 가능한 closure도 가능
> 

```swift
struct DivisionByZeroError: Error()

let numbers: [Int] = [1, 2, 3, 4, 0, 5]
let cancellable = numbers.pulbihser
			.tryFilter {
					if $0 == 0 {
							throw DivisionByZeroError()
					}, else {
							return $0 % 2 == 0
					}
			}.sink(
					receiveCompletion: { print($0) },
					receiveValue: { print($0) }
				)

// 2 4 (error 문구)
			
```

## compactMap

> nil을 제거하고, nil이 아닌 요소만 다운스트림에 publish한다.
> 

```swift
let numbers = [0...5]
let romanNumberalDict: [Int : String] = 
			[1: "I", 2: "II", 3: "III", 5: "V"]

let cancellable = numbers.publihser
			.compactMap { romanNumberalDict[$0] }
			.sink { print($0) }

// I II III V 
```

## tryCompactMap

> compactMap에 closure에서 에러 방출이 가능한 클로져
> 

```swift
struct ParseError: Error {}

func romanNumberal(from: Int) throws -> String? {
		let romanNumberalDict: [Int : String] = 
				[1 : "I", 2 : "II", 3 : "III", 4 : "IV", 5 : "V"]
		guard from != 0 else { throw ParseError() }
		return remanNumberDict[from]
}

let numbers = [6, 5, 4, 3, 2, 1, 0]
cancellable = numbers.publihser
			.tryCompactMap { try romanNumberal(from: $0) } 
			.sink(
						receiveCompletion: { print($0) },
						receiveValue: { print($0) }
			)

// V IV III II I Error
```

## removeDuplicates

> 이전 요소와 일치하지 않는 것만 publish하는 친구입니다.
> 

```swift
let numbers = [0, 1, 2, 2, 3, 3, 3, 4, 4, 4, 4, 0]
let cancellable = numbers.publihser
			.removeDuplicates()
			.sink { print($0) }

// 0 1 2 3 4 0
```

## removeDuplicates(by:)

> 이건 제공된 closure에서 평가한대로 이전 요소와 일치하지 않는 요소만 publihs한다.
closure는 두 요소가 동일한지 여부를 평가하기 위함이다.
여기서 true가 리턴되면 두번째 요소가 첫번째 여소와 중복임을 나타내는 것이다.
> 

```swift
struct Point {
		let x: Int
		let y: Int
}

let points = [Point(x: 0, y: 0), Point(x: 0, y: 1),
							Point(x: 1, y: 1), Point(x: 2, y: 1)]
let cancellable = points.publisher
			.removeDuplicates { prev, current in 
					prev.x == cucrrent.x
		}.sink { print($0) }

// Point(x: 0, y: 0) Point(x: 1, y: 1) Point(x: 2, y: 1)
```

## tryRemoveDuplicates(by: )

> removeDuplicates에서 closure에 error 방출할 수 있는 오퍼레이터
> 

```swift
struct BadError: Error {}
let numbers = [0, 0, 0, 0, 2, 2, 3, 3]
let cancellable = numbers.publisher
			.tryRemoveDuplicates { first, second -> Bool in
					if (first == 3 && second == 3) {
							throw BadError()
					}
					return first == second
			}
			.sink(
					receiveCompletion: { print($0) },
					receiveValue: { print($0) }
			)

// 0, 2, 3 Error 

```

## replaceEmpty(with: )

> 빈 스트림을 제공된 요소로 바꾸는 오퍼레이터
> 

```swift
Let numbers: [Double] = []
cancellable = numbers.publisher
		.replaceEmpty(with: Double.nan)
		.sink { print($0) }

// nan
```

## replaceError(with: )

> 에러를 제공된 요소로 바꾸는 오퍼레이터
> 

```swift
struct BadValuesError: Error {} 
let numbers = [0, 0, 0, 0, 1, 2, 2, 3, 3, 3, 4, 4, 4, 4] 
let cancellable = numbers.publisher 
			.tryRemoveDuplicates { first, second -> Bool in 
					if (first == 4 && second == 4) { 
								throw BadValuesError() 
						}
					 return first == second 
				} .replaceError(with: 10000) 
				.sink( 
						receiveCompletion: { print ("\($0)") }, 
						receiveValue: { print ("\($0)", terminator: " ") }
				) 

// Prints: "0 1 2 3 4 10000 finished"
```