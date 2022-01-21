# Operator - Mapping Elements

## map

> map은 upstream publisher의 모든 요소를 변환하는 operator이다.
> 

```swift
let publisher = [1, 2, 3, 4].publisher
let cancellable = publisher
				.map { $0 * 2 }
				.sink(receiveValue: { print($0) }) // 2, 4, 6, 8
```

## tryMap

> tryMap은 error-throwing closure를 사용하여 upstream publisher의 모든 요소를 변환하는 operator이다.
(closure에서 error 발생 시 publish를 종료하게 된다.)
> 

```swift
let publisher = [1, 2, 3, 4].publisher
let cancellable = publisher.tryMap { $0 * 2 }
					.sink(receiveCompletion: { 
							switch $0 {
							case .finished: 
										print("finished")
							case .failure(let error):
										print(error.localizedDescription)
							}
						}, receiveValue: { print($0) }
// 2
// 4
// 6
// 8
// finished
```

### error 발생 Code

```swift
enum Error: String, Error {
		case cantHandle
}

func changeNum(num: Int?) thorws -> Int {
		guard let num = num else { throw Error.cantHandle }
		return num * 2

let publisher = [1, 2, nil, 3, 4].publisher
let cancellable = publisher.tryMap { try changeNum(num: $0) }
					.sink(receiveCompletion: { 
							switch $0 {
							case .finished: 
										print("finished")
							case .failure(let error):
										print(error.localizedDescription)
							}
						}, receiveValue: { print($0) }
// 2
// 4
// error 발생 - 종료
```

## flatMap

> upstream publisher의 모든 요소를 지정한 최대 publishers 수까지 새로운 publishers로 변환한다.
> 

```swift
public struct WeatherStation { 
		public let stationID: String 
}
 var weatherPublisher = PassthroughSubject<WeatherStation, URLError>()

 let cancellable = weatherPublisher.flatMap(maxPublishers: .max(1), { station -> URLSession.DataTaskPublisher in 
			let url = URL(string:"http://localhost:8080/\(station.stationID)")! 
			return URLSession.shared.dataTaskPublisher(for: url) 
}).sink( 
		receiveCompletion: { completion in
				 print(completion) 
		}, 
		receiveValue: { 
				print($0.data, $0.response) 
		} 
) 

weatherPublisher.send(WeatherStation(stationID: "KSFO")) // San Francisco, CA 
weatherPublisher.send(WeatherStation(stationID: "EGLC")) // London, UK 
weatherPublisher.send(WeatherStation(stationID: "ZBBB")) // Beijing, CN

```

## mapError

> upstream publisher의 모든 오류를 새로운 오류로 변환하는 operator이다.
> 

```swift
struct DivisionByZeroError: Error {}
struct MyGenericError: Error { var wrappedError: Error } 

func myDivide(_ dividend: Double, _ divisor: Double) throws -> Double { 
			guard divisor != 0 else { throw DivisionByZeroError() } 
			return dividend / divisor
	 } 

let divisors: [Double] = [5, 4, 3, 2, 1, 0] 
divisors.publisher 
		.tryMap { try myDivide(1, $0) } 
		.mapError { MyGenericError(wrappedError: $0) } 
		.sink( 
				receiveCompletion: { print ("completion: \($0)") , 
				receiveValue: { print ("value: \($0)", terminator: " ") } 
		)

// Prints: "0.2 0.25 0.3333333333333333 0.5 1.0 completion: failure(MyGenericError(wrappedError: DivisionByZeroError()))"
```

## replaceNil

> stream에 있는 nil값을 element로 변환하는 operator
> 

```swift
let numbers = [1, 2, 3, 4, nil]
numbers.publisher
		.replaceNil(with: 5)
		.sink { print($0) }

// 1 2 3 4 5
```

## scan

> closure에서 변환 한 마지막 값과 함께, 현재 요소를 closure에 제공하여 upstream publisher의 요소를 변환한다.
> 

```swift
let publisher = (0...5).publisher
		.scan(0, { return $0 + $1 }
		.sink(receiveValue: { print($0) })

// 0 1 3 6 10 15
```

## tryScan

> sacn중 일어나는 error-throw를 하는 operator
> 

```swift
struct DivisionByZeroError: Error {}
/// A function that throws a DivisionByZeroError if `current`
/// provided by the TryScan publisher is zero.
func myThrowingFunction(_ lastValue: Int, _ currentValue: Int) throws -> Int {
guard currentValue != 0 else { throw DivisionByZeroError() }
return (lastValue + currentValue) / currentValue
}
let numbers = [1, 2, 3, 4, 5, 0, 6, 7, 8, 9]
let cancellable = numbers.publisher
.tryScan(10) { try myThrowingFunction($0, $1) }
.sink(
receiveCompletion: { print ("\($0)") },
receiveValue: { print ("\($0)", terminator: "\n") }
)
// Prints: "11 6 3 1 1 failure(DivisionByZeroError())".
```

## setFailureType

> 실패 타입을 지정하는 operator(Failurer가 Never일 때만 사용 가능하다)
> 

```swift
let publisher = [0, 1, 2, 3, 4, 5].publisher
let publisher1 = CurrentValueSubject<Int, Never>(0)

let cancellable = publihser1 
		.setFailureType(to: Error.self)
		.combineLatest(publisher1) /* publisher와 publisher1의 error 타입 불일치 But setFailurType을 사용함으로써
																error 없이 실해 가능하다. */
		.sink(
				receiveCompletion: { print("completion: \($0)") },
				receiveValue: { print("value: \($0)") }
		}
```