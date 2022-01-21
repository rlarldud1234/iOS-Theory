# Combine

# Combine

> 이벤트 처리 연산자를 결합하여 비동기식으로 이벤트 처리를 사용자 정의합니다.
> 

# Publisher

- Publisher 프로토콜은 시간에 따른 값의 흐름을 전달할 수 있는 프로토콜이다. Publisher는 값을 받아 처리하고 전달하는 연산자들을 가진다.

# Subscriber

- Publishers 체인의 끝에서, subscribers는 값을 수신받을 때 동작된다. publishers는 subscribers가 요청한 경우에만 값을 내보낸다. 이를 통해 우리는 subscribers코드가 언제 값을 수신하고, publishers와 연결될지 정할 수 있다.

## Publisher 종류

- Future: 단일 이벤트와 종료 혹은 실패를 제공하는 publisher (RxSwift의 Single이랑 비슷함)
- Just: 단일 이벤트 발생 후 종료되는 publisher
- Deferred: 구독이 이뤄질 때 publisher가 만들어 질 수 있도록 하는 Publisher
- Empty: 이벤트 없이 종료되는 publisher
- Fail: 오류와 함께 종료되는 publisher
- Record: 입력과 완료를 기록해 후에 다른 subscriber에서 반복할 수 있는 publisher

## Subscriber 방법

- subscribe 사용방법

```swift
class SubscriberExample: Subscriber {

	typealias Input = String()
	typealias Failure = Never()

	func receive(subscription: Subscription) {
		// subscriber에게 pulbihser를 성공적으로 구독했다고 알리고 아이템 요청 가능
	}

	func receive(_ input: String) -> Subscribers.Demand {
		// subscriber에게 publisher가 item을 생성했다는 것을 알림
	}

	func receive(completion: Subscribers.Completion<Never>) {
		// subscriber에게 publisher가 정상적으로 또는 error로 publish를 완료했음을 알림
	}
}

// Subscriber는 프로토콜로 정의해서 사용가능
```

> example
> 

```swift
class SubscriberExample: Subscriber {

	typealias Input = String()
	typealias Failure = Never()

	func receive(subscription: Subscription) {
		print("구독 시작")
		subscription.request(.unlimited) // publisher에게 item 요청
	}

	func receive(_ input: String) -> Subscribers.Demand {
		print("\(input)")
		return .none
	}

	func receive(completion: Subscribers.Completion<Never>) {
		print("completion")
	}
}

let publisher = Just("Hello")
publisher.subscribe(SubscriberExample())
```

- sink

```swift
let publisher = Just("Hello")
publisher.sink(
	receiveCompletion: { 
		print($0) // completion되거나 error가 생겼을 때 실행됨
	}, receiveValue: { 
		print($0) // Item값을 받아옴
	}
)
```

- assign

```swift
class exampleObject {
	var value: String = "" {
		didset {
			print(value)
		}
	}
}

let object = exampleObject()

// 배열을 publisher로 생성함
let publisher = ["Hello", "World"].publisher

// publisher를 구독하면서 publisher의 아이템이 object에 있는 value에 들어감
publisher.assign(to: .\value, on: object)
```