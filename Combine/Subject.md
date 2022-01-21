# Subject

# Subject : Publisher의 일종이다.

```swift
protocol Subject: AnyObject, Publisher
```

> AnyPublisher와 다른 점은 .send(_:)를 통해 외부에서도 값을 방출 할 수 있다는 것이다.
> 

## PassthroughSubject

> downstream subscriber에게 값을 보내는 subejct
> 

```swift
let subject = PasstrhoughSubject<String, Never>()

let subscription1 = subejct
				.sink(
				receiveCompletion: { completion in 
								print("(1): \(completion)" },
				receiveValue : { value in 
								print("(1): \(value)")}
						)

let subscription2 = subejct
				.sink(
				receiveCompletion: { completion in 
								print("(2): \(completion)" },
				receiveValue : { value in 
								print("(2): \(value)")}
						)

subject.send("Hello")
// (1): Hello
// (2): Hello

subscription1.cancel()

subject.send("world")
// (2): Hello

subject.send(completion: .finished)

subject.send("Happy")

// (2): finished
```

## CurrentValueSubject

> PassthroughSubject와 달리 초깃값과 최근 발행된 element에 대한 buffer를 갖는다
> 

```swift
var cancel = Set<AnyCancellable>()

let subject = CurrentValueSubject<Int, Never>(0)

subject.sink(
		receiveValue: { print($0) })
	.store(in: &cancel)
// 0

subject.send(1)
subject.send(2)
// 1
// 2

print(subject.value)
// 2

subject.value = 3
// 3

// subject.value = .finished (X) 불가능

subject.send(completion: .finished)
// finished

```