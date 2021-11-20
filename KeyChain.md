## KeyChain

-  ### KeyChain이란? 

  - ##### UserDefaults에 보안 문제를 방지할 수 있는 디바이스 안에 암호화된 데이터 저장 공간을 의미한다.

  

- ### KeyChain 특징

  - ##### 사용자가 직접 제거하지 않는 이상, 앱을 제거하고 설치해도 데이터는 남아있다.

  - ##### 디바이스를 잠그면 KeyChain도 잠기고, 디바이스를 풀리면 KeyChain도 풀린다.

    - ##### KeyChain이 잠김 상태에는 Item들에 접근할수도, 복호화 할 수도 없다.

    - ##### KeyChain이 풀린 상태에서도 해당 Item을 생성하고 저장한 어플리케이션에서만 접근이 가능하다.

  - ##### 같은 개발자가 개발한 여러앱에서 키체인 정보를 공유할 수도 있다.

  

- ### KeyChain Service API

  - ##### KeyChain Service API로 민감한 데이터를 암호화 - 복호화 하며 재사용하는 행위를 보다 쉽고 안전하게 사용할 수 있게 한다.
  
- ### KeyChain Items

  - #### 저장할 Data의 종류

    - ##### KSecClassInternetPassword

    - ##### KSecClassCertificate

    - ##### KSecClassGenericPassword

    - ##### KSecClassIdentity

    - ##### KSecClassKey

  - #### 대표적으로 인터넷용 아이디/ 패스워드를 저장할 때 사용하는 KSecClassInternetPassword,

    #### 인증서를 저장할 때 사용하는 KSecClassCertificate,

    ##### 일반 비밀번호를 저장할 때 사용하는 KSecClassGenericPassword등이 있다.

  

- ### KeyChain Query

  ##### KSecClass : 키체인 아이템 클래스 타입,

  ##### KSecAttrService : 서비스 아이디, // 앱 번들

  ##### KSecAttrAcount : 저장할 아이템의 계정 이름,

  ##### KSecAttrGeneric : 저장할 아이템의 데이터

  ```swift
  private let acount = "TokenService"
  private let service = Bundle.main.bundleIdentifier
  
  let query: [CFString: Any] = [KSecClass: KSecClassGenericPassword,
  															KSecAttrClass: service,
                                KSecAttrAcount: acount,
                                KSecAttrGeneric: data]
  ```

  

  #### Create

  - ##### SecItemAdd(_ : _ :)

  - ##### 키체인에 하나 이상의 항목을 추가할 때 사용

  ```swift
  func createTokens(_ token: [Token]) -> Bool {
    guard let data = try? JSONEncoder().encode(token),
    			let service = self.service else {return false}
    
    let query: [CFString: Any] = [KSecClass: KSecClassGenericPassword,
  																KSecAttrClass: service,
                                	KSecAttrAcount: acount,
                                	KSecAttrGeneric: data]
    
    return SecItemAdd(query as CFDictionary, nil) = errSecSuccess
  }
  ```

  

  #### Read

  - ##### SecItemCopyMatching(_ : _ :)

  - ##### 검색 커리와 일치하는 키체인 항목을 하나 이상 반환하는 기능. 또한, 특정 키 체인 항목의 속성을 복사할 수 있다.

  ```swift
  func readTokens() -> [Token]? { 
  	guard let service = self.service else {return nil}
  				let query: [CFString: Any] = [KSecClass: KSecClassGenericPassword,
  																			KSecAttrClass: service,
                                				KSecAttrAcount: acount,
                               	 				KSecMatchLimit: KSecMatchLimitOne,
                                 				KSecReturnAttributes: true,
                                        KSecReturnData: true]
   			 var items: CFTypeRef?
    if SecItemCopyMatching(query as CFDictionary, &item) != errSecSuccess { return nil }
    
    guard let existingItem = items as? [String: Any],
    		let data = existingItem[KSecAttrGeneric as String] as? Data,
    		let tokens = try? JSONDecoder().decode([TOPToken].self, from: Data) else { return nil }
    	
    return tokens
                                  
  }
  ```

  

#### Update

 - ##### SecItemUpdate(_ : _ :)

	- ##### 검색 쿼리와 일치하는 항목을 수정할 수 있는 기능

```swift
func updateTokens(_ token: [Token]) -> Bool {
  guard let query = self.query,
  			let data = try? JSONEncoder().encode(token) else { return false }
  
  			let attributes: [CFString: Any] = [KSecAttrAcount: account, 
                                           KSecAttrGeneric: data]
  
  return SecItemsUpdate(query as CFDictionary, attributes as CFDictionary) == errSecSuccess
}
```



#### Delete

- ##### SecItemDelete(_ : _ :)

- ##### 검색쿼리와 일치하는 항목을 제거하는 기능

```swift
func deleteTokens() -> Bool {
  guard let query = self.query else { return false }
  return SecItemDelete(query as CFDictionary) == errSecSuccess
}
```

