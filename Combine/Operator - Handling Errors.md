# Operator - Handling Errors

## assertNoFailure(_:file:line: )

> upstream publisher가 실패하면 fatal error를 발생시키고,
그렇지 않으면 수신된 모든 입력을 republish한다.
> 

```swift
struct ExampleError: Error { }

let subject = CurrentValueSubject<String, Error>("error 발생~")
subject
    .assertNoFailure()
    .sink(receiveCompletion: {
        print("completion: \($0)")
        
    }, receiveValue: {
        print("value: \($0)")
    })

subject.send("Hello")
subject.send(completion: Subscribers.Completion<Error>.failure(ExampleError()))

// error 발생~ 
// Hello
// fatal error 발생
```

## catch(: )

> upstream publisher의 오류를 다른 publisher로 대체하여 처리하는 오퍼레이터이다.
(에러가 났을 때 그 에러를 다른 publisher로 처리할 때 쓴다.)
> 

```swift
struct ExampleError: Error { }

let numbers = [1, 6, 7, 5, 3, 0, 6, 8, 9]
numbers.publisher
    .tryLast(where: {
        guard $0 != 0 else { throw ExampleError() }
        return true
    })
    .catch { _ in
        Just(-1)
    }
    .sink { print($0) }

// -1
```

## tryCatch(_: )

> upstream publisher의 오류를 다른 publisher로 바꾸거나 새로운 오류로 발생시켜 처리하는 오퍼레이터이다.
> 

```swift
enum ExampleError: Error { case error }

let numbers = [1, 6, 7, 5, 3, 0, 6, 8, 9]
numbers.publisher
    .tryMap { v in
        if v > 0 {
            return v
        } else {
            throw ExampleError.error
        }
    }
    .tryCatch { error -> AnyPublisher<Int, Error> in
        throw ExampleError.error
    }
    .sink(receiveCompletion: {
        print("completion: \($0)")
    }, receiveValue: {
        print("value: \($0)")
    })

// 1 6 7 5 3 failure erorr 발생
```

## retry(_: )

> 지정한 횟수까지 upstream publisher 구독을 다시 만드는 오포레이터이다.
* 보통 네트워킹에서 많이 사용이 된다.
> 

```swift
struct WebSiteData: Codable { var rawHTML: String }

let myURL = URL(string: "https://www.example.com")

let cancellable = URLSession.shared.dataTaskPublisher(for: myURL!)
    .print()
    .retry(3)
    .map({(page) -> WebSiteData in
        return WebSiteData(rawHTML: String(decoding: page.data, as: UTF8.self))
    })
    .catch { error in
        return Just(WebSiteData(rawHTML: "<HTML>Unable to load page - timed out.</HTML>"))
    }
    .sink(receiveCompletion: { print ("completion: \($0)") },
          receiveValue: { print ("value: \($0)") } )
```