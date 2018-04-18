# #2. The App Life Cycle

> 앱이라는 것은 개발자가 작성한 코드와 시스템 프레임워크 간의 정교한 상호작용이라고 말할 수 있습니다. 시스템 프레임워크를 통해 앱 실행에 필요한 기본 인프라가 제공되고, 개발자는 코드를 구현하게 됩니다. 이 둘 간의 효과적인 상호작용을 위해 iOS가 어떻게 작동하는지에 대한 원리를 이해할 필요가 있습니다.*

### The Main Function

> * iOS 앱은 main 함수로부터 모든게 시작됩니다. 다른 C 기반의 프로그램과 달리 iOS에서는 main 함수가 제공되며, 이를 수정하면 안됩니다.*

```objective-c
import <UIKit/UIKit.h>

#import "AppDelegate.h"

int main(int argc, char * argv[]) {

    @autoreleasepool {

        return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
    }
}
```

Main 함수가 하는 일은 UIKit에 제어권을 넘기는 일입니다. UIApplicationMain 함수는 앱의 핵심 객체를 만들고 사용 가능한 스토리보드에서 UI를 로드해서 초기 설정을 위한 코드를 실행시키고 앱의 실행 루프를 동작시킵니다.

### The Structure of an App

> UIApplicationMain 함수는 주요 객체를 설정하고 앱 실행을 시작합니다. 모든 iOS 앱의 중심에는 UIApplication 객체가 있으며, 이 객체는 해당 앱에서 시스템과 다른 객체 간의 상호작용을 할 수 있도록 도와줍니다.*

* 주목할 점은 iOS 앱은 MVC 패턴을 사용한다는 점입니다. MVC 패턴은 앱의 비즈니스 로직과 이를 시각적으로 어떻게 보여줄지에 대한 부분을 분리합니다. 이는 화면 크기가 서로 다른 여러 기기에서 실행할 수 있는 앱을 만드는 데 매우 중요합니다.

![1](https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Art/core_objects_2x.png) 

> 그림 2-1은 모든 앱에서 흔히 발견되는 객체를 보여줍니다.*



**UIApplication**

 UIApplication 객체는 이벤트 루프와 상위 레벨 앱 동작을 관리합니다. 또한 앱 전환 및 들어오는 푸시 알람과 같은 특수한 이벤트를 delegate에 전달합니다. UIApplication 객체는 subclassing 없이 그대로 사용할 수 있습니다. 

**App delegate**

 App delegate는 UIApplication과 함께 앱 초기화, 상태 전환, 그리고 고급 이벤트들을 처리할 수 있습니다. 또한 app delegate는 모든 앱에 존재하는 것이 보장되는 유일한 객체이기 때문에 앱의 초기 데이터 구조를 설정하는 데 가끔 사용합니다.

**Document and Data Model**

 Data model 객체는 애플리케이션의 컨텐츠를 저장하며, 이는 앱에서만 한정됩니다. 또한 document 객체를 사용해서 일부 또는 전체 data model 객체를 관리할 수 있습니다. document 객체는 필수적인건 아니지만 파일이나 패키지에 속하는 데이터를 그룹화 할 수 있는 방법을 제공합니다.

**View controller **

 View controller 객체는 화면에서 컨텐츠의 presentation(보여주기?)을 관리합니다. View controller는 

### The Main Run Loop 

> 앱의 main run loop는 모든 사용자 관련 이벤트를 처리합니다. UIApplication 객체는 실행시 main run loop를 설정하고, 이를 사용해 이벤트를 처리하고 UI 업데이트를 처리합니다. 이름에서 알 수 있듯이, main run loop는 앱의 main thread에서 실행되며, 사용자 이벤트가 입력되면 순차적으로 처리합니다.*

![2](https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Art/event_draw_cycle_a_2x.png) 

> 그림 2-2는 main run loop의 구조와 사용자 이벤트 결과가 어떻게 생기는지를 보여줍니다. 

사용자가 기기와 상호작용을 하면서 이와 관련된 이벤트가 시스템에 의해 생성되고 UIKit이 설정한 특수한 포트를 통해 앱에 제공 됩니다. 이벤트는 이벤트 큐에 들어가 있게 되고 실행을 위해 하나씩 main run loop로 가게 됩니다. UIApplication 객체는 이벤트를 수신하고 수행할 작업을 결정하는 첫 번째 객체입니다. 

터치 이벤트는 main window 객체로 보내지고, 다시 터치가 발생한 곳으로 가게 됩니다. 이 외에 다른 이벤트는 약간씩 다른 경로를 가지고 있을 수 있습니다. 

### Execution States for Apps

> iOS 앱은 항상 하나의 상태를 가지고 있습니다.

* **Not running** : 앱이 실행되지 않았거나 혹은 시스템에 의해 종료된 상태


* **Inactive** : 앱이 foreground에서 실행 중이지만, 일시적으로 이벤트를 받지 못하는 상태
* **Active** : 앱이 foreground에서 실행 중이며, 사용자 이벤트를 받을 수 있는 상태
* **Background** : 앱이 백그라운드에서 코드를 실행 중인 상태
* **Suspended** : 백그라운드에 있는 앱이 더 이상 코드를 실행하지 않는 정지된 상태

![3](https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Art/high_level_flow_2x.png) 



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



출처 : https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/TheAppLifeCycle/TheAppLifeCycle.html#//apple_ref/doc/uid/TP40007072-CH2-SW1