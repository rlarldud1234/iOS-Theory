## Tuist



### Tuist란?

##### Tuist는 XcodeGen과 유사하게 Xcode 프로젝트를 관리할 수 있는 툴이다. XcodeGen과 차이점은 XcodeGen은 yml이나 json으로 프로젝트 설정을 관리하지만, Tuist는 Project.swift 파일로 설정을 관리한다.



### Tuist의 장점

#### 쉬운 언어 

##### 프로젝트에 대해 생각할 때 설명하세요. 빌드 설정, 단계 및 기타 복잡성이 구현 세부 사항이 됩니다.



#### 재사용 가능성

##### 여러 Xcode 프로젝트를 유지하는 대신, 프로젝트를 한 번 설명하고 어디에서나 재사용하세요.



#### 초점

##### 생성된 프로젝트는 당신의 초점과 생산성에 최적화되어 있습니다. 그것들은 당신이 당면한 작업에 필요한 것을 포함하고 있습니다.



#### 초기 오류

##### 만약 우리가 당신의 프로젝트가 컴파일되지 않을 것이라는 것을 안다면, 우리는 일찍 실패합니다. 우리는 당신이 빌드 시스템이 오류를 거품을 일으키기를 기다리는 데 시간을 낭비하는 것을 원하지 않습니다.



#### 협약

##### 프로젝트의 구조에 대해 의견을 가지세요. 팀이 새로운 프로젝트를 만드는 데 사용할 수 있는 프로젝트 공장을 정의하세요



#### 스케일

##### Tuist는 대규모 프로젝트를 지원하도록 최적화되어 있다. 프로젝트가 목표 1이든 1000이든, 차이가 없어야 합니다.





### Tuist 사용법

- ##### 실행하기

  ```
  tuist init --platform ios
  ```



- ##### 설정파일

  ```swift
  let project = Project(
      name: "MyApp",
      organizationName: "MyOrg",
      targets: [
          Target(
              name: "MyApp",
              platform: .iOS,
              product: .app,
              bundleId: "io.tuist.MyApp",
              infoPlist: "Info.plist",
              sources: ["Sources/**"],
              resources: ["Resources/**"],
              headers: Headers(
                  public: ["Sources/public/A/**", "Sources/public/B/**"],
                  private: "Sources/private/**",
                  project: ["Sources/project/A/**", "Sources/project/B/**"]
              ),
              dependencies: [
                  /* Target dependencies can be defined here */
                  /* .framework(path: "framework") */
              ]
          ),
          Target(
              name: "MyAppTests",
              platform: .iOS,
              product: .unitTests,
              bundleId: "io.tuist.MyAppTests",
              infoPlist: "Info.plist",
              sources: ["Tests/**"],
              dependencies: [
                  .target(name: "MyApp")
              ]
          )
      ]
  )
  ```



- ##### 실행시키기

  ```swift
  tuist generate
  ```

  