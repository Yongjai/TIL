#  1. Expected App Behaviors

> 여기서 배울 내용은 앱이 처리하고 앱 설계 초기 단계에서 고려해야 하는 동작에 대해 설명하는 장입니다.	



### Providing the Required Resources

> 모든 앱은 다음과 같은 리소스들과 메타데이터를 가지고 있어야 합니다.

* **An information property-list file**
  * 우리가 흔히 보고 사용하던 Info.plist 입니다. 여기에는 메타데이터가 포함되어 있는데 시스템에서 앱과 상호작용을 하는데 사용됩니다.
* **A declaration of the app's required capabilities**
  * 모든 앱은 실행하기 위해 필요한 하드웨어 성능이나 기능을 선언해줘야 합니다.
* **One or more icons**
  * 앱 아이콘을 의미합니다.
* **One or more launch images**
  * 앱이 실행되면 UI가 보여지기 전까지 시스템이 임시 이미지를 띄워줍니다. 

**위 리소스들은 모든 앱에서 필요하지만 이것만 포함시키면 앱이 실행된다는 뜻은 아닙니다.**

> 문서에는 없는 내용이긴 하지만 iOS는 일반적으로 HTTP 통신을 허용하지 않기 때문에 info.plist의 NSAppTransportSecurity에서 통신이 필요한 URL을 지정해주거나 NSAllowsArbitaryLoads를 추가해줘야 HTTP 통신이 가능한 것처럼 추가적인 설정이 필요하단 뜻으로 해석할 수 있을것 같습니다.



### The App Bundle

> iOS 앱을 빌드하게 되면 Xcode는 앱을 bundle로 묶어주는데, bundle은 앱과 연관된 리소스들을 모아놓은 디렉토리입니다. Bundle에는 앱 실행 파일과 supporting resources(앱 아이콘, 이미지 파일, 현지화 파일..)이 들어있습니다.

문서를 보면 일반적인 bundle에 어떤 리소스들이 들어있는지 표로 잘 정리되어 있습니다. 위에서 다뤘던 4가지 리스소들에 대해 좀 더 자세히 짚고 넘어가겠습니다.



#### **The Information Property List File( `info.plist` )**

- Xcode는 컴파일하는 동안 프로젝트의 General, Capabilities, Info tab의 정보들을 가지고 Info.plist를 생성합니다.
- 모든 앱은 info.plist를 가지고 있어야 합니다.
- 기본값이 포함되어 있지만 위에서의 경우처럼 대부분이 변경이나 추가가 필수적입니다.
- 탭을 통해 변경 및 추가를 하거나 직접 source에서 변경 및 추가를 할 수 있습니다.
- 때문에 새 프로젝트를 시작할 때 어떤 기능이 필요한지를 잘 고려해서 키를 추가해줘야 합니다.]



#### Declaring the Required Device Capabilities

* 모든 앱은 실행에 필요한 장치별 기능을 선언해야 합니다.
* ![스크린샷 2017-12-29 오전 1.06.00](/Users/yongjai/Desktop/스크린샷 2017-12-29 오전 1.06.00.png) 
* Array를 이용해서 키의 값을 지정하는 경우, 키가 있으면 해당 기능이 필요함을 나타내고, 키가 없으면 기능이 필요하지 않음을 나타내며, 없어도 애플리케이션이 실행됩니다.
* Dictionary를 이용해서 키의 값을 지정하는 경우, Boolean값을 이용해서 표시하는데  true값은 필수 기능을 나타내고 false값은 디바이스에 기능이 없음을 나타냅니다. 앱에서 특정 기능을 선택할 수 있는 경우 Dictionary에 해당 키를 포함하면 안 됩니다.(?)

####  **App Icons**

* 앱 아이콘은 Image Assets에 포함되어 있어야 합니다.
* 자세한 내용은 휴먼 디자인 가이드 확인..



#### App Launch (Default) Images

* 앱을 실행시켰을 때 잠깐 보이는 `정적인` 이미지로, 앱 화면을 구성하고 사용자에게 보여질 준비가 완료되면 사라집니다.
* 스냅샷을 사용할 수 있을 경우, launch image가 아닌 스냅샷을 이용하게 됩니다.
* 앱이 foreground(현재 실행 상태)에서 background로 들어갈 때는 스냅 샷이 생성되며 다시 foreground로 돌아가면 런칭 이미지가 아닌 스냅 샷을 활용합니다.
* 앱을 오랫동안 실행하지 않은 경우에는 스냅 샷을 삭제하고 기존의 런칭 이미지를 활용하게 됩니다.





### Supporting User Privacy

> 개인 정보 보호와 이를 사용하기 위한 앱 설계는 매우 중요합니다. 앱에는 앱을 사용하는 사용자가 외부에 공개하기 싫은 개인적인 정보들이 포함되어 있기 때문입니다. 개인 정보를 활용하려면 애플에서 명시한 룰을 지키고, 사용자의 동의를 얻은 뒤 진행해야 합니다.

* 사용자의 데이터를 필요로 하는 시점에 데이터 접근에 대한 엑세스를 요청해야 합니다. 
  * Info.plist에서 엑세스 하려는 데이터나 리소스가 앱에 필요한 이유를 작성할 수 있도록 도와줍니다.
* 데이터를 어떻게 사용할 것인지 사용자에게 공개할 수 있어야 합니다.
  * 앱을 배포할 때, 개인 정보 정책이나 성명에 대한 URL을 지정해야 합니다.
  * 앱 설명란에 요약해서 작성할 수도 있습니다.
* 사용자는 언제든지 접근 권한을 변경할 수 있습니다.
* 명확하게 필요하지도 않은 데이터에 대해 엑세스하려고 하지 말아야 합니다.
* 데이터를 보호할 수 있어야 합니다.
  * 로컬에 저장할 때는 디스크 암호화를 사용해서 암호화 된 형식으로 저장합니다.
  * 네트워크를 사용할 때는 App Transport Security를 사용합니다.
* 접근시 사용자의 권한이 필요한 것들
  * 블루투스, 카메라, 캘린더, 위치, 사진, 시리 등

```
앱에서 보호된 항목에 접근하려고 할 경우, 시스템은 사용자에게 엑세스 권한을 묻는 경고 메시지를 표시하게 됩니다. iOS 10부터는 Info.plist에서 엑세스시 alert에 보여줄 문자열을 포함시켜야 합니다. 만약 제공하지 않을 경우, 앱이 종료됩니다.
```



### Internationalizing Your App

> iOS 앱을 배포할 때, 한국에만 배포하는 것이 아닌 외국에도 배포할 수 있는데 이 때 해야하는 것을 지역화 및 국제화라는 것입니다.

* **Localization** vs **Internationalizing**
* Localization이 좀 더 작은 규모로 Internationalizing에 포함된다고 이해했습니다.
* Localization : 단순한 언어 차이를 만들어 주는 것(?)
* Internationalizing : 그 앱을 사용하는 사람의 지역과 문화를 고려해서 앱의 차이를 만들어 주는 것(?)
  * 예를 들어, 한국어로 나온 앱을 아랍어로 국제화 한다면 아랍어의 특성에 맞춰서 글자 간격이나 라벨의 크기 등을 바꿔줘야 합니다.
* 지역화 가능한 리소스 파일로 분리한 뒤에, 각 리소스를 언어 별 프로젝트라고 불리는 .lproj 디렉토리를 제공해야 합니다.
* 다음과 같은 것들을 지역화해야 합니다.
  * 문자열을 해당 국가의 언어로 바꿔줘야 합니다.
  * 오디오나 비디오도 해당 국가에 맞춰줘야 합니다.
  * 이미지와 같은 경우도 해당 국가에 맞춰줘야 합니다.

지역화 관련 WWDC 영상 : <https://developer.apple.com/videos/play/wwdc2016/201





# #2. The App Life Cycle

> 앱이라는 것은 개발자가 작성한 코드와 시스템 프레임워크 간의 정교한 상호작용이라고 말할 수 있습니다. 시스템 프레임워크를 통해 앱 실행에 필요한 기본 인프라가 제공되고, 개발자는 코드를 구현하게 됩니다. 이 둘 간의 효과적인 상호작용을 위해 iOS가 어떻게 작동하는지에 대한 원리를 이해할 필요가 있습니다.

### The Main Function

> iOS 앱은 main 함수로부터 모든게 시작됩니다. 다른 C 기반의 프로그램과 달리 iOS에서는 main 함수가 제공되며, 이를 수정하면 안됩니다.

```objective-c
#import <UIKit/UIKit.h>
#import "AppDelegate.h"

int main(int argc, char * argv[]) {

    @autoreleasepool {
        return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
    }
}
```



Main 함수가 하는 일은 UIKit에 제어권을 넘기는 일입니다. UIApplicationMain 함수는 앱의 핵심 객체를 만들고 사용 가능한 스토리보드에서 UI를 로드해서 초기 설정을 위한 코드를 실행시키고 앱의 실행 루프를 동작시킵니다.

### The Structure of an App

> UIApplicationMain 함수는 주요 객체를 설정하고 앱 실행을 시작합니다. 모든 iOS 앱의 중심에는 UIApplication 객체가 있으며, 이 객체는 해당 앱에서 시스템과 다른 객체 간의 상호작용을 할 수 있도록 도와줍니다.

* 주목할 점은 iOS 앱은 MVC 패턴을 사용한다는 점입니다. MVC 패턴은 앱의 비즈니스 로직과 이를 시각적으로 어떻게 보여줄지에 대한 부분을 분리합니다. 이는 화면 크기가 서로 다른 여러 기기에서 실행할 수 있는 앱을 만드는 데 매우 중요합니다.

![스크린샷 2017-12-29 오전 7.38.26](/Users/yongjai/Desktop/스크린샷 2017-12-29 오전 7.38.26.png) 

> 그림 2-1은 모든 앱에서 흔히 발견되는 객체를 보여줍니다.

- **UIApplication**

 UIApplication 객체는 이벤트 루프와 상위 레벨 앱 동작을 관리합니다. 또한 앱 전환 및 들어오는 푸시 알람과 같은 특수한 이벤트를 delegate에 전달합니다. UIApplication 객체는 subclassing 없이 그대로 사용할 수 있습니다. 

- **App delegate**

 App delegate는 UIApplication과 함께 앱 초기화, 상태 전환, 그리고 고급 이벤트들을 처리할 수 있습니다. 또한 app delegate는 모든 앱에 존재하는 것이 보장되는 유일한 객체이기 때문에 앱의 초기 데이터 구조를 설정하는 데 가끔 사용합니다.

**Document and Data Model**

 Data model 객체는 애플리케이션의 컨텐츠를 저장하며, 이는 앱에서만 한정됩니다. 또한 document 객체를 사용해서 일부 또는 전체 data model 객체를 관리할 수 있습니다. document 객체는 필수적인건 아니지만 파일이나 패키지에 속하는 데이터를 그룹화 할 수 있는 방법을 제공합니다.

**View controller **

 View controller 객체는 화면에서 컨텐츠의 presentation(보여주기?)을 관리합니다. View controller는 



### The Main Run Loop 

> 앱의 main run loop는 모든 사용자 관련 이벤트를 처리합니다. UIApplication 객체는 실행시 main run loop를 설정하고, 이를 사용해 이벤트를 처리하고 UI 업데이트를 처리합니다. 이름에서 알 수 있듯이, main run loop는 앱의 main thread에서 실행되며, 사용자 이벤트가 입력되면 순차적으로 처리합니다.

![스크린샷 2017-12-29 오전 8.08.36](/Users/yongjai/Desktop/스크린샷 2017-12-29 오전 8.08.36.png) 

> 그림 2-2는 main run loop의 구조와 사용자 이벤트 결과가 어떻게 생기는지를 보여줍니다. 

사용자가 기기와 상호작용을 하면서 이와 관련된 이벤트가 시스템에 의해 생성되고 UIKit이 설정한 특수한 포트를 통해 앱에 제공 됩니다. 이벤트는 이벤트 큐에 들어가 있게 되고 실행을 위해 하나씩 main run loop로 가게 됩니다. UIApplication 객체는 이벤트를 수신하고 수행할 작업을 결정하는 첫 번째 객체입니다. 

하터치 이벤트는 main window 객체로 보내지고, 다시 터치가 발생한 곳으로 가게 됩니다. 이 외에 다른 이벤트는 약간씩 다른 경로를 가지고 있을 수 있습니다. 

### Execution States for Apps

> iOS 앱은 항상 하나의 상태를 가지고 있습니다.

* **Not running** : 앱이 실행되지 않았거나 혹은 시스템에 의해 종료된 상태
* **Inactive** : 앱이 foreground에서 실행 중이지만, 일시적으로 이벤트를 받지 못하는 상태
* **Active** : 앱이 foreground에서 실행 중이며, 사용자 이벤트를 받을 수 있는 상태
* **Background** : 앱이 백그라운드에서 코드를 실행 중인 상태
* **Suspended** : 백그라운드에 있는 앱이 더 이상 코드를 실행하지 않는 정지된 상태

![스크린샷 2017-12-29 오전 8.23.35](/Users/yongjai/Desktop/스크린샷 2017-12-29 오전 8.23.35.png) 

iOS 시스템은 전체 시스템에서 발생하는 작업을 처리하며 해당 앱을 여러가지 상태로 이동시킵니다. 예를 들어 사용자가 홈 버튼을 누르거나 전화가 걸려오면 현재 실행 중인 앱이 비활성 상태로 변하게 됩니다.

- 대부분의 상태 전환에는 app delegate 메소드가 호출 됩니다. 메소드를 잘 사용하면 상태변화에 대응할 수 있습니다.
- **application:willFinishLaunchingWithOptions** : 앱에서 처음으로 코드를 실행 할 수 있습니다.
- **application:didFinishLaunchingWithOptions** : 사용자에게 앱의 화면이 보여지기 전에 마지막으로 초기화를 수행할 수 있습니다.
- **applicationDidBecomeActive** : 앱이 foreground로 전환되는 시점을 알려줍니다.
- **applicationWillResignActive** : 앱이 foreground 상태에서 inactive 되는 시점입니다.
- **applicationDidEnterBackground** : 앱이 background에서 실행되고 있으며, 언제든지 정지될수 있음을 알립니다.
- **applicationWillEnterForeground**: 앱이 background에서 foreground로 진입하고 있지만 아직 active 된 상태는 아닙니다.
- **applicationWillTerminate** : 앱이 종료되고 있음을 알리는 메소드 입니다. 하지만 앱이 시스템에 의해 정지되었을 경우에는 호출 되지 않습니다.





### App Termination

> 앱은 언제든지 종료될 수 있어야 하며, 종료하기 전에 사용자 정보를 저장하거나 다른 중요한 작업 수행을 위해 기다리지 않습니다. 시스템에 의한 앱 종료는 앱 수명 주기에서 정상적인 부분입니다. 보통은 시스템이 사용하지 않는 메모리를 회수하여 다른 앱을 실행 할 수 있는 공간을 확보하기 위해 종료하지만, 오작동을 하거나 앱이 적절하게 응답하지 않는 경우에도 앱을 종료 시킬 수 있습니다.

* 시스템에 의해 종료되면 앱은 종료에 대한 어떤 알림도 받지 않으며 프로세스가 중지되고 메모리가 회수됩니다.
* 사용자가 강제로 종료하는 경우도 같습니다.





### Threads and Concurrency

> 시스템은 앱의 main thread를 생성하고 추가적인 스레드를 생성해서 다른 작업을 수행할 수 있습니다. iOS의 경우, 스레드를 직접 만들고 관리하는 대신에 GCD, operation, async 프로그래밍 기법을 선호합니다. 

* GCD를 사용하면 수행할 작업과 작업의 순서를 정할 수 있지만 대체로 시스템이 정하도록 하는 것이 전반적인 성능을 향상시키고 코드를 심플하게 만들어 줍니다.
* Threads와 Concurrency를 사용할 때는 고려해야할 사항들이 있습니다.
  * 화면을 그리는 모든 작업들은 main thread에서 실행한다.
  * 네트워크 작업이나 파일에 접근하는 등 시간이 오래 걸리는 작업은 GCD를 이용해서 백그라운드에서 수행해야 합니다. 
    * 만약 서버에서 이미지를 가져오게 되는데 같은 스레드에서 작업을 실행하게 되면 이미지를 가져올 때까지 사용자는 아무것도 할 수 있는 것이 없게 됩니다. 