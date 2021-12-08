## Swift SOLID

> ### SOLID 원칙은 객체 지향 프로그래밍에 사용되는 원칙이다.

### 객체 지향 프로그래밍이란?

#### 프로그래밍에서 필요한 데이터를 추상화시켜 상태와 해위를 가진 객체를 만들고 그 객체들 간의 유기적인 상호작용을 통해 로직을 구성하는 프로그래밍 방법이다.



#### 장점

- ##### 코드 재사용 용이

  - ##### 남이 만든 클래스를 가져와서 이용할 수 있고 상속을 통해 확장해서 사용할 수 있다.

- ##### 유지보수가 쉬움

  - ##### 절차 지향 프로그래밍에서는 코드를 수정해야할 때 일일이 찾아 수정해야하는 반면 객체 지향 프로그래밍에서는 수정해야 할 부분이 클래스 내부에 멤버 변수혹은 메서드로 존재하기 때문에 해당 부분만 수정하면 된다.

- ##### 대형 프로젝트에 적합

  - ##### 클래스 단위로 모듈화시켜서 개발할 수 있으므로 대형 프로젝트처럼 여러 명, 여러 회사에서 프로젝트를 개발할 때 업무 분담하기 쉽다.
  
  

#### 단점

- ##### 처리 속도가 상대적으로 느림

- ##### 객체가 많으면 용량이 커질 수 있음

- ##### 설계시 많은 시간과 노력이 필요



### 객체 지향 프로그래밍 키워드 5가지



#### 1) 클래스 + 인스턴스(객체)

- ##### 클래스 : 어떤 문제를 해결하기 위한 데이터를 만들기 위해 추상화를 거쳐 집단에 속하는 속성과 행위를 변수와 메서드로 저으이한 것으로 객체를 만들기 위한 메타 정보라고 볼 수 있다.

- ##### 인스턴스(객체) : 클래스에서 정의한 거슬 토대로 실제 메모리에 할당된 것으로 실제 프로그램에서 사용되는 데이터



#### 2) 추상화

- ##### 추상화는 클래스를 설계하는 것 자체를 의미한다. 즉 "공통의"의 속성이나 기능을 묶어 이름을 붙이는 것이다.



#### 3) 캡슐화

- ##### 목적

  - ##### 코드를 재수정 없이 재활용하는 것

  - ##### 접근 제어자를 통한 정보 은닉

##### 캡슐화를 통해 객체가 외부에 노출하지 않아야할 정보 또는 기능을 접근제어자를 통해 적절히 제어 권한이 있는 객체에서만 접근하도록 할 수 있기에 수정이 일어났을 때 책임이 객체만 수정하면 되기에 영향 범위를 예측하는데 수월해졌다.



#### 4) 상속

- ##### 상속은 부모클래스의 속성과 기능을 그대로 이버받아 사용할 수 있게하고 기능의 일부분을 변경해야 할 경우 상속받은 자식클래스에서 해당 기능만 다시 수정하여 사용할 수 있게 하는 것이다.

  - ###### 다중상속은 불가하다.(클래스의 상속 관계에서 혼란을 줄 수 있기 때문에 상속은 반드시 하나만 가능하고 필요에 따라 인스턴스를 사용할 수 있게 한다.)



#### 5) 다형성

##### 오버라이딩(Overriding), 오버로딩(Overloading)이 가능하다는 얘기이다

- ##### 오버라이딩 : 부모클래스의 메서드와 같은 이름, 매개변수를 재정의 하는 것

- ##### 오버로딩 : 같은 이름 함수를 여러개 정의하고, 매개변수의 타입과 개수를 다르게 하여 매개변수에 따라 다르게 호출하는 것



### SOLID



#### SRP(Single Responsibility Principle) : 단일 책임 원칙

- ##### 클래스의 수정 이유는 하나여야 한다.

- ##### 하나의 클래스는 하나의 책임만 가져야 한다.

- ##### 하나의 책임이 여러가지 클래스로 나눠져 있어도 안된다.

- ##### 하나의 클래스 안에 여러가지 협력관계가 있는 것은 괜찮다.

#### before

```swift
class Handler {
  func handle() {
    let data = requestDataToAPI()
    let array = parse(data: data)
    saveToDB(array: array)
  }
    
  private func requestDataToAPI() -> Data {
  // send API request and wait the response
  }
    
  private func parse(data: Data) -> [String] {
  // parse the data and create the array
  }
    
  private func saveToDB(array: [String]) {
  // save the array in a database (CoreData/Realm/...)
  }
}
```



#### after

```swift
class Handler {
 
    let apiHandler: APIHandler
    let parseHandler: ParseHandler
    let dbHandler: DBHandler
 
    init(apiHandler: APIHandler, parseHandler: ParseHandler, dbHandler: DBHandler) {
        self.apiHandler = apiHandler
        self.parseHandler = parseHandler
        self.dbHandler = dbHandler
    }
 
    func handle() {
        let data = apiHandler.requestDataToAPI()
        let array = parseHandler.parse(data: data)
        dbHandler.saveToDB(array: array)
    }
}
 
class APIHandler {
 
    func requestDataToAPI() -> Data {
        // send API request and wait the response
    }
}
 
class ParseHandler {
 
    func parse(data: Data) -> [String] {
        // parse the data and create the array
    }
}
 
class DBHandler {
 
    func saveToDB(array: [String]) {
        // save the array in a database (CoreData/Realm/...)
    }
}
```



### OCP(Open-Closed Principle) : 개방 폐쇄 원칙

- #### 확장에는 열려 있어야 하지만 변경에는 닫혀 있어야 한다.

- #### 객체가 변경될 때는 해당 객체만 바꿔도 동작이 잘되면 OCP를 잘 지킨것이고, 바꿔야 할 것이 많으면 OCP를 잘 지키지 않은 것

- #### 모듈이 주변환경에 지나치게 의존해서는 안된다.



#### before

```swift
enum Country {
  case korea
  case japan
  case china
}

class Flag {
  let country: Country
  
  init(country: Country) {
    self.country = country
  }
}

func printNameOfCountry(flag: Flag) {
  switch flag.country {
    case .china:
      print("중국")
    case .korea:
      print("한국")
    case .japan:
      print("일본")
  }
}
```



#### after

```swift
protocol Country {
  var name: String { get }
}

struct Korea: Country {
  let name: String = "한국"
}

struct Japan: Country {
  let name: String = "일본"
}

struct China: Country {
  let name: String = "중국"
}

class Flag {
  let country: Country
  
  init(country: Country) {
    self.country = country
  }
}

func printNameOfCountry(flag: Flag) {
  print(flag.country.name)
}
```



### LSP(Liskov Substitutetution Principle) : 리스코드 치환 원칙

- #### 서브타입은 (상속받은) 기본 타입으로 대체 가능해야 한다.

- #### 자식 클래스는 부모 클래스 동작(의미)를 바꾸지 않는다.

- #### 상속을 사용하였을 때 서브 클래스는 자신의 슈퍼클래스 대신 사용되도 같은 동작을 해야한다.

#### before

```swift
class 직사각형 {
  var 너비: Float = 0
  var 높이: Float = 0
  var 넓이: Float {
    return 너비 * 높이
  }
}

class 정사각형: 직사각형 {
  override var 너비: Float {
    didSet {
      높이 = 너비
    }
  }
}

func printArea(of 직사각형: 직사각형) {
  직사각형.높이 = 5
  직사각형.너비 = 2
  print(직사각형.넓이)
}

let rectangle = 직사각형()
printArea(of: rectangle) //10
let square = 정사각형()
printArea(of: square) //4
```



#### after

```swift
protocol 사각형 {
  var 넓이: Float { get }
}

class 직사각형: 사각형 {
  private let 너비: Float
  private let 높이: Float
  
  init(너비: Float, 높이: Float) {
    self.너비 = 너비
    self.높이 = 높이
  }
  
  var 넓이: Float {
    return 너비 * 높이
  }
}

class 정사각형: 사각형 {
  private let 변의길이: Float
  
  init(변의길이: Float) {
    self.변의길이 = 변의길이
  }
  
  var 넓이: Float {
    return 변의길이 * 변의길이
  }
}
```



### ISP(Interface Segregation Principle) : 인터페이스 분리 원칙

- #### 클래스 내에서 사용하지 않는 인터페이스를 구현하지 말아야 한다.

- #### 클라이언트 객체는 사용하지 않는 메소드에 의존하면 안된다.

- #### 인터페이스가 거대해지는 경우 SRP를 어기는 경우가 생길 수 있고, 해당 인터페이스를 체택해서 사용하는 경우 쓰지 않는 메소드가 있어도 넣어야 할 경우가 발생할 수 있으니 최대한 인스턴스를 분리하는 것을 권장

#### before

```swift
protocol GestureProtocol {
  func didTap()
  func didLongTap()
  func didDoubleTap()
}

class GestureButton: GestureProtocol {
  func didTap() {}
  func didLongTap() {}
  func didDoubleTap() {}
}

class DoubleTapButton: GestureProtocol {
  func didDoubleTap() {}
  
  // Useless
  func didTap() {}
  func didLongTap() {}
}
```



#### after

```swift
protocol TapGestureProtocol {
  func didTap()
}

protocol LongTapGestureProtocol {
  func didLongTap()
}

protocol DoubleTapGestureProtocol {
  func didDoubleTap()
}

class GestureButton: TapGestureProtocol, LongTapGestureProtocol, DoubleTapGestureProtocol {
  func didTap() {}
  func didLongTap() {}
  func didDoubleTap() {}
}

class DoubleTapButton: GestureProtocol {
  func didDoubleTap() {}
}

class LongAndTapButton: LongTapGestureProtocol, TapGestureProtocol {
  func didTap() {}
  func didLongTap() {}
}

func doSomething(button: DoubleTapGestureProtocol & LongTapGestureProtocol) {
  button.didDoubleTap()
  button.didLongTap()
}
```



### DIP(Dependency Inversion Principle) : 의존관계 역전 원칙

- #### 상위 레벨 모듈은 하위레벨 모듈에 의존해서는 안된다.

- #### 두 모듈은 추상화된 인터페이스(프로토콜)에 의존해야 한다.

- #### 추상화 된 것 은 구체적인 것에 의존하면 안되고, 구체적인 것이 추상화된 것에 의존해야 한다.

- #### 의존 관계를 맺을 때 변화하기 쉬운 것 또는 자주 변화하는 것(클래스) 보다는 변화하기 어렵거나 거의 변화가 없는 것(프로토콜)에 의존해야 한다.

- #### 하위레벨 모듈이 상위레벨 모듈을 참조하는 것은 되지만 상위레벨 모듈이 하위레벨 모듈을 참조하는 것은 안하는 것이 좋다. 그런 경우는 제네릭이나 Associate를 사용한다.

- #### DIP를 만족하면 의존성 주입이라는 기술로 변화를 쉽게 수용할 수 있다.



#### before

```swift

class 맥북13인치 {
  func 전원켜기() {}
}

class 개발자 {
  let 노트북: 맥북13인치 = 맥북13인치()
  
  func 개발시작() {
    노트북.전원켜기()
  }
}
```

 

#### DI

```swift
class 맥북13인치 {
  func 전원켜기() {}
}

class 개발자 {
  let 노트북: 맥북13인치
  
  init(노트북: 맥북13인치) {
    self.노트북 = 노트북
  }
  
  func 개발시작() {
    노트북.전원켜기()
  }
}
```



#### after

```swift

protocol 노트북 {
  func 전원켜기()
}

class 개발자 {
  let 노트북: 노트북
  
  init(맥북: 노트북) {
    self.노트북 = 맥북
  }
  
  func 개발시작() {
    노트북.전원켜기()
  }
}

class 맥북13인치: 노트북 {
  func 전원켜기() {}
}

class 맥북15인치: 노트북 {
  func 전원켜기() {}
}

class 레노버: 노트북 {
  func 전원켜기() {}
}
```



#### 연관 타입(Asscoiate Type)의 활용

- ##### Generic에서 나오는 개념 중 하나로 진짜 Type을 주는 것이 아니다.

- ##### 타입이 그 어떤 것이어도 상관없지만, 하나의 타입임은 분명하다는 의미

- ##### 프롵토콜이 실제로 적용되기 전에는 사용되지 않는 타입

- ##### 해당 프로토콜의 외부에서는 참조할 수 없다.

#### before

```swift
protocol 노트북 {
    func 전원켜기()
    func 앱스토어(맥북: 맥북13인치)
}

class 맥북13인치: 노트북 {
    func 전원켜기() {}
    func 앱스토어(맥북: 맥북13인치) {
        print("앱스토어에 접근")
    }
}
```



#### after

```swift
protocol 노트북 {
    func 전원켜기()
    func 앱스토어(맥북: MyType)
    associatedtype MyType
}

class 맥북13인치: 노트북 {
    func 전원켜기() {}
    func 앱스토어(맥북: 맥북13인치) {
        print("앱스토어에 접근")
    }
}

class 맥북15인치: 노트북 {
    func 전원켜기() {}
    func 앱스토어(맥북: 맥북15인치) {
        print("앱스토어에 접근")
    }
```





### SOLID 원칙을 지키지 않았을 때의 문제점

- #### 경직성 : 하나를 바꿀 때 다른것들도 바꿔야해서 시스템을 변경하기 어렵다.

- #### 부서지기 쉬움 : 한 부분이 변경되었을 때 다른 한 부분이 영향을 받아서 새로운 오류가 발생한다.

- #### 부동성 : 다른 시스템이 재사용하기 힘들다.

- #### 점착성 : 제대로 동작하기 힘들다.

- #### 불필요한 복잡성 : 과도한 설계

- #### 불필요한 반복

- #### 불투명성 : 의도를 파악하기 어려운 혼란스러운 표현