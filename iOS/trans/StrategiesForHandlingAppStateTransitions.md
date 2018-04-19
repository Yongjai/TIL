###4. Strategies for Handling App State Transitions

> 앱의 실행 상태에 따라 시스템은 어느 정도의 기대치를 가지고 있으며, 상태 전환이 발생하면 시스템이 app delegate에게 알림을 줍니다. `UIApplicationDelegate` 프로토콜을 이용해 앱의 상태 변화를 감지하고, 각 상황에 맞는 적절한 대응을 할 수 있습니다. 예를 들어 foreground에서 background로 전환할 때 저장되지 않은 데이터를 기록하고, 진행중인 작업을 중지 할 수 있습니다. 여기서는 상태 전환 코드를 어떻게 구현하면 좋을지에 대한 내용을 다루고 있습니다.



### What to Do at Launch Time

> 앱이 실행되면 app delegate의  `application:willFinishLaunchingWithOptions:` 메소드와 `application:didFinishLaunchingWithOptions:` 메소드를 사용해서 다음과 같은 일을 할 수 있습니다.

* 앱이 시작된 이유에 대한 정보를 확인하고, 적절하게 대응할 수 있습니다.
* 앱의 중요한 데이터 구조를 초기화할 수 있습니다.
* 앱에서 보여줄 window와 view를 준비할 수 있습니다.
  * OpenGL ES를 이용하는 앱은 위 메소드를 사용하면 안 됩니다. 대신에  `applicationDidBecomeActive:` 메소드를 사용합니다.
  * 앱의 window는  `application:willFinishLaunchingWithOptions:` 메소드를 이용해서 보여줍니다. UIKit은 `application:didFinishLaunchingWithOptions:` 메소드가 리턴되기 전까지 윈도우가 보이도록 지연시킬 수 있습니다.



앱이 시작되는 시점에 시스템은 자동으로 앱의 main storyboard와 initial view controller를 로드합니다. State restoration(상태 복원)을 지원하는 앱은  `willFinishLaunchingWithOptions :` , `application : didFinishLaunchingWithOptions :` 두 가지 메소드를 이용해 이전 상태 인터페이스를 복원합니다.

`application : willFinishLaunchingWithOptions :` 메소드를 사용해서 앱 window를 띄우고, 상태 복원이 일어날지 아닐지 결정해야 합니다. `application : didFinishLaunchingWithOptions :` 메소드를 사용해서 앱의 UI를 마지막으로 조정합니다.

위에서 언급했던 두 메소드인 `application:willFinishLaunchingWithOptions:`과`application:didFinishLaunchingWithOptions:`에서 실행되는 작업은 가능한 가볍게해서 앱 시작 시간을 줄여야 합니다. 앱은 실행, 초기화, 이벤트 처리를 5초 이내에 완료되어야 합니다. 이 작업이 너무 오래 걸리면 시스템이 응답이 없음으로 간주하고 앱을 종료시킬 수 있습니다. 따라서 네트워크 엑세스와 같이 실행 속도를 느려지게 만드는 작업은 보조 스레드에서 실행 되어야 합니다.



#### The Launch Cycle

> 앱이 실행되면, 실행이 되지 않던 상태에서 active 또는 background 상태로 이동한 뒤, inactive 상태를 통해 전환됩니다. Launch cycle의 일부로, 시스템은 앱의 프로세스와 메인 스레드를 만들고 메인 스레드에서 main function을 호출합니다. Xcode 프로젝트와 제공되는 main function은 UIKit 프레임워크를 제어하고, 앱이 구동되는데 필요한 초기화하고 실행하는 데 필요한 작업을 해줍니다.



다음 그림은 호출된 app delegate 메소드를 포함해서 앱이 foreground로 들어가며 실행될 때 발생하는 이벤트 시퀀스 입니다. 

![1](https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Art/app_launch_fg_2x.png)



일반적으로 특정 유형의 백그라운드 이벤트를 처리하기 위한 경우 앱이 백그라운드로 들어가며 실행이 되는데 그 실행 주기는 다음 그림과 같습니다. 앱이 background 상태로 시작할때는 foreground 상태로 시작될때와 실행주기에 약간의 차이가 생기는데, 가장 큰 차이점은 작업을 처리한 뒤, 정지(suspend) 될 수 있다는 점입니다. 백그라운 상태로 실행될 때에도 시스템은 UI 파일을 로드하지만, window에 띄우지 않습니다.



![2](https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Art/app_launch_bg_2x.png)



앱이 어느 상태로 시작되는지 확인하려면 `application:willFinishLaunchingWithOptions:` 메소드 또는 `application:didFinishLaunchingWithOptions:` 메소드 안에 있는 UIApplication 객체의 `applicationState` 속성 값을 확인해 보면 알 수 있습니다.

앱이 foreground로 시작되면 이 속성에는 UIApplicationStateInactive값을 포함합니다. 앱이 background로 시작되면 속성에 UIApplicationStateBackground 값이 포함됩니다. 이 차이를 이용해서 delegate 메소드가 실행될 때 어떤 동작을 할지 적절하게 조정할 수 있습니다.



#### Launching in Landscape Mode

> 앱에서 가로 모드만 사용하려면 시스템에 이를 요청해야 합니다. 일반적으로 앱은 세로 모드로 실행이 되어 필요에 따라 기기 방향에 맞춰서 화면을 회전시킵니다. 가로, 세로 모드를 둘 다 지원하려면 항상 세로에 대한 뷰를 구성하고 난 뒤에 회전을 처리해야 합니다. 하지만 가로 모드만 지원하고 싶을 때가 있습니다. 

- Info.plist에 UIInterfaceOrientation key를 추가하고 값을 `UIInterfaceOrientationLandscapeLeft` `UIInterfaceOrientationLandscapeRight` 둘 중 하나로 설정합니다.
- layout이나 autosizing 옵션이 설정되어있는지 확인해야 합니다.
- `shouldAutorotateToInterfaceOrientation:` 메소드가 true를 반환하도록 합니다. (세로모드일 경우 false)

**앱은 윈도우 기반 컨텐츠를 관리하기 위해 view controller를 사용해야 합니다.**

Info.plist파일의 UIInterfaceSrientation Key는 iOS에게 관리되는 실행되는 뷰의 방향을 기준으로 앱 상태 표시줄(표시된 경우)의 방향을 구성해야 한다는 것을 알립니다. View controller는 이 키를 존중하며 이 키의 초기 방향을 일치하도록 설정합니다. 이 키를 사용하는 것은 `applicationDidFinishLaunching :` 메소드의 실행 초기에 UIApplication의 `setStatusBarOrientation : animated :` 메소드를 호출하는 것과 같습니다.



#### Installing App-Specific Data Files at First Launch

> 앱의 구동에 요구되는 데이터나 설정 값 셋팅을 위해 첫 실행 주기(first launch cycle)를 이용할 수 있습니다. 앱의 특정 데이터 파일은 Library/Application Support/directory/bundleID/app sandbox에 위치해야 합니다.  앱 번들 내에 있는 파일은 code signed 되어 있어 수정을 할 수 없기 때문에 만약 수정이 필요한 경우에는 반드시 해당 파일을 샌드박스의 다른 폴더로 복사한 다음 수정해야합니다.

* bundleID는 앱의 bundle 식별자를 의미합니다. 이 디렉토리를 세분화해서 필요에 따라 데이터 파일을 구성할 수 있습니다. 또한 App의 iCloud container 디렉토리나 local document 디렉토리와 같은 다른 디렉토리에 파일을 만들 수도 있습니다.
* App bundle에서 파일을 수정하면 앱의 서명이 무효화 되고 앱이 실행되지 않습니다.

---



## What to Do When Your App Is Interrupted Temporarily

> 시스템 알림이 전달되면 앱은 일시적으로 제어권을 잃게 되고, 사용자 터치 이벤트를 받아 들이지 않습니다. 이러한 상황을 대비해 앱은 `applicationWillResignActive` 메소드에서 아래와 같은 작업을 수행해야 합니다.

* 데이터 및 상태 정보를 저장합니다.


* 타이머 및 기타 주기적인 작업을 중지합니다.
* 실행중인 메타데이터 쿼리를 모두 중지합니다.
* 새로운 작업을 시작하지 않도록 합니다.
* 동영상 재생을 일시적으로 중지합니다. (AirPlay를 통한 재생은 제외)
* OpenGL ES Framework 속도를 재조절합니다.
* 중요하지 않은 코드를 실행하는 모든 dispatch queue 또는 operation queue를 일시적으로 중단합니다. (비활성 상태에서는 네트워크 요청 및 시간에 민감한 백그라운드 작업을 계속 처리할 수 있습니다.)

앱이 일시 정지 상태에서 벗어나 다시 활성 상태로 되돌아 오면 `applicationWillResignActive:` 메소드에서 했던 작업들을 `applicationDidBecomeActive:` 메소드에서 되돌려야 합니다. 따라서 다시 활성 상태가 되면 타이머를 다시 시작하고 dispatch queue를 다시 시작하고 OpenGL ES 프레임 속도를 다시 조절해야합니다. 그러나 게임은 사용자가 다시 시작할 때까지 일시 중지 상태를 유지해야합니다.

만약 앱에 `NSFileProtectionComplete` 옵션으로 보호된 파일이 있다면 사용자가 Sleep/Wake 버튼을 눌렀을 때해당 파일에 대한 참조를 모두 닫아야 합니다. 적절한 암호로 구성된 기기는 Sleep/Watch 버튼을 누르면 화면이 잠기고 완전한 보호 기능이 설정된 파일의 암호 해독 키가 삭제되도록 할 수 있습니다. 화면이 잠겨있는 동안 해당 파일에 접근하려는 시도는 실패합니다. 따라서 이러한 파일이 있다면 `applicationWillResignActive:` 메소드에서 해당 파일에 대한 참조를 닫아줘야 하며, `applicationDidBecomeActive :` 메소드에서 새 참조를 열어야합니다.



**사용자 데이터는 항상 앱의 적절한 check point에 저장해야 합니다. App의 상태 전환을 사용해서 저장되지 않은 객체의 변경 사항을 디스크에 쓰도록 할 수도 있지만, 앱 상태 전환이 데이터를 저장할 때까지 기다리면 안됩니다. 예를 들어 사용자 데이터를 관리하는 view controller는 해제할 때 데이터를 저장해야 합니다.**



#### Responding to Temporary Interruptions

> 전화가 걸려오는 것과 같이 alert-based interruption이 발생하면 앱은 일시적으로 비활성 상태로 전환됩니다. 이 경우 시스템은 사용자에게 진행 방법을 알려줄 수 있습니다(?) 사용자가 해당 알림을 닫을 때까지 앱은 비활성 상태가 유지됩니다. 알림을 닫는 시점에 앱은 활성 상태로 돌아가거나 백그라운드 상태로 이동합니다. 

다음 그림은 알림 인터럽트가 발생했을 때, 앱의 이벤트 흐름을 보여줍니다.

![3](https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Art/app_interruptions_2x.png)



배너를 표시하는 알림은 위의 경우와 다른 방식으로 비활성화 됩니다. 배너는 앱의 상단 가장 자리에 배치되며 앱은 터치 이벤트를 계속 받을 수 있습니다. 하지만 사용자가 배너를 내리게 되면 alert-based interruption처럼 앱이 비활성 상태로 이동하게 됩니다. 사용자가 notification center를 닫거나 다른 앱을 시작하지 않는 이상 앱은 비활성 상태로 유지됩니다. 이 때, 앱은 적절한 활성 상태 또는 백그라운드로 이동합니다. 사용자는 설정을 통해 배너 표시와 경고 표시를 사용할 수 있습니다.

Sleep/Wake 버튼을 누르면 앱이 일시적으로 비활성화 될 수 있습니다. 사용자가 이 버튼을 누르면 시스템은 터치 이벤트를 비활성화하고 앱을 백그라운드로 이동시킨 뒤 applicationState 속성 값을 UIApplicationStateBackground로 설정하고 화면을 잠궈버립니다. 잠긴 화면은 데이터 보호를 사용해서 파일을 암호화해서 앱에 추가적인 영향을 줍니다. 이러한 영향들이 무엇이 있는지는 밑에서 나옵니다.

----



## What to Do When Your App Enters the Foreground

> Foreground로 들어가면 백그라운드 상태로 이동하면서 중지된 작업을 재개할 수 있습니다. `applicationDidEnterBackground :` 메소드에서 수행 된 작업을 `applicationWillEnterForeground` 메소드에서 실행 취소 해야하며 `applicationDidBecomeActive:` 메소드는 실행 때와 동일한 작업을 계속 수행할 수 있도록 해야합니다.



다음 그림은 background에서 foreground로 이동하는 걸 보여주는 그림입니다.

![4](https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Art/app_enter_foreground_2x.png)



UIApplicationWillEnterForegroundNotification 알림은 앱이 foreground에 다시 들어 올때 추적할 수 있습니다. 앱 객체는 default notification center를 사용해서 알림에 등록할 수 있습니다.



#### Be Prepared to Process Queued Notifications

> 정지 상태의 앱은 foreground 또는 background 실행 상태가 될 때, 대기 중인 알림을 처리할 준비가 되어야 합니다. 앱이 정지되어 있는 동안에는 앱이 코드를 실행하지 못하므로, 시스템이 관련 알림을 대기열에 추가하고 코드 실행이 시작되는 순간 앱에 알림을 전달합니다. 앱이 다시 시작될 때, 알림으로 바빠지게 되는 것을 막기 위해 시스템은 이벤트를 각 유형별로 합쳐서 앱이 중단된 이후의 변경을 반영합니다.

다음 표에는 이벤트를 합쳐 앱에 전달할 수 있는 알림들이 나열되어 있습니다. 이러한 알림의 대부분은 등록된 옵저버에게 직접 전달됩니다. 기기 방향 변경과 관련된 일부는 일반적으로 시스템 프레임 워크에 의해 차단되어 다른 방식으로 앱에 전달됩니다.

| Event                  | Notifications                            |
| ---------------------- | ---------------------------------------- |
| 액세서리 연결 등록 / 해제        | `EAAccessoryDidConnectNotification` `EAAccessoryDidDisconnectNotification` |
| 기기 화면 방향 전환            | `UIDeviceOrientationDidChangeNotification` 이 알림 외에도 view controller는  인터페이스 방향을 자동으로 업데이트 합니다. |
| 큰 시간 변화                | `UIApplicationSignificantTimeChangeNotification` |
| 배터리 상태 및 잔량 변화         | `UIDeviceBatteryLevelDidChangeNotification` `UIDeviceBatteryStateDidChangeNotification` |
| 근접 상태 변화               | `UIDeviceProximityStateDidChangeNotification` |
| 보호된 파일의 상태 변화          | `UIApplicationProtectedDataWillBecomeUnavailable` `UIApplicationProtectedDataDidBecomeAvailable` |
| 외부 화면 연결 / 해제          | `UIScreenDidConnectNotification` `UIScreenDidDisconnectNotification` |
| 화면의 스크린 모드 변화          | `UIScreenModeDidChangeNotification`      |
| 설정에서 변경 가능한 앱 설정 값의 변화 | `NSUserDefaultsDidChangeNotification`    |
| 사용 언어 및 지역 변경          | `NSCurrentLocaleDidChangeNotification`   |
| 사용자 iCloud 계정 변경       | `NSUbiquityIdentityDidChangeNotification` |

- 대기 중인 알림(queued notification)은 사용자 터치나 입력 전에 앱의 main run loop로 전달됩니다. 앱이 재개될 때 지연이 발생하지 않도록 이러한 이벤트를 빠르게 처리 할 수 있어야 합니다. 그러나 백그라운드 상태에서 돌아 왔을 때 앱이 느리게 나타나는 경우 Instruments를 사용하여 알림 핸들러 코드가 지연을 유발하는 건지 확인해야 합니다.
- Foreground로 돌아오는 앱은 마지막 업데이트 이후 dirty로 표시된 모든 뷰에 대해 view-update 알림을 받습니다. 백그라운드에서 실행중인 앱은 `setNeedsDisplay` 또는 `setNeedsDisplayInRect :` 메소드를 호출하여 뷰에 대한 업데이트를 요청할 수 있습니다. 그러나 뷰가 표시되지 않기 때문에 시스템은 요청을 합치고 앱이 foreground로 돌아온 후에만 뷰를 업데이트합니다.





#### Handle iCloud Changes

> iCloud 상태 변화에 따라 앱은 iCloud 관련 UI를 업데이트하거나, 관련 문서에 대한 참조 상태를 변경해야 합니다. 앱에서 iCloud 사용 여부를 묻는 메시지가 이미 표시되는 경우, iCloud 상태가 변경되었다고 해서 다시 메시지를 띄우면 안 됩니다. 관련된 설정은 앱 환경 설정이나 Settings에서 사용자가 지정 할 수 있게 해야 합니다. 그러면 설정 번들을 이용하거나 앱에서 옵션으로 해당 설정을 표시 할 수 있습니다.

* iCloud가 어떤 이유든 상태가 변경되면 시스템은 앱에 NSUbiquityIdentityDidChangeNotification 알림을 전달합니다. 사용자가 iCloud 계정에 로그인, 로그아웃하거나 문서와 데이터의 동기화를 활성화 또는 비활성화하면 iCloud의 상태가 변경됩니다. 이 알림은 변경 사항을 수용하기 위해 캐시와 iCloud 관련 사용자 인터페이스 요소를 업데이트하는 앱의 단서입니다. 예를 들어, 사용자가 iCloud에서 로그아웃하면 모든 iCloud 기반 파일 또는 데이터에 대한 참조를 제거해야합니다.
* 설정이 현재 사용자 기본 데이터베이스에 없는 경우가 아니라면 prompt를 반복하면 안됩니다.





#### Handle Locale Changes

> 앱이 일시 중지 된 상태에서 사용자가 지역을 변경하면 NSCurrentLocaleDidChangeNotification 알림을 사용하여 앱이 foreground로 돌아갈 때 날짜, 시간 및 숫자와 같은 지역에 민감한 정보를 포함하는 모든 뷰를 강제로 업데이트 할 수 있습니다. 

* 지역 관련 문제를 피하는 가장 좋은 방법은 뷰를 쉽게 업데이트 할 수 있게 코드를 작성하는 것 입니다. 예를 들어, NSLocale 객체를 검색할 때 `autoupdatingCurrentLocale Class method`를 사용하면 변경에 맞춰 자동으로 갱신되는 locale 객체를 돌려줍니다. 그러나 지역이 변경되면 현재 지역에서 생긴 콘텐츠가 포함 된 뷰를 새로 고쳐야합니다.
* 현재 지역 정보가 변경 될 때마다 캐시된 날짜 및 숫자 formatter 객체를 만듭니다. 지역 변경을 위한 자세한 내용은  *Internationalization and Localization Guide*를 참고하면 됩니다.





#### Handle Changes to Your App’s Settings

> Settings를 통해 관리되는 설정 값이 있다면 `NSUserDefaultsDidChangeNotification` 알림을 확인해야 합니다. 앱이 일시 정지되거나 백그라운드 상태일 때도 설정을 수정할 수 있으므로 해당 알림을 사용하면 설정 변경에 따른 적정한 응답을 할 수 있습니다. 

* 경우에 따라 해당 알림에 응답하는 것은 잠재적인 보안 허점을 줄일 수 있습니다. 예를 들어, 이메일은 사용자 계정 변경에 응답해야 합니다. 이러한 변경 사항을 인지하지 못하면 개인 정보 또는 보안 문제가 발생할 수 있습니다. 특히 계정이 현재 사용자에게 속하지 않아도 이전 계정 정보를 사용해서 메일을 보낼 수 있습니다.
* NSUserDefaultsDidChangeNotification 알림을 받으면 앱은 관련 설정을 다시 로드하고 필요한 경우 UI를 재설정해야합니다. 암호 또는 기타 보안 관련 정보가 변경된 경우 이전에 표시된 정보를 숨기고 사용자가 새 암호를 입력하도록 해야합니다.

---



## What to Do When Your App Enters the Background

> Foreground에서 backgroun로 이동할 때는 앱 델리게이트의 `applicationDidEnterBackground :` 메소드를 사용하여 다음을 수행해야 합니다.

* **스냅샷을 찍을 준비를 합니다.**
  * `applicationDidEnterBackground :` 메서드가 반환되면 시스템은 앱의 사용자 인터페이스를 캡처하여 캡쳐한 이미지를 transition 애니메이션으로 사용합니다. 만약 어떤 뷰에 중요한 정보가 포함되어 있다면 `applicationDidEnterBackground :` 메서드가 반환되기 전에 해당 뷰를 숨기거나 수정해야 합니다. 이 프로세스의 일부로 뷰 계층 구조에 새 뷰를 추가하는 경우 앱 스냅 샷 준비에서 설명한대로 뷰를 강제로 가져와야합니다.
* **앱 상태 정보를 저장합니다.**
  * 백드라운드로 들어가 전에 앱은 중요한 사용자 데이터를 모두 저장해 둡니다. 앱 상태에 대한 마지막 변경 사항을 저장하려면 백그라운드 전환을 이용하면 됩니다.
* **필요에 따라 메모리를 확보해야 합니다.**
  * 필요없는 캐시 데이터를 릴리스하고 간단한 정리를 통해서 앱의 메모리 사용량을 줄일 수 있습니다. 메모리 공간이 큰 앱은 시스템에 의해 가장 먼저 종료되기 때문에 이미지 리소스, 데이터 캐시 및 더 이상 필요하지 않은 객체들을 릴리스 해야 합니다.

`applicationDidEnterBackground:` 메소드는 약 5초 동안 모든 작업을 수행하게 되는데, 작업 수행 이내에 메소드가 return 되지 않으면 앱은 종료되고 메모리에서 제거됩니다. 작업 수행에 더 많은 시간이 필요한 경우 `beginBackorundTaskWithExpirationHandler:` 메소드를 호출하여 백그라운드 실행 시간을 요청한 다음, 보조 스레드에서 장시간 작업을 수행합니다. 백그라운드 작업을 시작하는지 여부에 관계없이 `applicationDidEnterBackground:` 메소드는 5초 이내에 종료되어야 합니다.

시스템은 `applicationDidEnterBackground :` 메소드를 호출하는 것 외에도 UIApplicationDidEnterBackgroundNotification 알림을 보냅니다. 이 알림을 이용해서 앱의 다른 객체에게 정리 작업을 나눠줄 수 있습니다.



#### The Background Transition Cycle

> 사용자가 홈 버튼을 누르거나 Sleep/Wake 버튼을 누르거나 시스템이 다른 앱을 실행 시킬 때, foreground에 있던 앱은 비활성 상태(inactive state)로 전환되고 백그라운드 상태로 들어갑니다. 이러한 상태 변화는 app delegate의  `applicationWillResignActive:` 와 `applicationDidEnterBackground:` 메서드를 호출하게 됩니다. 대부분의 앱은 `applicationDidEnterBackground:` 메소드가 호출 되고, 잠시 후 일시 중지 상태로 전환됩니다. 음악 재생과 같이 특정한 백그라운드 작업을 요청하거나 시스템이 약간의 추가 실행시간을 요청하는 앱은 더 오랫동안 계속 실행될 수 있습니다.



다음 그림은 `applicationWillResignActive:` 와 `applicationDidEnterBackground:` 메서드를 호출할 때 어떻게 되는지를 보여주는 그림입니다.

![5](https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Art/app_bg_life_cycle_2x.png)



#### Prepare for the App Snapshot

> app delegate의 `applicationDidEnterBackground:` 메소드가 반환 되고 잠시 후 시스템은 앱의 화면을 스냅샷을 촬영합니다. 비슷하게 백그라운드 작업 수행이 진행되고 화면에 변화가 생기면, 시스템은 스냅샷을 찍어 이를 반영합니다. 

앱이 다운로드 된 항목을 처리하기 위해 실행되면 시스템은 항목의 통합으로 인해 변경된 모든 사항을 반영 할 수 있도록 새로운 스냅 샷을 찍습니다. 시스템은 multitasking UI에서 이러한 스냅 샷 이미지를 사용하여 앱 상태를 보여줍니다. 

백그라운드에 진입할때 view에 변화를 주는 경우, `snapshotViewAfterScreenUpdates:` 메소드를 호출하여 변경 사항을 렌더링 할 수 있습니다. 뷰에서 `setNeedsDisplay` 메서드를 호출하면 스냅샷이 다음 drawing cycle 전에 수행되므로 변경 내용이 렌더링되지 않아 스냅샷에 아무 효과를 주지 못합니다.`snapshotViewAfterScreenUpdates:` 메소드의 매개변수를 `true` 값으로 설정하면 강제로 스냅샷을 업데이트 합니다.



#### Reduce Your Memory Footprint

> 모든 앱은 백그라운드로 들어갈 때 적당한 메모리를 확보해야 합니다. 시스템은 가능한 많은 앱을 메모리에 유지하려고 하지만 메모리가 부족하게 되면 일시 중지 된 앱을 종료해서 해당 메모리를 회수합니다. 위에서 말했듯이 메모리를 많이 사용하는 앱은 종료 1순위 입니다.

앱은 더 이상 필요없는 객체에 대해 강한 참조를 제거해야 합니다. 강한 참조를 제거하면 컴파일러는 해당 메모리를 다시 확보할 수 있도록 객체를 즉시 해제할 수 있습니다. 그러나 일부 객체의 성능을 높이기 위해 캐시하는 경우에는 앱이 백그라운드로 전활될 때까지 기다린 후에 해당 객체에 대한 참조를 제거할 수 있습니다.

가능한 빨리 강한 참조를 해제해야 하는 객체에는 아래와 같은 예가 있습니다.

- 생성한 이미지 객체
- 디스크에서 다시 로드할 수 있는 대형 미디어 또는 데이터 파일
- 앱에 당장 필요하지 않으며, 나중에 쉽게 다시 만들 수 있는 모든 객체



앱의 메모리 사용량을 줄이기 위해 앱이 백그라운드로 이동할 때 앱은 자동으로 일부 데이터를 제거합니다.

* 시스템이 모든 Core Animation layer에 대한 백업 저장소를 제거합니다. 이러한 행위로 앱의 레이어 객체가 메모리에서 제거되거나 현재 레이어 속성이 변하지 않습니다. 단순하게 화면에 해당 레이어의 내용이 표시되는 것을 막을 수 있습니다.  
* 캐시 된 이미지에 대한 시스템 참조를 제거합니다.
* 다른 시스템이 관리하는 데이터 캐시에 대한 강한 참조를 제거합니다.



출처 : https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/StrategiesforHandlingAppStateTransitions/StrategiesforHandlingAppStateTransitions.html