## 3. Background Execution

> 사용자가 앱을 사용하지 않을 경우, 시스템은 앱을 백그라운드 상태로 전환시키게 됩니다. 대부분의 앱에서 백그라운드 상태라는 것은 suspended 상태가 되어가는 상태라고 말할 수 있습니다. 앱을 정지시키면 배터리 수명을 향상 시키며, 다른 앱이 foreground에서 실행 될수 있는 리소스를 제공해줍니다. 하지만 모든 앱이 백그라운드에서 정지되는 것은 아닙니다. 예를 들어 하이킹 앱은 백그라운드에서도 사용자의 위치를 추적해야하며, 오디오 앱은 잠금화면에서도 계속 음악을 재생할 수 있어야 합니다.

백그라운드 상태에서도 앱을 실행시켜야 할 경우, iOS는 시스템 리소스나 배터리를 소모하지 않고 효율적으로 실행할 수 있도록 도와줍니다.

* Foreground에서 짧은 작업을 하는 앱은 백그라운드로 전환될때 해당 작업을 완료할 시간을 요청할 수 있습니다.
* Foreground에서 다운로드를 시작하는 앱은 다운로드 관리를 시스템에 맡길 수 있기 때문에 다운로드가 되는 동안 앱이 중지되거나 종료되지 않습니다.
* Background에서 특정 작업을 실행해야 하는 앱은 하나 이상의 백그라운드 실행 모드에 대한 지원을 선언 할 수 있습니다.

전체적인 사용자의 환경을 개선하기 위해 background에서의 작업 수행이 필요하지 않다면 항상 피해야 합니다. 사용자가 다른 앱을 실행하거나 기기를 잠그거나 당장 사용하지 않는다면 앱은 background로 들어가게 됩니다. 이러한 상황들은 앱이 당장 필요하지 않다는 신호를 보내고 있는 것입니다. 이러한 조건에서 계속 앱을 실행시키게 되면 기기 배터리가 소모되어 사용자가 앱을 종료할 수 있으므로 background에서 무엇을 해야하는 지에 대해 명심하고 피하는 것이 좋습니다.

---



### Executing Finite-Length Tasks

> 백그라운드로 이동하는 앱은 최대한 빨리 시스템에 의해 suspended 되기 위해 실행이 중단된 상태로 놓여지게 됩니다. 만약 앱이 작업을 진행하고 있거나 작업을 마무리하기 위해 시간이 더 필요하다면 UIApplication의 객체인 `beginBackgroundTaskWithName:expirationHandler:` or `beginBackgroundTaskWithExpirationHandler:`를 이용해서 추가적인 실행 시간을 요청할 수 있습니다. 두 메소드 중 하나를 호출하면 앱은 일시적으로 중지가 지연되고, 작업을 끝마칠 수 있는 시간이 주어집니다. 작업이 끝났다면 `endBackgroundTask:`메소드를 호출하여 시스템에게 작업이 끝났음을 알려주어 suspended 될 수 있도록 해야 합니다.

`beginBackgroundTaskWithName : expirationHandler :` 또는 `beginBackgroundTaskWithExpirationHandler :` 메소드를 호출 할 때마다 해당 작업과 연관된 고유 한 토큰이 생성됩니다. 앱이 작업을 완료하면 해당 토큰과 함께 `endBackgroundTask :` 메소드를 호출하여 작업이 완료되었음을 시스템에 알립니다. 백그라운드 작업에 대한 `endBackgroundTask :` 메소드를 호출하지 않으면 앱이 종료됩니다. 작업을 시작할 때 만기 핸들러를 제공하면, 시스템은 해당 핸들러를 호출하여 작업을 끝내고 앱이 종료되는 것을 막을 수 있는 기회를 제공합니다.



백그라운드에서의 작업을 지정하기 위해 앱이 백그라운드로 이동할 때까지 기다릴 필요가 없습니다. 작업을 시작하기 전에 `beginBackgroundTaskWithName : expirationHandler :` 또는 `beginBackgroundTaskWithExpirationHandler :` 메소드를 호출한 뒤에 작업이 끝나자 마자 `endBackgroundTask :` 메소드를 호출하면 됩니다. 앱이 foreground에서 실행될 때에도 사용할 수 있습니다.



아래 코드는 앱이 백그라운드로 들어갈 때 시간이 긴 작업을 어떻게 시작하는 지를 보여줍니다. 이 예제에서 백그라운드 작업을 시작하는 요청에 작업이 오래 걸리는 경우에 대비해 expiration 핸들러가 포함되어 있습니다. 그리고 나서 이 작업은 비동기 실행을 위해 dispatch queue에 들어가고 `applicationDidEnterBackground ` 메소드를 정상적으로 리턴 됩니다. 블록을 사용하면 백그라운드 작업 식별자와 같은 중요한 변수에 대한 참조를 유지하는 데 필요한 코드가 간단해집니다. bgTask 변수는 현재 백그라운드 작업 식별자에 대한 포인터를 저장하고 이 메소드에서 사용되기 전에 초기화되는 클래스의 멤버 변수입니다.

```objective-c
- (void)applicationDidEnterBackground:(UIApplication *)application {
    bgTask = [application beginBackgroundTaskWithName:@"MyTask" expirationHandler:^{
        // 작업을 중단하거나 끝낼 지점을 지정해서 끝나지 않은 작업을 정리
        [application endBackgroundTask:bgTask];
        bgTask = UIBackgroundTaskInvalid;
    }];
 
    // 긴 작업을 시작하고 바로 리턴
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
 
        // 작업과 관련된 작업을 수행
        [application endBackgroundTask:bgTask];
        bgTask = UIBackgroundTaskInvalid;
    });
}
```

* UIApplication의 backgroundTimeReamaing 값을 통해서 작업을 처리하는 데 남은 시간을 알 수 있습니다.
* Expiration handler에 작업을 종료할 때 수행하는 추가적인 코드를 추가할 수 있습니다.
  * 하지만 expiration handler가 불릴 때는 이미 앱의 제한 시간에 가깝기 때문에 실행 시간이 오래 걸리면 안됩니다.
  * 따라서, 최소한의 상태 정보만 정리한 뒤에 작업을 끝내야 합니다.


---



### Downloading Content in the Background

> 파일을 다운로드 할 때, `NSURLSession` 객체를 이용해서 다운로드를 해야만 앱이 중지되거나, 종료 될 경우 시스템에서 다운로드 프로세스를 관리 할 수 있습니다. 백그라운드 다운로드를 위해 `NSURLSession` 객체를 설정하면 시스템은 이러한 다운로드를 별도의 프로세스로 관리하고 일반적인 방법으로 상태를 앱에 보고 할 수 있습니다. 다운로드가 진행되는 동안 앱이 종료되면 시스템은 백그라운드에서 다운로드를 계속하고 다운로드가 완료되거나 하나 이상의 작업에 앱의 주의가 필요할 때 애플리케이션을 시작합니다. 백그라운드 다운로드를 지원하려면 `NSURLSessionConfiguration` 객체를 만들어 몇몇 프로퍼티를 적절한 값으로 설정해야합니다. 그래야 세션을 만들때, 해당 설정 객체를 `NSURLSession`의 적절한 초기화 메소드에 전달할 수 있습니다. 

**백그라운드 다운로드를 지원하는 설정 객체를 생성하는 프로세스는 다음과 같습니다.**

* NSURLSessionConfiguration의 `backgroundSessionConfigurationWithIdentifier :` 메소드를 사용하여 설정 객체를 만듭니다.
* 설정 객체의 sessionSendsLaunchEvents의 속성 값을 YES로 설정합니다.
* Foreground에서 다운로드를 시작할 경우, 설정 객체의 discretionary property 값을 YES로 설정하는 것이 좋습니다.
  * **discretionary property** : 최적의 성능을 위해 시스템의 재량으로 백그라운드 작업을 스케줄링할 수 있는지 여부를 결정하는 부울 값입니다.
* 설정 객체의 다른 속성을 적절하게 설정해야 합니다.
* 설정 객체를 사용해서 NSURLSession 객체를 만들어야 합니다.





#### Declaring Your App’s Supported Background Tasks

> 어떤 종류의 백그라운드 실행은 앱에 의해 미리 선언되어야 합니다. Xcode5 이상부터는 프로젝트 설정의 기능 탭을 통해 백그라운드 모드를 선언할 수 있습니다. 백그라운드 모드 옵션을 사용하도록 설정하면 `UIBackgroundModes Key` 가 Info.plist에 추가됩니다. 



|      Xcode background mode       | UIBackgroundModes value | Description                              |
| :------------------------------: | :---------------------: | :--------------------------------------- |
|        Audio and AirPlay         |          audio          | 백그라운드에서 사용자가 들을 수 있는 콘텐츠를 재생하거나 오디오를 녹음합니다. |
|         Location updates         |        location         | 백그라운드에서 실행 중인 경우에도 앱을 통해 사용자에게 자신의 위치를 계속 알려 줍니다. |
|          Voice over IP           |          voip           | 사용자가 인터넷 연결을 통해 전화를 걸 수 있는 기능을 제공합니다.    |
|       Newsstand downloads        |    newsstand-content    | 백그라운드에서 매거진이나 신문 콘텐츠를 다운로드하고 처리하는 뉴스 스탠드 앱입니다. |
| External accessory communication |   external-accessory    | 외장 액세서리 프레임워크를 통해 정기적인 일정에 따라 업데이트를 제공해야 하는 하드웨어 장치와 함께 작동합니다. |
|  Uses Bluetooth LE accessories   |    bluetooth-central    | Core Bluetooth Framework를 통해 정기적인 일정에 따라 업데이트를 제공해야 하는 블루투스 장치와 함께 작동합니다. |
| Acts as a Bluetooth LE accessory |  bluetooth-peripheral   | Core Bluetooth Framework를 통해 주변 모드에서 블루투스 통신을 지원합니다. |
|         Background fetch         |          fetch          | 정기적으로 네트워크에서 소량의 콘텐츠를 다운로드하고 처리합니다.      |
|       Remote notifications       |   remote-notification   | 푸시 알림이 도착하면 앱이 콘텐츠 다운로드를 시작합니다. 이 통지를 사용하면 푸시 통지와 관련된 콘텐츠를 표시하는 데 지연되는 시간을 최소화할 수 있습니다. |

각 모드는 관련 이벤트에 응답하기 위해 앱이 깨어나거나 시작되어야한다는 것을 시스템에 알립니다. 예를 들어 음악 재생을 시작한 뒤, 백그라운드로 이동한 앱이 있다면 이 앱은 계속해서 오디오 출력 버퍼를 채우기 위해 실행 되어야 합니다. 오디오 모드를 활성화하면 적절한 간격으로 앱에 필요한 콜백을 계속해서 수행해야한다는 것을 시스템 프레임 워크에 알려줍니다. 오디오 모드를 활성화 하지 않는다면 앱이 백그라운드로 이동하면 중지됩니다.



#### Tracking the User’s Location

> 백그라운드에서 사용자의 위치를 추적하는 몇 가지 방법이 있습니다. 대부분의 경우, 백그라운드에서 계속 앱을 실행할 필요가 없습니다. 

* **The significant-change location service** (권장함)

  높은 정밀도의 위치 데이터가 필요없는 앱에 적합합니다. 이 서비스는 사용자의 위치가 크게 변경될 때만 위치가 업데이트 되므로 중요하지 않은 위치 관련 정보를 제공하는 소셜 앱에 이상적입니다. 업데이트가 발생했을 때 앱이 일시 중지되면 시스템은 업데이트를 처리하기 위해 앱을 백그라운드에서 깨웁니다. 이 서비스를 시작하고 종료된 뒤에 새 위치를 사용할 수 있게 되면 앱은 자동으로 다시 시작됩니다.


* **Foreground-only location services **  vs   **Background location services**

  둘 다 Core Location Service를 사용하여 위치 데이터를 검색합니다. 하지만 Foreground-only location services는   앱이 중단된 경우 업데이트를 하지 않으며, 이는 앱이 백그라운드 서비스나 작업을 지원하지 않을 경우 발생하게 됩니다. 따라서 Foreground에 있을 때만 위치 데이터가 필요한 경우에 사용합니다.



Xcode 탭의 Backgroundmode를 이용하거나 Info.plist의 UIBackgroundModes key를 포함시켜 활성화 시킬 수 있습니다. 이 모드를 사용하도록 설정하면 시스템이 일시 중단되는 앱을 차단하지 않습니다. 따라서 이 키를 사용하면 백그라운드에서 애플리케이션을 실행하여 위치 업데이트가 발생할 때마다 이러한 업데이트를 효과적으로 처리할 수 있습니다.



#### Playing and Recording Background Audio

> 오디오를 지속적으로 지속하거나 녹음하는 앱은 백그라운드에서도 해당 작업을 계속 할 수 있어야 합니다. 

대표적인 예들은 다음과 같습니다.

* 뮤직 플레이어 앱
* 오디오 녹음 앱
* AirPlay를 통해 오디오 또는 비디오 재생을 지원하는 앱
* VoIP 앱

UIBackgroundModes 키에 오디오 값이 포함되어 있으면 시스템의 미디어 프레임 워크가 자동으로 해당 앱이 백그라운드로 이동할 때 일시 중지되지 않도록합니다. 오디오 또는 비디오 콘텐츠를 재생하거나 오디오 콘텐츠를 녹음하는 동안 앱은 백그라운드에서 계속 실행됩니다. 그러나 녹화 또는 재생이 중지되면 시스템이 앱을 일시 중지합니다.

미디어 파일을 재생하는 동안 앱은 중지되지 않기 때문에 앱이 백그라운드에서 실행되는 동안에도 콜백은 정상적으로 작동합니다. 콜백은 재생을 위한 데이터를 제공하는 데 필요한 작업만 수행하면됩니다. 예를 들어 스트리밍 오디오 앱은 서버에서 음악 스트림 데이터를 다운로드하고 재생을 위해 현재 오디오 샘플을 푸시해야합니다. 앱은 재생과 관련없는 관련없는 작업을 수행해서는 안됩니다.

두 개 이상의 앱이 오디오를 지원할 수 있기 때문에 시스템은 주어진 시간에 어떤 앱을 재생 혹은 녹음할 지 결정합니다. Foreground 앱은 항상 우선 순위를 갖습니다. 둘 이상의 백그라운드 앱에서 오디오를 재생할 수 있습니다. 이러한 결정은 각 앱의 오디오 세션 개체 구성을 기반으로 합니다. 항상 앱의 오디오 세션 개체를 적절하게 구성하고 시스템 프레임워크와 함께 조심스럽게 작업하여 중단 및 기타 오디오 관련 알림을 처리할 수 있어야 합니다.



####Implementing a VoIP App

> VoIP(Voice over Internet Protocol)을 사용하면 사용자가 단말기의 인터넷 연결을 이용해 전화를 걸 수 있습니다. 이러한 앱은 수신 및 기타 관련 데이터를 받을 수 있도록 지속적으로 네트워크와 연결을 유지해야 합니다. 앱을 항상 깨워두는 것 대신에 시스템은 앱을 중단시킬 수 있고 앱을 위한 소켓 모니터링 기능을 제공합니다. 수신 트래픽이 감지되면 시스템은 VoIP앱을 기동시키고 이 앱으로 소켓 제어 기능을 반환합니다.

VoIP 앱을 구성하려면 다음을 수행해야 합니다.

* Backgroundmode section에서 Voice over IP를 켜야합니다. (Info.plist에 voip값과 함께 .ckground/Modes키를 포함해도 됩니다.)
* VoIP 사용을 위해 앱의 소켓 중 하나를 구성할 수 있습니다.
* 백그라운드로 이동하기 전에 `SECKee.liveTimeout:handler:` 메소드를 호출하여 주기적으로 실행될 핸들러를 두면 이 핸들러는 앱을 통해 서비스 연결을 유지할 수 있습니다.
* 오디오 세션을 구성해서 활성 기능의 전환을 처리할 수 있습니다.

네트워크 소켓의 관리가 필요할 경우, UIBackgroundModes Key에 VoIP 값을 포함 시키면 시스템은 백그라운드에서 앱이 실행되어야 함을 알 수 있습니다. 또한 이 키가있는 앱은 시스템 부팅 직후 백그라운드에서 다시 시작되어 VoIP 서비스를 항상 사용할 수 있습니다.

백그라운드에서 오디오를 전달하려면 VoIP 앱을 백그라운드 오디오 앱처럼 구성해야합니다. 따라서 UIBackgroundModes 키에 오디오 및 VoIP 값을 모두 포함해야합니다. 이렇게하지 않으면 백그라운드에 있는 동안 앱에서 오디오를 재생하거나 녹음 할 수 없습니다. 



#### Fetching Small Amounts of Content Opportunistically

> 새로운 콘텐츠를 주기적으로 확인해야하는 앱은 새로운 콘텐츠를 가져오기 위한 작업을 할 수 있도록 시스템에 요청할 수 있습니다. 

이 모드를 지원하려면 Xcode의 Background modes 섹션에 있는 Background fetch 옵션을 활성화하면 됩니다. Info.plist 파일에 값과 함께 UIBackgroundModes 키를 포함시켜 활성화 할 수도 있습니다. 시스템은 콘텐츠를 다른 앱과 시스템 자체의 필요에 따라 앱이 콘텐츠를 가져오는 데 필요한 기능의 균형을 맞춰야 합니다. 그러한 정보를 평가한 후에 시스템은 적절한 기회가 있을 경우 앱에 시간을 제공합니다. 즉, 콘텐츠를 가져오는 것을 보장하진 않는다는 말 같습니다.

적절한 기회가 발생하면 시스템은 앱을 깨운 뒤, 델리게이트의 `application: performFetchWithCompletionHandler: ` 메소드를 호출합니다. 이 방법을 사용하여 새로운 콘텐츠를 확인하고 콘텐츠를 사용할 수 있는 경우 다운로드 작업을 할 수 있습니다. 다운로드가 끝나면 completion 핸들러 블록을 실행하고 콘텐츠 사용 가능 여부를 전달해야합니다. 이 블록을 실행하면 앱을 중단시키고 전력 사용량을 시스템에 알려줄 수 있습니다.

콘텐츠를 다운로드 할 때 NSURLSession 클래스를 사용하여 다운로드를 관리하는 것이 좋습니다.



#### Using Push Notifications to Initiate a Download

> 새로운 콘텐츠를 사용할 수 있을 때 서버에서 푸시 알림을 보내는 경우 백그라운드에서 앱을 실행하도록 시스템에 요청하여 새로운 콘텐츠를 즉시 다운로드 할 수 있습니다. 이를 사용하는 이유는 사용자가 푸시 알림을 볼 때와 앱이 관련된 콘텐츠를 표시하는 데 걸리는 시간을 최소화 할 수 있기 때문입니다.

이 모드를 지원하려면 Xcode의 Background modes 섹션에서 Remote notification 옵션을 활성화하면 됩니다. Info.plist 파일에 원격 알림 값이있는 UIBackgroundModes 키를 포함시켜 활성화 할 수도 있습니다.

푸시 알림에 의해 다운로드 작업을 시작하려면 notification의 payload에 값이 1로 설정된 content available key가 있어야합니다. 해당 키가 있으면 시스템이 백그라운드에서 앱을 실행하고 앱 델리게이트의 `application:didReceiveRemoteNotification:fetchCompletionHandler:`메소드를 호출합니다. 해당 메소드를 구현하면 관련 콘텐츠를 다운로드하고 앱에 통합해야 합니다.

마찬가지로 컨텐츠를 다운로드할 때 NSURLSession 클래스를 사용하여 다운로드를 관리하는 것이 좋습니다.



#### Downloading Newsstand Content in the Background

> 잡지나 신문을 다운로드 하는 뉴스 스탠드 앱은 백그라운드에서 실행이 가능합니다. 

이 모드를 지원하려면 Xcode의 Background modes 섹션을 이용하거나 Info.plist 파일에 newsstand-content 값이 있는 UIBackgroundModes 키를 포함시켜 활성화 할 수도 있습니다.이 키가 있으면 시스템은 앱이 실행 중이지 않더라도 실행시켜 다운로드를 시작할 수 있습니다.

Newsstand Kit Framework를 사용하여 다운로드를 시작하면 시스템에서 앱의 다운로드 프로세스를 처리합니다. 앱이 일시 중지되거나 종료 된 경우에도 파일은 계속 다운로드됩니다. 다운로드 작업이 완료되면 시스템에서 파일을 앱 샌드 박스로 전송하고 앱에 알립니다. 앱이 실행되고 있지 않으면 이 알림이 표시되어 새로 다운로드 한 파일을 처리 할 수있는 기회가 제공됩니다. 다운로드 중에 오류가 발생하더라도 알림을 통해 다운로드 중인 파일을 처리할 수 있습니다.



#### Communicating with an External Accessory

> 외부 장치와 함께 동작하는 앱은 외부 장치가 새로운 업데이트를 제공할 경우 백그라운드 실행을 요청할 수 있습니다. 심박 수 모니터와 같이 정기적인 간격으로 데이터를 제공하는 경우에 중요합니다.

이 모드를 지원하려면 Xcode의 Background modes 섹션에서 외부 장치 통신을 이용하면 됩니다. Info.plist 파일에 external-accessory 값과 함께 UI BackgroundModes 키를 포함시켜 사용할 수도 있습니다. 새로운 데이터가 장치로부터 도착하면, 해당 데이터를 처리할 수 있도록 프레임워크가 앱을 실행시키고 시스템은 장치 연결 및 분리 알림을 처리할 수 있도록 앱을  실행시킵니다.

장치 업데이트의 백그라운드를 지원하는 앱은 다음 지침을 따라야합니다.

* 앱은 사용자가 장치 업데이트 이벤트를 시작하고 중지 할 수 있는 인터페이스를 제공해야 합니다. 이 인터페이스는 장치 세션을 적절하게 열거나 닫아야 합니다.
* 깨어나게 되면, 앱은 데이터를 처리하는데 10초 정도 걸리게 됩니다. 이상적인 모습은 최대한 빨리 데이터를 처리하고 다시 앱이 중지 되어야 합니다. 그러나 시간이 더 필요한 경우 앱은 `beginBackgroundTaskWithExpirationHandler :` 메소드를 사용하여 추가 시간을 요청할 수 있습니다. (꼭 필요한 경우에만 사용할 것)



#### Communicating with a Bluetooth Accessory

> 블루투스 기기와 함께 작동하는 앱은 일시 중지 되었을 때 주변 기기가 업데이트를 제공하면 앱을 깨우라고 요청할 수 있습니다. 이는 블루투스 심장 박동 벨트와 같이 일정한 간격으로 데이터를 전송하는 Bluetooth-LE 장치에 중요합니다.

이 모드를 지원하려면 Xcode 프로젝트의 Background modes 섹션에서 블루투스 액세서리를 이용하면 됩니다. Info.plist 파일에 bluetooth-central 값과 함께 UIBackgroundModes 키를 포함시켜 활성화 할 수도 있습니다. 이 모드를 활성화하면 Core Bluetooth framework가 주변 기기의 활성 세션을 계속 열어 둡니다. 주변 장치에서 새로운 데이터가 도착하면 시스템에서 데이터를 처리 할 수 있도록 앱을 시작합니다. 그리고 시스템은 장치 연결 및 연결 해제 알림을 처리하기 위해 앱을 작동시킵니다.

블루투스 데이터의 백그라운드 처리를 지원하는 앱은 모두 세션 기반이어야 하며 다음 같은 지침을 따라야 합니다.

* 사용자가 블루투스 이벤트 전송을 시작하고 중지 할 수 있는 인터페이스를 제공해야 합니다. 이 인터페이스는 장치 세션을 적절하게 열거나 닫아야 합니다.
* 깨어나게 되면, 앱은 데이터를 처리하는데 10초 정도 걸리게 됩니다. 이상적인 모습은 최대한 빨리 데이터를 처리하고 다시 앱이 중지 되어야 합니다. 그러나 시간이 더 필요한 경우 앱은 `beginBackgroundTaskWithExpirationHandler :` 메소드를 사용하여 추가 시간을 요청할 수 있습니다. (꼭 필요한 경우에만 사용할 것)

---



### Getting the User's Attention While in the Background

> 알림은 앱이 중지되었거나 백그라운드에 있을때 사용자의 시선을 끌수 있는 방법입니다. 로컬 알림의 사운드, 배지, 알림 기능을 이용하여 사용자에게 알릴 수 있으며 사용자는 앱을 foreground로 되돌려 놓을지 결정해야합니다. 앱이 이미 foreground에서 실행중인 경우, 로컬 알림은 사용자에게 전달되지 않습니다.

- 로컬 알림을 예약하려면 `UILocalNotification` 클래스의 인스턴스를 만들고 매개변수를 구성한 다음 `UIApplication` 클래스의 메소드를 이용해 일정을 예약 할 수 있습니다.
- 로컬 알림은 알림의 유형(소리, 알림, 배지) 및 알림 시각에 대한 셋팅을 할 수 있습니다.
- `UIApplication` 클래스의 메소드는 알림을 즉시 전달하거나 예약하는 옵션을 제공합니다.

```objective-c
- (void)scheduleAlarmForDate:(NSDate*)theDate {
    UIApplication* app = [UIApplication sharedApplication];
    NSArray* oldNotifications = [app scheduledLocalNotifications];
 
    // 새 알림을 예약하기 전에 이전 알림 지우기
    if ([oldNotifications count] > 0)
        [app cancelAllLocalNotifications];
 
    // 새 알림 만들기
    UILocalNotification* alarm = [[UILocalNotification alloc] init];
    if (alarm) {
        alarm.fireDate = theDate;
        alarm.timeZone = [NSTimeZone defaultTimeZone];
        alarm.repeatInterval = 0;
        alarm.soundName = @"alarmsound.caf";
        alarm.alertBody = @"Time to wake up!";
 
        [app scheduleLocalNotification:alarm];
    }
}
```

위 코드는 사용자가 설정한 날짜와 시간을 사용하여 알림을 예약하는 예제 입니다. 한 번에 하나의 알림만 구성하고 새 알림을 예약하기 전에 이전 알림을 취소합니다. 알림 자체는 경고 상자와 앱이 실행될 때 재생되는 사운드 파일로 구성됩니다 알림은 실행 중이지 않거나 백그라운드에 있을 때 울립니다. 응용 프로그램이 활성화되어 foreground에서 실행중인 경우 앱 델리게이트의 `application : didReceiveLocalNotification :` 메소드가 대신 호출됩니다.

로컬 알림과 함께 사용되는 sound 파일은 푸시 알림에 사용되는 것과 동일한 요구 사항을 갖습니다. 맞춤 사운드 파일은 앱의 기본 번들 내에 있어야하며 선형 PCM, MA4, μ-Law 또는 a-Law 형식 중 하나를 지원해야합니다. `UILocalNotificationDefaultSoundName` 상수를 지정하여 장치에 대한 기본 경고 사운드를 재생할 수도 있습니다. 알림이 전송되고 사운드가 재생되면 시스템은 해당 알림을 지원하는 장치에서도 진동을 유발합니다. UIApplication 클래스의 메소드를 사용하여 예약 된 알림을 취소하거나 알림 목록을 가져올 수 있습니다.

------



### Understanding When Your App Gets Launched into the Background

> 백그라운드 실행을 지원하는 경우, 시스템이 이벤트 처리를 위해 앱을 재실행 시킬 수 있습니다. 사용자에 의해 강제로 앱이 종료되지 않은 이상 다음과 같은 이벤트 중 하나가 발생하면 시스템이 임의로 앱을 재실행하게됩니다.

```
* 위치 앱 : 지정된 특정 지역에 들어왔을 때
* 오디오 앱 : 오디오 재생 혹은 마이크 사용해서 일부 데이터를 처리할 때
* 블루투스 앱 : 연결된 주변 기기에서 데이터를 수신하거나 중앙으로부터 명령을 받을 때
* 백그라운드 다운로드 앱 : 다운로드를 성공적으로 완료했거나 에러가 발생했을 때
```

- 대부분의 경우 시스템은 사용자가 강제 종료 한 후에 앱을 다시 실행하지 않지만, iOS 8 이상의 장비에서 위치앱은 재실행됩니다.
- 만일 기기가 비밀번호(지문)를 필요로 하면 잠금해제 해야 백그라운드 상태에서 앱이 실행됩니다.

------



### Being a Responsible Background App

> Foreground 상태의 앱은 시스템 리소스 및 하드웨어 사용과 관련해 항상 백그라운드 상태의 앱보다 우선시 됩니다. 백그라운드에서 실행되는 앱은 이러한 불균등을 대비하여 백그라운드에서 실행될 동작을 준비해야합니다. 특히 백그라운드로 이동하는 앱은 다음 가이드를 준수해야 합니다.

- **코드에서 OpenGL ES를 호출하지 마십시오.** 
  - 백그라운드에서 실행되는 동안 EAGLContext 객체를 만들거나 OpenGL ES 드로잉 명령을 실행하면 앱은 즉시 종료됩니다. 
- **앱이 정지 되기 전에 Bonjour와 관련된 서비스를 취소하십시오.** 
  - 앱이 백그라운드로 이동해 정지되기 전에 Bonjour를 취소하고 네트워크 서비스와 관련된 수신 대기 소켓을 닫아야 합니다. 만약 Bounjour 서비스를 직접 취소하지 않으면 시스템이 서비스를 종료시키게됩니다.
- **네트워크 기반 소켓의 연결 오류를 처리 할 수 있도록 준비하십시오.** 
  - 시스템은 여러가지 이류로 앱이 정지된 동안 소켓 연결을 해제할 수 있습니다. 신호 손실이나 네트워크 전환 오류에 대비되어있어야 예상치 못한 문제가 발생하지 않습니다.
- **백그라운드로 이동하기 전에 앱 상태를 저장하십시오.** 
  - 중지된 앱은 메모리에서 제거되기 전에 앱에 알림이 제공되지 않이 때문에 상태보존 메커니즘을 활용해 앱의 UI 상태를 디스크에 저장해야합니다.
- **백그라운드로 이동하기 전에 불필요한 객체에 대한 강한 참조를 해제하세요.** 
  - 앱에서 용량이 큰 객체(이미지)를 관리하는 경우, 백그라운드로 이동하기 전에 해당 캐시에 대한 강한 참조를 해제해야합니다.
- **정지되기 전에 공유 시스템 자원 사용을 중지하십시오.** 
  - 주소록, 캘린더, 데이터 베이스와 같이 공유 시스템 리소스와 상호작용하는 앱은 정지되기 전에 해당 리소스 사용을 중지해야합니다.
- **View 업데이트를 하지 마십시오.** 
  - 앱이 배경에 있을때는 View가 표시되지 않으므로 업데이트를 하지않는 것이 좋습니다.
- **외부 액세서리에 대한 연결 알림 및 연결 해제 알림에 응답하세요.**
- **백그라운드로 이동할 때 활성화된 경고 창을 정리하세요.**
  -  앱을 백그라운드로 전환할때 시스템이 자동으로 `UIActionSheet` 또는 `UIAlertView`를 닫지 않습니다. 백그라운드로 이동하기 전에 적절한 정리를 해야합니다.
- **백그라운드로 이동하기 전에 View에서 민감한 정보를 제거하십시오.** 
  - 앱이 백그라운드로 전환되면 시스템은 앱 화면을 스냅 샷으로 찍은 다음, 다시 foreground로 전환 할때 간단히 표시하게 됩니다. `applicationDidEngetBackground:` 메소드에서 돌아오기 전에 비밀 번호나 개인 정보는 숨기도록 해야합니다.
- **백그라운드에 있는 동안 최소한의 작업을 수행하십시오.**

------



### Opting Out of Backgound Execution

> 백그라운드에서 앱을 실행시키지 않으려면 Info.plist 파일의 `UIApplicationExitsOnSuspend` 키의 값을 true로 추가하여 백그라운드를 명시적으로 선택 해제 할 수 있습니다. 앱이 선택 해제되면 앱 생명 주기가 not-running, inactive, active 상태를 순환하며 백그라운드나 suspended 상태로 들어가지 않습니다. 사용자가 홈 버튼을 눌러 앱을 종료하면 `appDelegate`의 `applicationWillTermintae:` 메소드 호출되고 앱이 종료되기 전에 약 5초간 정리되고 not-running 상태로 이동하게됩니다.

- 백그라운드 실행을 선택해제 하는 것은 권장하지는 않지만, 특정 조건에서는 기본 옵션이 될 수 있습니다. 특히 백그라운드에서 실행되는 코드가 복잡성을 증가시키는 경우 앱을 종료시키는 것이 간단한 해결책이 될 수 있습니다.
- 많은 메모리를 소비하고, 쉽게 메모리를 해제 할 수 없는 경우 시스템이 앱을 빠르게 종료하여 다른 앱을 위한 공간을 확보 할 수 있습니다.

