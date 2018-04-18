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
* Array를 이용해서 키의 값을 지정하는 경우, 키가 있으면 해당 기능이 필요함을 나타내고, 키가 없으면 기능이 필요하지 않음을 나타내며, 없어도 애플리케이션이 실행됩니다.
* Dictionary를 이용해서 키의 값을 지정하는 경우, Boolean값을 이용해서 표시하는데  true값은 필수 기능을 나타내고 false값은 디바이스에 기능이 없음을 나타냅니다. 앱에서 특정 기능을 선택할 수 있는 경우 Dictionary에 해당 키를 포함하면 안 됩니다.

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

지역화 관련 WWDC 영상 : https://developer.apple.com/videos/play/wwdc2016/201

지역화 관련 예제 : https://github.com/Yongjai/iOS_Adv/tree/master/Week12



출처 : https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/ExpectedAppBehaviors/ExpectedAppBehaviors.html#//apple_ref/doc/uid/TP40007072-CH3-SW2