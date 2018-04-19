## #5. Strategies for Implementing Specific App Features

> 각각의 앱은 서로 다른 기능과 필요성을 가지고 있지만, 어떤 특징들은 앱에서 공통적입니다. 이번 장에서는 특정 유형의 기능을 앱에서 어떻게 구현할 수 있는지에 대한 가이드라인을 제시합니다.



### Privacy Strategies

> 사용자의 개인 정보를 보호하는 것은 앱을 디자인할때 고려해야할 중요한 부분입니다. 보호해야 할 개인정보에는 사용자의 신원 및 개인 정보와 사용자의 데이터가 포함됩니다. 시스템 프레임워크는 연락처와 같은 데이터를 관리하기 위해 개인 정보 제어를 기본적으로 제공하지만, 앱은 로컬에서 사용하는 데이터를 보호하기 위해 개별 적인 조치를 취해야 합니다.



#### Protecting Data Using On-Disk Encryption

> 데이터 보호는 내장된 하드웨어에 암호화 된 형식으로 파일을 저장하고, 필요할 때 암호를 해독하는 방식을 사용합니다. 사용자의 기기가 잠겨있는 동안에는 해당 파일을 만든 앱도 파일에 접근 할 수 없습니다. 앱이 보호된 파일에 접근하려면 기기 잠금을 해제해야 합니다. 

데이터 보호는 대부분의 iOS 기기에서 사용할 수 있으며 다음 요구 사항을 준수해야 합니다.

* 사용자 기기의 파일 시스템은 데이터 보호를 지원해야 합니다. 
* 사용자는 기기에 활성화 된 암호 잠금을 설정해야 합니다.



파일을 보호하려면 원하는 보호 수준을 나타내는 속성을 파일에 추가해야 합니다. NSData 또는 NSFileManager 클래스를 사용하여 보호 여부를 지정할 수 있습니다.

- 새로 작성하는 파일은 NSData의 `writeToFile: options: error:` 메소드를 사용하여  보호 설정을 할 수 있습니다.
- 기존 파일의 경우 NSFileManager의 `setAttributes: ofItemAtPath: error:` 메소드를 사용해 NSFileProtectionKey의 값을 설정하거나 변경할 수 있습니다.

위 방법을 사용할 때는 파일에 대해 다음의 보호 수준 중 하나를 지정해야 합니다.

* **No protection** : 파일은 암호화되지만 패스코드로 보호되지 않으며 장치가 잠길 때 사용할 수 있습니다.
  * `NSDataWritingFileProtectionNone`의 옵션 (NSData) 또는 `NSFileProtectionNone` 속성 (NSFileManager)를 지정하면 됩니다.
* **Complete** : 장치가 잠겨있는 동안 파일이 암호화되어 접근할 수 없습니다. 
  * `NSDataWritingFileProtectionComplete`의  옵션 (NSData) 또는 `NSFileProtectionComplete` 속성 (NSFileManager)를 지정하면 됩니다.
* **Complete unless already open** : 파일이 암호화되며, 장치가 잠겨있는 동안 닫힌 파일에 액세스 할 수 없습니다. 사용자가 장치를 잠금 해제하면 앱에서 파일을 열고 사용할 수 있습니다. 하지만 파일이 열려있는 동안 사용자가 기기를 잠그면 앱에서 접근이 가능합니다.
  * `NSDataWritingFileProtectionCompleteUnlessOpen`의 옵션 (NSData) 또는 `NSFileProtectionCompleteUnlessOpen` 속성 (NSFileManager)를 지정하면 됩니다.
* **Complete until first login** : 장치가 부팅되고 사용자가 잠금을 해제 할 때까지 파일이 암호화되어 접근이 불가능합니다.
  * `NSDataWritingFileProtectionCompleteUntilFirstUserAuthentication`의 옵션 (NSData) 또는 `NSFileProtectionCompleteUntilFirstUserAuthentication` 속성 (NSFileManager)를 지정하면 됩니다.



파일을 보호하는 경우 해당 파일에 대한 접근 권한을 잃을 준비를 해야 합니다. 완전한 파일 보호가 활성화되면 사용자가 장치를 잠그면 앱은 파일의 내용에 대한 read/write가 불가능합니다. 다음을 이용해서 보호된 파일의 상태 변경 사항을 추적할 수 있습니다.

* App delegate는 `applicationProtectedDataWillBecomeUnavailable :` 와 `applicationProtectedDataDidBecomeAvailable :` 메소드를 구현할 수 있습니다.
* 모든 객체는 `UIApplicationProtectedDataWillBecomeUnavailable` 와 `UIApplicationProtectedDataDidBecomeAvailable` 알림을 등록 할 수 있습니다.
* 모든 객체는 공유된 UIApplication 객체의 `protectedDataAvailable` 속성값을 검사하여 파일에 접근 할 수 있는지 여부를 결정할 수 있습니다.

새롭게 작성하는 파일의 경우, 데이터를 작성하기 전에 데이터 보호를 사용하는 것이 좋습니다.

* `writeToFile : options : error :` 메소드를 사용하여 NSData 객체의 내용을 디스크에 쓰는 경우 해당 작업이 자동으로 수행됩니다. 기존 파일의 경우 데이터 보호를 추가하면 보호되지 않은 파일이 새 보호 버전으로 바뀝니다.




#### Identifying Unique Users of Your App

> 앱에서 사용자를 식별하는 작업은 명백한 이점을 줄 때만 해야합니다. 앱에서 한 사용자와 다른 사용자를 차별화 해야하는 경우 iOS는 이를 수행하는데 도움이 되는 식별자를 제공합니다. 그러나 높은 수준의 보안이 필요한 경우 더 많은 작업이 필요합니다. 예를 들어, 금융 서비스를 제공하는 앱은 사용자에게 로그인 자격 증명을 요구하여 사용자가 특정 계정에 접근할 권한이 있는지에 대한 확인이 필요합니다.

```
사용자를 식별 할 때 얻는 정보로 무엇을 할 것인지 항상 투명해야 합니다. 사용자를 식별하여 사용자를 추적하면 안됩니다.
```

다음은 사용자를 식별하는 데 필요한 몇 가지 시나리오와 이를 구현하는 방법 입니다.

* **사용자를 서버의 특정 계정에 연결하려고 할 때** : 사용자가 계정 정보를 안전하게 입력할 수 있는 로그인 화면이 필요합니다. 수집한 사용자 계정 정보는 암호화된 방식으로 저장하여 보호해야 합니다. 
* **다른 기기에서 실행중인 앱의 인스턴스를 차별화하려고 할 때 **: 한 기기의 사용자와 다른 기기의 사용자를 구별하는 id를 얻으려면 UIDeviece 클래스의 `identifierForVendor` 속성을 사용하면 됩니다. 이 기술을 통해 특정 사용자를 식별할 수 있습니다. 한 사용자는 서로 다른 id 값을 갖는 여러 기기를 가질 수 있습니다.
* **광고를 목적으로 사용자를 식별하려고 할 때** : ASIdentifierManager 클래스의 `advertisingIdentifier` 특성을 사용하여 사용자의 ID를 확보할 수 있습니다.

사용자는 모든 iOS 기기에서 앱을 실행할 수 있으므로 Apple은 여러 기기에서 동일한 사용자를 식별 할 수있는 방법을 제공하지 않습니다. 특정 사용자를 식별해야하는 경우 UUID(Universally Unique IDs), 로그인 계정 또는 다른 유형의 식별 시스템을 사용하여 자체 솔루션을 제공해야합니다.

----



### Respecting Restrictions

> 사용자는 앱에 사용되는 미디어의 등급을 지정하여 제한 설정할 수 있습니다. 만약 앱이 미디어를 포함하거나 수정하는 경우, 제한 설정을 확인하고 그 변경사항에 맞는 응답을 해야합니다.



제한 설정 값을 가져오려면 standardUserDefaults 객체의 `objectForKey` 메소드를 사용해 값을 확인 할 수 있습니다.

| Media rating key                         | Value                                    |
| ---------------------------------------- | ---------------------------------------- |
| com.apple.content-rating.ExplicitBooksAllowed | `Boolean`. If the value of this key is `NO`, explicit books are not allowed |
| com.apple.content-rating.ExplicitMusicPodcastsAllowed | `Boolean`. If the value of this key is `NO`, explicit music, movies, and podcasts are not allowed. |
| com.apple.content-rating.AppRating       | `NSNumber`. The value of this key ranges from 0 to 1000. An app whose rating is higher than the current key value is not allowed. |
| com.apple.content-rating.MovieRating     | `NSNumber`. The value of this key ranges from 0 to 1000. A movie whose rating is higher than the current key value is not allowed. |
| com.apple.content-rating.TVShowRating    | `NSNumber`. The value of this key ranges from 0 to 1000. A TV show whose rating is higher than the current key value is not allowed. |

만약 `objectForKey:`메소드가 위 특정 키에 대해 nil을 리턴하면 해당 정보를 사용할 수 없음을 의미합니다. 이 경우 앱은 자체 정책을 사용해서 적절한 등급을 결정할 수 있씁니다. 

사용자의 제한 설정 시기를 감지하려면 `NSUserDefaultsDidChangeNotification` 알림을 등록하면 됩니다. 공유된standardUserDefaults 객체가 영구 도메인 중에 환경 설정이 변경된 것을 감지하면 앱에 이 알림을 보냅니다.

앱 등급은 미국 국가 코드로 정의되며 보편적으로 적용됩니다.

| Rating name | Numerical value |
| ----------- | --------------- |
| 4+          | 100             |
| 9+          | 200             |
| 12+         | 300             |
| 17+         | 600             |

영화, TV 등급은 국가마다 다릅니다. 국가 또는 지역에서 영화 또는 TV 프로그램의 등급 시스템을 지정하지 않은 경우 앱은 자체 정책을 사용하여 적절한 등급을 결정해야합니다.

----



### Supporting Multiple Versions of iOS

> 최신버전의 iOS와 이전 버전을 함께 지원하는 앱의 경우, 이전 버전의 iOS에서 최신 API를 사용하지 못하도록 런타임 검사를 실시해야합니다. 런타임 검사는 현재 OS에서 사용할 수 없는 기능을 사용하려고 할 때 발생하는 앱 충돌(crash)를 방지해줍니다.

* 클래스가 존재하는지 여부를 판별하려면 해당 Class 객체가 nil인지 확인해야 합니다. Linker는 알 수없는 클래스 객체에 대해 nil을 반환하므로 다음과 유사한 조건부 검사를 사용할 수 있습니다.

  ``` objective-c
  if ([UIPrintInteractionController class]) {
     // Create an instance of the class and use it.
  }
  else {
     // The print interaction controller is not available so use an alternative technique.
  }
  ```

* 해당 메소드를 기존 클래스에서 사용할 수 있는지 확인하기 위해 `instancesRespondToSelector:` 또는 `respondsToSelector:`를 사용할 수 있습니다.

* C 기반 함수가 사용 가능한지 판별하려면 함수 이름과 NULL의 불린 비교를 통해 알 수 있습니다. 만약 NULL이 아니라면 이 함수를 호출 할 수 있습니다.

  ```objective-c
  if (UIGraphicsBeginPDFPage != NULL) {
      UIGraphicsBeginPDFPage();
  }
  ```



---




### Preserving Your App’s Visual Appearance Across Launches

> 앱이 백그라운드 실행을 지원하더라도 평생 실행될 수 없습니다. 이런 관점에서 시스템은 백그라운드로 전환된 앱을 현재 foreground에서 구동되는 앱의 메모리 확보를 위해 언제든지 시스템에 의해 종료 될수 있습니다. 하지만 앱이 이미 실행 중이든 종료되었든 사용자는 이에 대해 신경 쓰지 않아야 합니다. 사용자의 관점에서 볼 때 앱 종료는 일시적인 중단과 같아야 합니다. 따라서 사용자가 앱으로 돌아 오면 해당 앱은 항상 사용자를 마지막 사용 시점으로 되돌려 사용자는 진행 중이던 모든 작업을 계속할 수 있습니다. 이 동작은 사용자에게 더 나은 환경을 제공하며 UIKit에 내장 된 상태 복원 지원은 비교적 쉽게 구현할 수 있습니다. 여기서는 iOS의 infrastrucure를 활용해 상태 보존/복원(state preservation, restroration)을 구현 하는 방법을 알아봅니다.

UIKit의 상태 보존 시스템은 앱의 뷰 컨트롤러 및 뷰 상태를 보존하고 복원하기위한 간단하면서도 유연한 infrastructure를 제공합니다. Infrastructure의 임무는 적절한 시기에 보존/복원 프로세스를 실행하는 것입니다. 이를 위해 UIKit에는 앱의 도움이 필요합니다. 앱의 콘텐츠 만 이해하면 콘텐츠를 저장하고 복원하는 데 필요한 코드만 작성할 수 있습니다. 그리고 앱의 UI를 업데이트 할 때 알아야할 것은 새로운 콘텐츠의 인터페이스와 기존의 저장된 콘텐츠를 어떻게 매핑시킬지를 알아야 합니다. 



앱의 상태 보존을 위해 고려해야하는 다음 세 가지가 있습니다.

- App delegate 객체 (앱의 최상위 레벨 상태 관리)
- View Controller 객체 (앱의 전반적인 UI 관리)
- Custom Views (보존해야 할 custom data가 있는 경우)

UIKit은 유저 인터페이스에서 보존하고 싶은 부분을 고를 수 있도록 허용합니다. 또한 상태 보존 처리를 위한 코드가 이미 있는 경우 해당 코드를 계속 사용하면서 필요에 따라 UIKit 상태 보존 시스템으로 일부분 마이그레이션 할 수 있습니다.



#### Enabling State Preservation and Restoration in Your App

> 상태 보존/복원은 자동 기능이 아니므로 앱에서 사용도록 허용해야 합니다. App delegate에서 다음 메소드를 구현해야 합니다.

* `application: shouldSaveApplicationState:`
* `application: shouldRestoreApplicationState:`

일반적으로 이러한 메소드의 구현은 `YES`를 리턴하여 상태 보존/복원이 발생할 수 있음을 나타내야 합니다. 그러나 상태 보존/복원은 상황에 따라 `NO`를 리턴하여 작업이 발생하지 않도록 해야 합니다. 예를 들어, 업데이트 한 앱을 릴리즈 한 후 앱이 상태 복원을 유용하게 하지 않는 경우 `application:shouldRestoreApplicationState:`로부터 `NO`를 리턴 받길 원할 수도 있습니다. 



#### The Preservation and Restoration Process

> 상태 보존/복원은 옵트 인(허용해야 사용이 가능) 기능이며 앱의 도움으로 작동합니다. 앱은 보존되어야하는 객체를 식별하고 UIKit은 적절한 시간에 객체를 보존하고 복원하는 작업을 수행합니다.  UIKit은 보존/복원 프로세스의 많은 부분을 처리하므로, 어떻게 작업을 하는지 알아두면 전반적인 코드 작성과 흐름 이해에 용이합니다. 

상태 보존/복원에 대해 생각할 때, 먼저 두 프로세스를 분리하는 것이 좋습니다. UIKit은 앱이 foreground에서 background로 이동할 때와 같은 적절한 시간에 앱의 상태를 보존합니다. 상태 보존에 필요한 관련 데이터를 암호화된 디스크 상의 파일에 쓰고, 다음에 앱이 launch될때 UIKit이 해당 파일을 찾아 앱 상태 복원 시도합니다. 물론, 이때 파일은 암호화 되어있기 때문에 기기가 잠금해제 되어있는 경우에만 발생합니다.

상태를 복원하는 동안 `UIKit`은 보존된 데이터를 사용하여 UI를 재구성 하지만, 실제 객체 생성은 코드에 의해 처리됩니다. Storyboard에 접근해 객체를 로드하는 것은 가능하지만, 실제로 어느 객체가 필요한지, 어느 객체가 생성되었는지는 코드를 통해서 알 수 있기 때문입니다. 객체가 생성되면 UIKit이 보존해 놓은 자료를 이용해 객체를 초기화 합니다.

각각의 상태에서 앱은 다음과 같은 역할을 하게됩니다.

- 보존하는 동안 앱이 해야할 일
  - UIKit에 상태 보존을 해야한다고 알립니다.
  - UIKit에 어떤 ViewController와 View가 보존될 지 알립니다.
  - 보존되는 객체에 대한 데이터를 인코딩해야합니다.
- 복원하는 동안 앱이 해야할 일
  - UIKit에 상태 복원을 해야한다고 알립니다.
  - UIKit에서 요청한 객체를 제공(생성) 해야 합니다.
  - 복원할 객체의 상태를 디코딩하고, 이를 이용해 객체를 이전 상태로 되돌립니다.

앱의 책임 중 가장 중요한 부분은 UIKit에게 보존/복원할 객체를 알려주는 것으로, 관련 코드를 디자인 할때 대부분의 시간을 소비해야 합니다. 또한 실제 프로세스를 가장 잘 제어 할 수 있습니다. 그 이유를 이해하려면 예를 살펴 보는 것이 도움이됩니다.

다음 그림은 사용자가 몇몇 탭과 상호작용을 한 후의 탭 바 인터페이스의 뷰 컨트롤러 계층 구조를 보여줍니다. 보다시피 몇몇 뷰 컨트롤러는 자동적으로 앱 기본 스토리보드 파일의 일부로 로드되지만 몇몇 뷰 컨트롤러는 다른 탭의 뷰 컨트롤러로 present/push 됩니다. 상태 복원이 없으면 후속 실행 중 메인스토리 보드 파일의 뷰컨트롤러만 복원됩니다. 앱 상태 복원에 대한 지원을 추가하면 모든 뷰 컨트롤러를 보존할 수 있습니다. 



![1](https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Art/state_vc_hierarchy_initial_2x.png)



UIKit은 지정된 복원 식별자가 있는 객체만 보존합니다. 복원 식별자는 UIKit과 앱에 뷰와 뷰 컨트롤러를 식별하게 하는 문자열입니다. 이 문자열의 값은 코드에만 중요하지만 이 문자열이 있으면 UIKit에 태그가 있는 객체를 보존해야 한다고 알립니다. 보존 프로세스 중에 UIKit은 앱의 뷰 컨트롤러 계층을 살펴보고 복원 식별자가 있는 모든 객체를 보존합니다. 만약 뷰 컨트롤러에 복원 식별자가 없으면 해당 뷰 컨트롤러와 모든 뷰 및 자식 뷰 컨트롤러가 보존되지 않습니다. 

다음 그림은 이전 뷰 계층 구조의 업데이트 된 버전을 보여줍니다. 복원 식별자가 전부는 아니지만 대부분의 뷰 컨트롤러에 적용됩니다.

![2](https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Art/state_vc_hierarchy_preserve_2x.png)



앱에 따라 모든 뷰 컨트롤러를 보존하는 것이 바람직하지 않을 수도 있습니다. 뷰 컨트롤러가 일시적인 정보를 제공하는 경우 사용자는 복원 할 때 같은 지점으로 돌아가는 대신 인터페이스에서 보다 안정된 지점으로 복원할 수 있습니다.

보존하기로 선택한 각 뷰 컨트롤러는 나중에 복원 할 방법을 결정해야합니다. UIKit은 두 가지 방법으로 객체를 다시 만들 수 있습니다. 앱 델리게이트가 다시 만들도록하거나 뷰 컨트롤러에 복원 클래스를 할당하고 해당 클래스가 다시 만들도록 할 수 있습니다. 복원 클래스는 `UIViewControllerRestoration` 프로토콜을 구현하고 복원 시점에 지정된 객체를 찾거나 생성 할 책임이 있습니다. 다음은 각 항목을 사용할 때 필요한 몇 가지 팁입니다.

- 만약 main storyboard를 통해 view controller가 항상 로드된다면 `restore class`를 구현하지 말고, 대신 app delegate를 이용해 객체를 복원하는게 좋습니다.
- 만약 main storyboard를 통해 view controller가 로드되지 않는다면, `restore class`를 구현하는 것이 좋습니다.

보존 프로세스에서 UIKit은 저장할 객체를 식별하고 영향을 받은 각 객체의 상태를 디스크에 씁니다. 각 뷰 컨트롤러 객체는 저장하려는 어떤 데이터든 쓸 수 있는 기회가 주어집니다. 예를 들어 탭 뷰 컨트롤러는 선택한 탭의 identity를 저장합니다. 또한 UIKit은 뷰 컨트롤러의 복원 클래스와 같은 정보를 디스크에 저장합니다. 뷰 컨트롤러의 뷰 중에 복원 식별자가있는 경우 UIKit은 상태 정보도 저장하라는 메시지를 표시합니다.

다음에 앱이 실행되면 UIKit은 앱의 기본 스토리 보드 또는 nib 파일을 평상시처럼 로드하고 앱 델리게이트의 `application:willFinishLaunchingWithOptions:` 메소드를 호출한 다음 앱의 이전 상태를 복원하려고 시도합니다. 첫 번째 작업은 보존 된 것과 일치하는 뷰 컨트롤러 객체 세트를 앱에 제공하도록 요청하는 것입니다. 주어진 뷰 컨트롤러에 할당된 복원 클래스가 있는 경우, 해당 클래스는 객체를 제공하도록 요청받습니다. 그렇지 않으면 앱 델리게이트는 제공하도록 요청받습니다.



#### Flow of the Preservation Process

> 다음 그림은 상태 보존 중에 발생하는 상위 이벤트를 보여주고 앱 객체가 어떻게 영향을 받는지 보여줍니다. 보존하기 전에 UIKit은  `application:shouldSaveApplicationState:` 메소드를 호출하여 앱 델리게이트에게 보존해야 하는지 묻습니다. 이 메소드가 `YES`로 리턴되면 UIKit은 앱의 뷰와 뷰 컨트롤러를 수집하고 인코딩하기 시작합니다. 끝나면 인코딩 된 데이터를 디스크에 씁니다.

![3](https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Art/save_process_simple_2x.png)



다음에 앱이 시작될 때 시스템은 자동으로 보존된 상태 파일을 찾고 존재하는 경우 이 파일을 사용하여 인터페이스를 복원합니다. 이 상태 정보는 앱의 이전 및 현재 출시주기 사이에서만 관련이 있기 때문에 앱 실행이 완료된 후 파일은 일반적으로 삭제됩니다. 앱을 복원하는 중에 오류가 발생하면 파일도 삭제됩니다. 예를 들어, 앱이 복원 프로세스 중에 crash 나는 경우 시스템은 다음 실행주기에 crash를 피하기 위해 상태 정보를 자동으로 버립니다.



#### Flow of the Restoration Process

> 그림은 상태 복원 중에 발생하는 상위 이벤트를 보여주고 앱 객체가 어떻게 영향을 받는지 보여줍니다. 표준 초기화 및 UI 로딩이 완료된 후 UIKit은 상태 복원이 전혀 발생하지 않으면 `application:shouldRestoreApplicationState:` 메소드를 호출하여 앱 델리게이트에 요청합니다. 이는 앱 델리게이트에게 보존된 데이터를 검사하고 상태 복원이 가능한지 판단할 수 있는 기회입니다. 만약 가능하다면 UIKit은 앱 델리게이트 및 복원 클래스를 사용하여 앱의 뷰 컨트롤러에 대한 참조를 가져옵니다. 그런 다음 각 객체는 이전 상태로 복원하는 데 필요한 데이터가 제공됩니다.

![4](https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Art/restoration_process_simple_2x.png)



UIKit은 각각의 뷰 컨트롤러를 복원하는 데 도움이 되지만 뷰 컨트롤러 간의 관계를 자동으로 복원하지는 않습니다. 대신 각각의 뷰 컨트롤러는 자신을 이전 상태로 되돌리기 위해 충분한 상태 정보를 인코딩해야합니다. 예를 들어 네비게이션 컨트롤러는 네비게이션 스택에 있는 뷰 컨트롤러의 순서에 대한 정보를 인코딩합니다. 그런 다음 나중에 이 정보를 사용하여 뷰 컨트롤러를 스택의 이전 위치로 반환합니다. 자식 뷰 컨트롤러가 있는 다른 뷰 컨트롤러가 나중에 자식을 복원하는 데 필요한 정보를 인코딩하는 것과 비슷합니다.

모든 뷰 컨트롤러가 자식 뷰 컨트롤러를 인코딩해야하는 것은 아닙니다. 예를 들어, 탭바 컨트롤러는 자식 뷰 컨트롤러에 대한 정보를 인코딩하지 않습니다. 대신 앱이 탭바 컨트롤러 자체를 만들기 전에 적절한 자식 뷰 컨트롤러를 만드는 일반적인 패턴을 따르는 것으로 가정합니다.

앱의 뷰 컨트롤러를 다시 만들어야 하는 책임이 있으므로 복원 프로세스 중에 인터페이스를 유연하게 변경할 수 있습니다. 예를 들어 탭바 컨트롤러에서 탭의 순서를 바꾸고 보존된 데이터를 사용하여 각 탭을 이전 상태로 되돌릴 수 있습니다. 물론 앱 업데이트 도중과 같이 뷰 컨트롤러 계층을 크게 변경하면 보존된 데이터를 사용하지 못 할 수도 있습니다.



#### What Happnes When You Exclude Groups of View Controllers?

> 어떤 view controller의 restore identifier가 nil이면 해당 view controller와 이의 child view controllers은 보존 데이터에서 제외됩니다. 

예를들어, 다음 그림에서 네비게이션 컨트롤러는 복원 식별자가 없기 때문에 모든 자식 뷰 컨트롤러 및 뷰가 보존된 데이터에서 생략됩니다.

![5](https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Art/state_vc_caveats_2x.png)



뷰 컨트롤러를 보존하지 않기로 결정했다고 해도 모든 뷰 컨트롤러가 뷰 계층 구조에서 완전히 사라지는 것은 아닙니다. 실행시 앱은 기본 설정의 일부로 뷰 컨트롤러를 만들 수 있습니다. 예를 들어, 뷰 컨트롤러가 앱의 스토리 보드 파일에서 자동으로 로드되는 경우 다음 그림과 같이 기본 구성이더라도 표시됩니다.



![6](https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Art/state_vc_caveats_2_2x.png)



또 다른 사실은 뷰 컨트롤러가 자동으로 보존되지 않더라도 해당 뷰 컨트롤러에 대한 참조를 인코딩하여 수동으로 보존 할 수 있다는 것입니다. 위 그림(이 장의 첫 그림) 첫 번째 네비게이션 컨트롤러의 자식 뷰 컨트롤러 3개는 부모 네비게이션 컨트롤러에 복원 식별자가 없는 경우에도 복원 식별자가 있습니다. 앱 델리게이트(또는 보존 된 객체)가 해당 뷰 컨트롤러에 대한 참조를 인코딩하면 해당 상태가 보존됩니다. 네비게이션 컨트롤러에 이러한 순서가 저장되어 있지 않더라도 이러한 참조를 사용하여 뷰 컨트롤러를 재생성하고 이 후 실행주기 동안 네비게이션 컨트롤러에 설치할 수 있습니다.



#### Checklist for Implementing State Preservation and Restoration

> 앱의 상태 보존/복원을 지원하려면 app delegate와 view controller가 상태 정보를 인코딩/디코딩 할 수 있도록 수정해야 합니다. 만약 custome view를 사용하는 앱에 보존 가능한 상태 정보도 포함되어 있다면 이러한 객체도 수정해야 합니다. 

상태 보존/복원 기능 추가를 위한 코드 작성시, 아래 체크리스트를 확인해야 합니다.

- **필수** `application:shouldSaveApplicationState:`, `application:shouldRestoreApplicationState:`를 앱 델리게이트에 구현해야 합니다. 
- **필수** 보존하려는 각각의 view controller의 복원 식별자에 비어있지 않은 String 값 입력해야 합니다.
- **필수** `application:willFinishLaunchingWithOptions:` 에서 복원할 스크롤 위치와 관련 UI 설정을 위해 window를 살펴야 합니다.
- 필요하다면 view controller에 `restoration class`를 구현합니다. 만약 구현하지 않을 경우, 복원할 때 앱 델리게이트는 해당 뷰 컨트롤러를 제공하라는 요청을 받습니다.
- 보존/복원 하려는 view controller 혹은 view에 `encodeRestorableStateWithCoder:`와 `decodeRestorableStateWithCoder:`메소드를 추가합니다.
- 앱 델리게이트의 `application:willEncodeRestorableStateWithCoder:` 와 `application:didDecodeRestorableStateWithCoder:` 메소드를 사용해서 앱에 대한 모든 버전 정보 또는 추가 상태 정보를 인코딩/디코딩 합니다.
- 테이블 뷰나 콜렉션 뷰에서 data soucre 역할을 하는 객체는 `UIDataSourceModelAssociation protocol`을 구현해야합니다. 필수사항은 아니지만 이 프로토콜은 해당 뷰에서 선택되거나 보이는 영역의 복원을 도와줍니다.



#### Enabling State Preservation and Restoration in Your App

> 상태 보존/복원은 자동 기능이 아니므로 앱은 이를 사용하도록 혀용해야 합니다. 앱 상태 보존/복원을 자동 기능이 아니므로 app delegate에서 아래 두 메소드를 구현해 기능 지원을 명시해야합니다.

- `application:shouldSaveApplicationState:`
- `application:shouldRestoreApplicationState:`

일반적으로 이러한 메소드의 구현은 `YES`를 리턴하여 상태 보존/복원이 발생할 수 있음을 나타내야 합니다. 그러나 상태 보존/복원은 상황에 따라 `NO`를 리턴하여 작업이 발생하지 않도록 해야 합니다. 예를 들어, 업데이트 한 앱을 릴리즈 한 후 앱이 상태 복원을 유용하게 하지 않는 경우 `application:shouldRestoreApplicationState:`로부터 `NO`를 리턴 받길 원할 수도 있습니다. 



#### Preserving the State of Your View Controllers

> 앱의 뷰 컨트롤러 상태를 보존하는 것이 목표입니다. 뷰 컨트롤러는 사용자 인터페이스의 구조를 정의하며, 인터페이스를 제공하는데 필요한 뷰를 관리하고 해당 뷰의 뒤에서 데이터를 가져오고 설정합니다. 하나의 뷰 컨트롤러의 상태를 보존하려면 다음을 따라야 합니다.

- **필수** View에 복원 식별자 값을 할당 합니다.
- **필수** 실행 시 새로운 뷰 컨트롤러 객체를 생성하거나 찾을 코드 제공해야 합니다.
- **옵션** `encodeRestorableStateWithCoder :` 와 `decodeRestorableStateWithCoder :` 메소드를 구현하여 이 후 실행 중에 다시 만들 수 없는 상태 정보를 인코딩하고 복원합니다.



#### Marking Your View Controllers for Preservation

> UIKit은 restoreIdentifier 속성에 유효한 문자열 객체가 포함 된 뷰 컨트롤러만 보존합니다. 보존하려는 뷰 컨트롤러의 경우, 뷰 컨트롤러 객체를 초기화 할 때 이 속성의 값을 설정해야 합니다. 스토리보드 또는 nib 파일에서 뷰 컨트롤러를 로드하는 경우 복원 식별자를 설정할 수 있습니다.

복원 식별자에 적절한 값을 선택하는 것이 중요합니다. 복원 프로세스에서 코드는 가져오거나 새로 만들 뷰 컨트롤러를 복원 식별자를 이용해서 결정합니다. 모든 뷰 컨트롤러 객체가 다른 클래스를 기반으로 하는 경우 복원 식별자에 클래스 이름을 사용할 수 있습니다. 그러나 뷰 컨트롤러 계층에 동일한 클래스의 인스턴스가 여러 개 포함되어 있으면 각 뷰의 사용에 따라 다른 이름을 선택해야 합니다.

뷰 컨트롤러를 제공하라는 메시지가 표시되면 UIKit은 뷰 컨트롤러 객체의 복원 경로를 제공합니다. 복원 경로는 루트 뷰 컨트롤러에서 시작하여 뷰 컨트롤러 계층 구조를 따라 현재 객체로 가는 일련의 복원 식별자입니다. 예를 들어, 복원 식별자가 TabBarControllerID인 탭 바 컨트롤러가 있고 첫 번째 탭에는 NavControllerID이고 루트 뷰 컨트롤러의 식별자가 MyViewController입니다. 그러면 `TabBarControllerID/NavControllerID/MyViewController`과 같은 복원 경로가 나오게 됩니다.

모든 객체의 복원 경로는 고유해야 합니다. 뷰 컨트롤러에 두 개의 자식 뷰 컨트롤러가 있는 경우, 각 자식에는 서로 다른 복원 식별자가 있어야 합니다. 그러나 다른 상위 객체가 있는 두 개의 뷰 컨트롤러는 동일한 복원 식별자를 사용할 수 있습니다. 나머지 복원 경로가 필요한 고유성을 제공하기 때문입니다. 네비게이션 컨트롤러와 같은 일부 UIKit 뷰 컨트롤러는 자동으로 자식 뷰 컨트롤러를 명확히 하여 각 자식에 대해 동일한 복원 식별자를 사용할 수 있도록 합니다.



#### Restoring Your View Controllers at Launch Time

> 복원 프로세스 중에 UIKit은 보존된 사용자 인터페이스를 구성하는 뷰 컨트롤러 객체를 만들거나 찾도록 앱에 요청합니다. UIKit은 뷰 컨트롤러를 찾을 때 다음 프로세스를 준수합니다.

* **뷰 컨트롤러에 복원 클래스가 있는 경우 UIKit은 해당 클래스에 뷰 컨트롤러를 제공하도록 요청합니다.** UIKit은 연관된 복원 클래스의 `viewControllerWithRestorationIdentifierPath : coder : ` 메소드를 호출하여 뷰 컨트롤러를 검색합니다. 이 메소드가 nil을 반환하면 앱이 뷰 컨트롤러를 다시 만들지 않고 UIKit이 이를 찾지 않는다고 가정합니다.
* **뷰 컨트롤러에 복원 클래스가 없으면 UIKit은 앱 델리게이트에게 뷰 컨트롤러를 제공할 것을 요청합니다.**  UIKit은 앱 델리게이트의 `viewControllerWithRestorationIdentifierPath : coder : ` 메소드를 이용해서 복원 클래스가 없는 뷰 컨트롤러를 찾습니다. 이 메소드가 nil을 반환하면 UIKit은 뷰 컨트롤러를 암시적으로 찾으려고 시도합니다.
* **올바른 복원 경로가 있는 뷰 컨트롤러가 이미있 는 경우 UIKit은 해당 객체를 사용합니다.** 앱이 실행되면 (프로그래밍 방식으로 또는 리소스 파일에서 로드하여) 뷰 컨트롤러를 만들고 복원 식별자를 할당하면 UIKit은 복원 경로를 통해 이를 암시적으로 찾습니다.
* **뷰 컨트롤러가 원래 스토리보드 파일에서 로드 된 경우 UIKit은 저장된 스토리보드 정보를 사용하여 찾아서 만듭니다.** UIKit은 뷰 컨트롤러의 스토리보드에 대한 정보를 복원 아카이브에 저장합니다. 복원시, 이 정보를 사용하여 동일한 스토리보드 파일을 찾고 뷰 컨트롤러를 찾을 수 없는 경우 해당 뷰 컨트롤러를 인스턴스화합니다.

뷰 컨트롤러에 복원 클래스를 지정하면 UIKit에서 뷰 컨트롤러를 암시적으로 찾으려고 시도하지 않습니다. 복원 클래스의 `viewControllerWithRestorationIdentifierPath : coder :` 메소드가 nil을 반환하면 UIKit은 뷰 컨트롤러를 찾으려고 시도하는 것을 중지하고 뷰 컨트롤러를 실제로 만들지 여부를 제어 할 수 있습니다. 복원 클래스를 지정하지 않으면 UIKit은 뷰 컨트롤러를 찾을 수있는 모든 작업을 수행하여 응용 프로그램의 스토리보드 파일에서 필요에 따라 뷰 컨트롤러를 만듭니다.

복원 클래스를 사용하기로 결정한 경우 `viewControllerWithRestorationIdentifierPath : coder :` 메소드의 구현은 클래스의 새 인스턴스를 만들고 최소한의 초기화를 수행 한 결과의 객체를 리턴해야 합니다. 

아래 코드는 이 메소드를 사용하여 스토리보드에서 뷰 컨트롤러를 로드하는 방법의 예를 보여줍니다. 뷰 컨트롤러는 원래 스토리 보드에서 로드 되었기 때문에 이 메소드는 `UIStateRestorationViewControllerStoryboardKey` 키를 사용하여 아카이브에서 스토리보드를 가져옵니다. 이 메소드는 뷰 컨트롤러의 데이터 필드를 구성하려고 하지 않습니다. 이는 나중에 뷰 컨트롤러의 상태가 디코딩 될 때 발생합니다.

```objective-c
+ (UIViewController*) viewControllerWithRestorationIdentifierPath:(NSArray *)identifierComponents coder:(NSCoder *)coder {
   MyViewController* vc;
   UIStoryboard* sb = [coder decodeObjectForKey:UIStateRestorationViewControllerStoryboardKey];
   if (sb) {
      vc = (PushViewController*)[sb instantiateViewControllerWithIdentifier:@"MyViewController"];
      vc.restorationIdentifier = [identifierComponents lastObject];
      vc.restorationClass = [MyViewController class];
   }
    return vc;
}
```

앞의 예와 같이 복원 식별자 및 복원 클래스를 재할당하면 새로운 뷰 컨트롤러를 만들 때 사용하는 것이 좋습니다. 복원 식별자를 복원하는 가장 간단한 방법은 identifierComponents 배열의 마지막 항목을 가지고 뷰 컨트롤러에 할당하는 것입니다.

실행시 앱의 메인 스토리보드 파일에서 이미 로드 된 객체의 경우 각 객체의 새 인스턴스를 만들면 안됩니다. 대신 `viewControllerWithRestorationIdentifierPath : coder :` 메소드를 구현해서 적절한 객체를 반환하거나 UIKit에서 이러한 객체를 암시적으로 찾을 수 있게 해야 합니다.



#### Encoding and Decoding Your View Controller’s State

> 보존을 위해 예정된 각 객체에 대해 UIKit은 객체의 `encodeRestorableStateWithCoder :` 메소드를 호출하여 상태를 저장할 수 있는 기회를 제공합니다. 디코드 프로세스 동안 `decodeRestorableStateWithCoder :` 메소드에 일치하는 호출이 수행되어 해당 상태를 디코딩하고 이를 객체에 적용할 수 있습니다. 이 메소드의 구현은 선택 사항이지만 뷰 컨트롤러에 대해서는 권장됩니다. 이를 사용하여 다음 유형의 정보를 저장하고 복원 할 수 있습니다.

* 화면에 표시되는 데이터에 대한 참조 (데이터 자체가 아닌)
* 컨테이너 뷰 컨트롤러의 경우 하위 뷰 컨트롤러에 대한 참조
* 현재 선택 항목에 대한 정보
* user-configurable view가 있는 뷰 컨트롤러의 경우 해당 뷰의 현재 구성에 대한 정보.

인코드 및 디코드 메소드에서는 다른 객체를 포함하여 코더에서 지원하는 모든 값을 인코딩 할 수 있습니다. 뷰 및 뷰 컨트롤러를 제외한 모든 객체의 경우 객체는 NSCoding 프로토콜을 채택하고 해당 프로토콜의 메소드를 사용하여 상태를 작성할 수 있어야 합니다. 뷰 및 뷰 컨트롤러의 경우 코더는 NSCoding 프로토콜의 메소드를 사용하여 개체의 상태를 저장하지 않습니다. 대신 코더는 객체의 복원 식별자를 저장하고 보존 할 수 있는 객체의 목록에 추가하므로 해당 객체의 `encodeRestorableStateWithCoder :` 메소드를 호출합니다.

뷰 컨트롤러의 `encodeRestorableStateWithCoder :` 와 `decodeRestorableStateWithCoder :` 메소드는 구현시 어느 시점에서 항상 super를 호출해야 합니다. super를 호출하면 부모 클래스에 추가 정보를 저장하고 복원 할 수 있습니다.

다음 코드는 지정된 뷰 컨트롤러를 식별하는 데 사용되는 숫자 값을 저장하는 메소드의 샘플 구현을 보여줍니다.

```objective-c
- (void)encodeRestorableStateWithCoder:(NSCoder *)coder {
   [super encodeRestorableStateWithCoder:coder];
 
   [coder encodeInt:self.number forKey:MyViewControllerNumber];
}
 
- (void)decodeRestorableStateWithCoder:(NSCoder *)coder {
   [super decodeRestorableStateWithCoder:coder];
 
   self.number = [coder decodeIntForKey:MyViewControllerNumber];
}
```

코더 객체는 인코딩 및 디코딩 프로세스 중에 공유되지 않습니다. 보존 가능 상태를 가진 각 객체는 데이터를 읽거나 쓰는 데 사용할 수 있는 자체 코더를 받습니다. 고유한 코더를 사용한다는 것은 자신의 객체 사이에서 주요 네임 스페이스 충돌에 대해 걱정할 필요가 없다는 것을 의미합니다. 그러나 UIKit에서 제공하는 특수 키 이름은 사용하지 않아야합니다. 특히 각 코더에는 `UIApplicationStateRestorationBundleVersionKey` 와 `UIApplicationStateRestorationUserInterfaceIdiomKey  `가 포함되어 있으며 번들 버전 및 현재 사용자 인터페이스 관용구에 대한 정보를 제공합니다. 뷰 컨트롤러와 연결된 코더에는 해당 뷰 컨트롤러가 시작된 스토리 보드를 식별하는 `UIStateRestorationViewControllerStoryboardKey`가 포함될 수도 있습니다.



#### Preserving the State of Your Views

> 뷰에 보존할 가치가 있는 상태 정보가 있는 경우 나머지 뷰 컨트롤러들과 함께 저장할 수 있다. 일반적으로 소유하고 있는 뷰 컨트롤러에 구성되어 있기 때문에 대부분의 뷰는 상태 정보를 저장할 필요가 없습니다. 뷰의 상태를 저장해야하는 유일한 경우는 뷰 자체가 데이터 또는 소유한 뷰 컨트롤러와 독립적인 방식으로 사용자가 변경할 수 있는 경우입니다. 예를 들어, 스크롤 뷰는 현재 스크롤 위치를 저장합니다. 이 위치는 뷰 컨트롤러에 관심이 없지만 뷰 자체의 표시 방법에 영향을주는 정보입니다.

뷰의 상태를 저장하도록 지정하려면 다음을 수행하십시오.

* 뷰의 restorationIdentifier 속성에 유효한 문자열을 할당해야 합니다.
* 유효한 복원 식별자가 있는 뷰 컨트롤러의 뷰를 사용해야 합니다.
* 테이블 뷰 및 컬렉션 뷰의 경우 `UIDataSourceModelAssociation` 프로토콜을 사용하는 데이터 소스를 할당해야 합니다.

뷰 컨트롤러와 마찬가지로 뷰에 복원 식별자를 할당하면 뷰 객체에 앱이 저장하려고 하는 상태가 있음을 시스템에 알려줍니다. 복원 ID는 나중에 뷰를 찾는 데 사용될 수도 있습니다.

뷰 컨트롤러와 마찬가지로 뷰는 사용자 지정 상태를 인코딩하고 디코딩하는 메소드를 정의합니다. 저장 가능한 상태의 뷰를 작성하면 이 메소드를 사용하여 관련 데이터를 읽고 쓸 수 있습니다.



#### UIKit Views with Preservable State

> 모든 뷰의 상태를 저장하려면 뷰에 복원 식별자를 지정해야합니다. 복원 식별자가 없는 뷰는 UIKit에 의해 보존할 수 있는 객체 목록에 추가되지 않습니다.

UIKit 뷰에는 보존할 수 있는 상태 정보가 있습니다.

- UICollectionView
- UIImageView
- UIScrollView
- UITableView
- UITextField
- UITextView
- UIWebView





#### Preserving the State of a Custom View

> 복원 가능한 상태가 있는 custom view를 구현하는 경우 `encodeRestorableStateWithCoder :` 와 `decodeRestorableStateWithCoder : ` 메소드를 구현하고 이를 사용하여 해당 상태를 인코딩하고 디코딩합니다. 다른 방법으로 쉽게 재구성 할 수 없는 데이터만 저장하려면 이러한 방법을 사용하십시오. 이 메소드를 사용하여 사용자 상호 작용에 의해 수정된 데이터를 뷰에 저장합니다. 뷰 또는 제시 한 뷰 컨트롤러가 쉽게 구성 할 수있는 데이터를 제공하기 위해 이러한 메소드를 사용하지 마십시오.

다음 코드는 편집 가능한 텍스트를 포함하고 있는 커스텀 뷰의 선택을 보존하고 복원하는 방법의 예를 보여줍니다. 이 예에서 `selectionRange` 및 `setSelectionRange :` 메소드를 사용하여 접근 할 수 있습니다.이 메소드는 뷰에서 선택을 관리하는 데 사용하는 사용자 지정 메소드입니다. 데이터를 인코딩하려면 제공된 코더 객체에 데이터를 기록해야합니다. 데이터를 복원하려면 읽기 및 보기에 적용해야 합니다.

```objective-c
// Preserve the text selection
- (void) encodeRestorableStateWithCoder:(NSCoder *)coder {
    [super encodeRestorableStateWithCoder:coder];
 
    NSRange range = [self selectionRange];
    [coder encodeInt:range.length forKey:kMyTextViewSelectionRangeLength];
    [coder encodeInt:range.location forKey:kMyTextViewSelectionRangeLocation];
}
 
// Restore the text selection.
- (void) decodeRestorableStateWithCoder:(NSCoder *)coder {
   [super decodeRestorableStateWithCoder:coder];
   if ([coder containsValueForKey:kMyTextViewSelectionRangeLength] &&
           [coder containsValueForKey:kMyTextViewSelectionRangeLocation]) {
      NSRange range;
      range.length = [coder decodeIntForKey:kMyTextViewSelectionRangeLength];
      range.location = [coder decodeIntForKey:kMyTextViewSelectionRangeLocation];
      if (range.length > 0)
         [self setSelectionRange:range];
   }
}
```



#### Preserving Your App's High-Level State

> view controller와 view에 보존 되는 데이터가 아니라도, 다음 두 메소드를 이용해 앱에 필요한 기타 데이터를 저장 할 수 있습니다.

- `application:willEncodeRestorableStateWithCoder:`
- `application:didDecodeRestorableStateWithCoder:`

위 메소드는 보존 프로세스의 맨 처음에 호출 되므로 UI의 현재 버전 등과 같은 high-level의 앱 상태를 저장 할 수 있습니다.



#### Tips for Saving and Restore State Information

> 앱 상태 보존 / 복원을 구현하는 경우 다음 지침을 고려하십시오.

- **버전 정보를 앱 상태와 함께 인코딩 합니다.** 보존을 할때에는 앱의 현재 UI 버전을 식별할 수 있는 버전 문자열 또는 숫자를 인코딩 하는 것이 좋습니다. 버전 정보는 app delegate의 `application:willEncodeRestorableStateWithCoder:` 메소드에서 인코딩 할 수 있으며, `application:shouldRestoreApplicationState:` 메소드가 호출 될 때 인코딩 되었던 정보를 확인할 수 있습니다.
- **앱 상태 정보에 데이터 모델 객체를 포함하지 마십시오.** 앱은 iCloud 또는 디스크의 로컬 파일에 데이터를 별도로 저장해야합니다. 상태 복원 메커니즘을 사용하여 데이터 저장시, 복원 작업중에 문제가 발생하면 보존 된 데이터가 삭제 될 수 있습니다.
- **앱 상태 보존은 기존에 디자인된 방식으로 view controller를 사용할 것을 기대합니다.** 앱의 view controller 계층 구조는 기존에 설계된 방식을 유지해야합니다. view controller 간의 계층/포함 관계와 상과없이 view를 보여주는 경우 보존 시스템이 보존할 view controller를 제대로 찾아내지 못 할 수도 있습니다.
- **모든 view controller를 보존하는 것은 의도에 맞지 않을 수있습니다.** 어떤 경우의 view controller는 보존하는 것이 적절하지 않을 수 있습니다. 예를 들어 사용자의 민감한 정보가 있는 화면은 보존/복원하지 않습니다.
- **복원 프로세스 중에 view controller 클래스를 변경하지 마십시오.** 복원 하는 동안 앱의 클래스가 보존했던 객체의 클래스와 일치 하지 않거나 하위 클래스가 아니면 복원 작업을 하지 않습니다.
- **사용자가 강제로 앱을 종료하면 시스템에서 앱의 보존 상태를 자동으로 삭제합니다.** 앱이 종료될때 복원 상태를 삭제하는 것은 일종의 안정 장치입니다. 보존/복원 기능을 테스트 하려면 멀티태스킹 상태를 사용하지 말고 Xcode를 이용해 임시 명령이나 제스처로 exit를 호출하도록 해야 합니다.


---




### Tips for Developing a VoIP App

> VoIP(Voice over Internet Protocol) 앱을 통해 사용자는 기기의 셀룰러 서비스 대신 인터넷 연결을 이용해 전화를 걸 수 있습니다. iOS 8 이상에서는 APN(Apple Push Notification Serviece) 및 PushKit 프레임 워크의 API를 사용하여 VoIP 앱을 만들 수 있습니다. 푸쉬기능을 기용하면 VoIP 사용을 위한 지속적인 네트워크 연결이나 소켓 구성을 하지 않아도 됩니다. VoIP 푸시가 도착하면 앱이 푸시를 처리할 시간이 주어집니다(앱이 종료된 상태에도).

- VoIP 구현을 위해 다음과 같은 요구사항들이 있습니다.
  - **앱에 VoIP 백그라운드 모드를 사용해야합니다.** Xcode project Capabilities 탭에서 백그라운드 모드를 활성화 합니다. 보통 VoIP 앱은 오디오 내용을 포함하기 때문에 Audio 및 AirPlay 백그라운드 모드도 사용하는것이 좋습니다.
  - PushKit API를 사용하여 VoIP 푸시 알림을 수신하고, 수신 알람을 처리하도록 하십시오.
  - 오디오 세션을 구성하고 활성화 시키십시오.
  - iPhone에서 더 나은 UX를 보장하려면 `Core Telephony` 프레임 워크를 이용하십시오.
- VoIP 백그라운드 모드를 활성화 하면 백그라운드에서 오디오를 재생할 수도 있고, 시스템 부팅 직후 백그라운드에서 재시작 되어 VoIP 서비스를 항상 사용 할수 있습니다.





#### Using the Reachability Interfaces to Improve the User Experience

> VoIP 앱은 네크워크 환경에 크게 의존하기 때문에, `System Configuration framework`의 `reachability interface`를 활용해 네크워크 가능성을 확인하고 결과에 맞게 동작을 조정해야합니다. `reachability interface`를 사용하면 네트워크 상태가 변경 될때마다 앱에 알림을 보낼 수 있습니다. 변경 사항을 감지하여 사용자에게 VoIP 연결 상태에 대해 알려주도록 해야합니다.

- 네트워크 가용성을 파악해 앱의 동작을 조정하면, 기기의 배터리 수명을 향상 시킬 수 있으며 앱이 더 자주 잠자기 상태로 있을 수 있습니다.
- `reachability interface`에 대한 자세한 내용은 [System Configuration Framework Reference](https://developer.apple.com/reference/systemconfiguration)를 참고하세요

