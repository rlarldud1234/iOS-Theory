# CoreData

> 단일 장치에서 데이터를 유지·캐싱하고 변경 사항을 되돌릴 수 있다.
데이터를 영구적으로 저장할 수 있고, 임시 데이터를 캐싱할 수 있다.
> 

## 기능 소개

### Persistence

- 코어 데이터는 저장소에 각 개체를 매핑하는 세부적인 내용을 추상화하여 직접적으로 DB를 관리할 필요 없이 Swift나 Object-C를 통해 데이터를 저장할 수 있도록 한다.

### Undo and Redo of Individual of Batched Changes

- 코어 데이터의 실행 취소 매니저가 변경 사항을 추적하고 이들을 개별적 또는 그룹 단위로 되돌릴 수 있다.

### Background Data Tasks

- 객체로 Json을 parsing하는 것과 같이 잠재적으로 UI-blocking할 수 있는 data task를 백그라운드에서 수행할 수 있다.

### View Synchronization

- 코어 데이터는 테이블 뷰와 콜렉션 뷰에 대해 data source를 제공하여 뷰와 데이터를 동기화시킨다.

### Versioning and Migration

- 코어 데이터는 데이터 모델 버저닝과 데이터 마이그레이션을 제공한다.

## 데이터 스택

앱의 모델 계층을 공동으로 지원하는 클래스를 설정해야 하는데, 이러한 클래스들을 코어 데이터 스택이라 한다.

![Untitled](CoreData%2020c9551f897c49aba3c59e44cb2b4df6/Untitled.png)

### NSManagedObjectModel

해당 객체의 인스턴스는 앱의 타입, 프로퍼티, 관계에 대해 작성한 모델 파일을 나타난다.

### NSManagedObjectContext

해당 객체의 인스턴스는 앱 타입의 인스턴스에 대한 변화를 추적한다.

managed object의 변경 사항을 조작하고 수정하는데 사용한다.

### NSPersistentStoreCoordinator

해당 객체의 인스턴스는 저장된 앱 타입의 인스턴스를 저장하고 불러온다.

### NSPersistentContainer

해당 객체의 인스턴스는 model, context, store, corrdinator를 한 번에 설정한다###

## Using Core Data in the Background

코어 데이터는 멀티 스레드 환경에서 사용되도록 고안되었지만, 코어 데이터 내의 모든 객체가 thread-safe한 것은 아니다.

코어 데이터를 멀티 스레드 환경에서 사용하기 위해

- ManagedObjectContext는 초기화시 연결된 스레드(큐)애 바인딩 된다.
- context로 부터 나온 managed object는 context가 바인딩된 큐에 바인딩된다.

## Comparing Main Queue and Private Queue Context

### main queue context

애플리케이션 인터페이스와 관련하여 메인 큐에서만 사용된다.

### private queue context

초기화시 고유 큐를 생성하며 오직 해당 큐에서만 사용된다.

큐는 비공개적이고 NSManagedObjectContext 인스턴스 내부에 있기 때문에 perform() 및 performAndWait() 메소드를 통해서만 엑세스 할 수 있다.

## Initializing and Configuring Contexts

NSPersistentContainer를 사용하면

- main queue: viewContext
- private queue: performBackgroundTask(), newBackgroundContext()

## Avoiding Problems

유저와 관련 없는 작업을 메인 큐에서 처리하지 말아야 한다.

여러 큐 사이에 NSManafedObject 객체를 넘기지 말자.

managed object reference를 다른 큐로 전달해야 할 떄에는 NSManagedObjectID 객체를 사용해라.