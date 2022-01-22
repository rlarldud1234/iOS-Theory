# Operator - Reducing Elements

## collect()

> 친구는 받은 elment를 수집하고, upstream publihser가 완료되면 collection의 single array를 내보내는 오퍼레이터
> 

```swift
let numbers = (0...8)
let cancellable = numbers.publisher
			.collect()
			.sink { print($0) }

// [0, 1, 2, 3, 4, 5, 6, 7, 8]
```

## collect(_ :)

> coount에 최대 수신 element 수를 넣으면 된다
> 

```swift
let numbers = [0...10]
let cancellable = numbers.publisher 
		.collect(5)
		.sink { print($0) } 

// [0, 1, 2, 3, 4] [5, 6, 7, 8, 9] [10]
```

## collect(_:options:)

파라미터

```swift
func collect<S>(_ strategy: Publishers.TimeGroupingStrategy<S>, 
								options: S.SchedulerOptions? = nil) ->
								Pulbuhsers.CollectByTime<Self, S> where S : Schedular
```

내가 지정한 Scehduler/Stride로 element 배열을 보내는 것이다.

```swift
let sub = Timer.publisbh(every: 1, on: .main, in: .default)
					.autoconnect()
					.collect(.byTime(RunLoop.main, .secods(5)))
					.sink { print($0) }

// [2021-01-21 8:37:01 +0000, 2021-01-21 8:37:02 +0000, 2021-01-21 8:37:03 +0000,
//	2021-01-21 8:37:04 +0000, 2021-01-21 8:37:05 +0000]
```

TimeGroupingStrategy

```swift
public enum TimeGroupingStrategy<Context> where Context: Scheduler {

		case byTime(Context, Context.SchedularTimeType.Stride)

		case byTimeOrCount(Context, Context.SchedulerTimeType.Stride, Int)
}
```

Context : Scheduler 타입을 가진다.

## ignoreOutput()

> 출력을 무시하다
> 

```swift
let numbers = [1, 2, 3, 4].publisher

numbers.ignoreOutput()
	.sink(receiveCompletion: { print($0) },
			receiveValue: { print($0) })

// finished
```

## reduce(_: _:)

> stream의 각 element를 수집하고 완료시 최종 결과만 publish한다.
* scan과 다른 점 : scan은 과정도 보여주지만 reduce는 결과만 보여준다.
> 

```swift
let publisher = (0...10).publisher
		.reduce(0, { return $0 + $1 }
		.sink { print($0) }

// 55
```

## tryReduce

> reduce에 error 방출 가능한 closure 사용
> 

```swift
struct ExampleError: Error { }

func throwingFunction(_ lastValue: Int, _ currentValue: Int) throws -> Int { 
	guard currentValue != 0 else { throw ExampleError() }
	return lastValue + currentValue
}

let numbers = [1, 2, 3, 4, 0, 5]
let cancellable = numbsers.publisher
		.tryReduce(10) { try throwingFunction($0, $1) }
		.replaceError(with: 0)
		.sink(
			receiveComplteion: { print($0) },
			receiveValue: { print($0) }
			)

// 0 finished
```