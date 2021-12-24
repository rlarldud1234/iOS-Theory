# Clean Architecture

# Business Rule?

> Business Rule(Logic)은 컴퓨터 프로그램에서 실세계의 규칙에 따라 데이터를 생성·표시·저장·변경하는 부분을 일으킨다. (Domain Logic)
> 

- Business Rule(Logic)은 유저의 입력(UI)과 DB 사이에서 발생한 데이터 교환을 위한 특정 알고리즘이나 규칙이 정의된 Layer이다.
- 이는 고객의 요구에 따라 변경될 수 있으며, 그렇기 때문의 별도의 Layer에 배치되어야 한다.

# System Architecture?

> System Architecture는 시스템 구조(structure), 행위(behavior), 뷰(view)를 정의하는 개념 모델이다.
시스템의 목적을 달성하기 위해 컴포넌트가 어떻게 상호작용하고 정보가 어떻게 교환되는지를 설명된다.
> 

## 관심사의 분리

소프트웨어를 계층으로 나누게 되면 관심사를 분리할 수 있다.

1. 프레임워크 독립적
    - SystemArchitecture는 라이브러리 존재 여부나 프레임워크에 한정적이지 않아 도구로써 사용하는 것이 가능하다.
2. 테스트 용이
    - Business Rule은 UI, DB, Web, Server 등 기타 외부 요인과 관계없이 테스트가 가능하다.
3. UI독립적
    - 시스템의 다른 부분을 고려하지 않고 UI를 변경할 수 있다.
4. Database 독립적
    - DB또한 독립적으로 변경할 수 있으며(SQL, Mongo 등), 이는 Business Rule과 얽매이지 않는다.
5. 외부 기능 독립적
    - Business Rule은 외부 사항(UI, DB)에 대해서 아무것도 모른다.

# Clean Architecrue

![Untitled](Clean%20Architecture%20705e106bf3ba4fbbbfe401556a2b095a/Untitled.png)

## Domain

Domain Layer는 Business Rules가 존재하는 영역이다.

EX) 번역앱은 번역을 하고 쇼핑몰 앱은 물건을 팝니다.

비즈니스 본질은 쉽게 바뀌지 않으므로 Business Rule은 잘 변하지 않는 안정된 영역이다.

## Infrasturcture

Infrasturcture Layer는 UI, DataBase, Web API, Frameworks등이 존재하는 영역이다.

이는 Domain에 비하여 자주, 쉽게 바꿀 수 있다. 예를 들어 UI Button의 형태는 쉽게 바뀔 수 있지만 방법은 그렇지가 않는다.

Layer를 분리하고, 관심사를 분리하는 규칙을 의존성 규칙(Dependency Rule)이라고 한다.

# Dependency Rule

> 모든 소스코드 의존성은 반드시 outer에서 inner로, 고수준 정책을 향해야 한다.
> 

Dependency Rule은 Business Logic을 담당하는 코드들이 DB또는 Web같이 구체적인 세부사항에 의존하지 않고 독립적으로 실행해야 한다는 규칙이다.

![Untitled](Clean%20Architecture%20705e106bf3ba4fbbbfe401556a2b095a/Untitled%201.png)

이 사진을 

Depency Rule에 따르면 

inner circle에 해당하는 Domain은 outer circle에 해당하는 Infrastructure에 대해서 아무것도 모른다.

이는 UI, DB는 Business Rule에 의존하지만 Business Rule은 그렇지 않는다.

ex) UI가 웹이건, 모바일이건,  DB가 SQL이건 NoSQL이건 관계없이 Business Rule 입장에서는 아무 상관이 없다.

이렇게 분리된 Layer 덕에 Infrastructure를 쉽게 변경할 수 있다.

이 그림은 위의 그림을 자세히 내려다 본 것이다.

![Untitled](Clean%20Architecture%20705e106bf3ba4fbbbfe401556a2b095a/Untitled%202.png)

Domain이 Entities, Use Cases로 세분화 되었고, Adapter가 새로 생겨 Domain과 infrastructure 사이의 경계를 관리한다.

이 그림도 Dependency Rule에 따라 outer에서 inner로 의존성을 가지게 된다.

## Entity

Entity는 애플리케이션에서 핵심적인 기능인 Business Rule들을 담고 있다.

OOP관점에서 보면

Entity는 class 내의 method들의 그룹을 볼 수 있다.

ex) 대출 이자가 10%라는 규칙이 있다. 지불해야 하는 이자를 손으로 계산하던 컴퓨터로 하던 대출 이자가 10%라는 사실은 변하지 않을 것이다.

대출 이자 10%라는 규칙(Business Rule)은 외부 사항(outer layer)을 전혀 모른다.

즉, Entity들은 outer layer들에 속한 다른 class나 component들에 전혀 모르고 신경을 쓰지 않아도 된다.

## Use Cases

Use Case는 특정 application에 대한 Business Rule이다.

Use Case는 시스템이 어떻게 자동화 될 것인지 대해서 정의하고 app의 행위를 결정한다.

Business Rule을 사용하여 Use Case의 목적을 달성한다.

Use Case는 Entities에 의존하는 동시에 상호작용한다. 물론 outer layer에 대해서는 아는게 없다.

대출이 web page, iphone 어디에서 이뤄지던, data가 cloud, sqlite 어디에 저장되건 Use Case는 관심이 없다.

다만 이 게층에서는 outer layer에서 사용할 수 있는 abstract class나 interfaces를 정의한다.

## Adapters

Adapter는 domain과 infranstructure 사이의 번역기 역할을 수행한다.

ex) GUI로부터 input data를 받아 use cases와 Entities에게 편리한 repakage합니다.

Use Case와 Entities를 output을 가져와 GUI에 표시하거나 DB에 저장하기 편리한 형식으로 repakage합니다.

Adapters는 GUI의 MVC 아키텍쳐를 완전히 내포하며, Presnter와 View, Controller가 모두 여기에 속한다.

## Infrastructure

Infrastructure는 모든 i/O compnents(UI, DB, Frameworks, devices)가 있는 곳이다. 

이는 변화될 가능성이 매우 높기 때문에 안정적인 domain과는 확실히 분리가 되어있고, 그렇기 때문에 비교적 쉽게 변화되고 component 또한 쉽게 교환된다.

# 교차 경계

![Untitled](Clean%20Architecture%20705e106bf3ba4fbbbfe401556a2b095a/Untitled%203.png)

위 다이어그램은 원의 경계가 어떻게 교차하는지 보여주는 예시이다.

Adapter에 해당하는 Presenter와 Controller가 상위 층인 Use Case와 어떻게 소통하는지 나타내고 있다.

제어의 흐름이 Controller → Use Case → Presenter로 흘러감을 알 수 있다.

고수준 Use Case가 저수준의 Presenter를 참조하고 있는 상황이다. 
이 문제를 해결하기 위해 의존 관계 역전의 법칙(DIP)를 사용하여 한다.

지금까지 실컷 사용한 Dependency Rule이지만 이를 해결하기 위해서는 
아래 두 상황을 고려해야 한다.

## What data cross the boundaries

의존성 규칙을 지키기 위해서는 단순하고, 고립된 형태의 데이터 구조를 사용해야 한다.

DB 형식의 데이터 구조 또는 Framework에 중속적인 데이터 구조가 사용하게 된다면, 이러한 저수준의 데이터 형식을 고수준에서 알아야 하기에 의존성 규칙을 위반하게 된다.

## Crossing boundaries

제어 흐름은 원의 내부에서 외부로 향할 수 있는데, 이는 의존성 규칙에서 위배되므로 의존성 역전 법칙(DIP)을 이용하여 해결해야 한다.

즉 고수준에서 저수준에 직접 참조하게 될 때 Interface를 하나 둠으로써, 저수준의 세부사항을 알 필요 없고 변동사항에도 영향을 받지 않게 된다.

![Untitled](Clean%20Architecture%20705e106bf3ba4fbbbfe401556a2b095a/Untitled%204.png)

의존성 규칙 위반

![Untitled](Clean%20Architecture%20705e106bf3ba4fbbbfe401556a2b095a/Untitled%205.png)

의존성 규치 위반x