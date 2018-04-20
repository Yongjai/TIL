# View Controller Programming Guide for iOS

> iOS에서 View Controller와 관련된 개발을 할 때 가이드를 제공합니다.



## The Role of View Controllers

> View Controller는 뷰와 이벤트를 관리하고, 뷰 컨트롤러에서 다른 뷰 컨트롤러로의 transitioning을 핸들링하는 등의 역할을 합니다.



* Content View Controller :  앱의 컨텐츠를 표현하는 역할을 합니다. 
* Container View Controller : 다른 뷰 컨트롤러(Child View Controller)의 정보들을 가지고 있습니다. Navigation Controller, Tab Bar Controller가 이에 속합니다.



지금부터 뷰 컨트롤러가 무엇을 하는지에 대해 간략하게 살펴보겠습니다.

### View Management

> View Controller의 가장 중요한 역할은 뷰들의 계층을 관리하는 역할입니다. 모든 뷰컨트롤러는 루트 뷰를 하나씩 가지고 있으며 View Controller는 항상 이 루트 뷰에 대한 강한 참조를 가지고 있으며, 각 뷰들 역시 자신의 서브 뷰들에 대한 강한 참조를 가지고 있습니다.



다음 그림은 뷰 컨트롤러와 뷰간의 관계를 보여줍니다.

![1](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/VCPG_ControllerHierarchy_fig_1-1_2x.png)

Container View Controller는 자신이 포함하고있는 Content View Controller의 Child 뷰들은 간섭하지 않는다. 단지 컨테이너 뷰컨트롤러의 디자인에 따라 루트뷰들의 사이즈를 조절하고 배치합니다.

아래 그림을 보면 좀 더 명확하게 알 수 있습니다.

![2](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/VCPG_ContainerViewController_fig_1-2_2x.png)



Split View Controller는 View Controller A/B를 가지고 있고 이를 가지고 화면에 사이즈를 조절하고 배치해주는 모습입니다.



### Data Marshaling

> 뷰 컨트롤러는 앱의 데이터와 뷰 사이의 중계자 역할을 하고 있습니다. UIViewController 클래스의 메소드와 프로퍼티를 통해 앱에 보여지는 것들을 관리할 수 있습니다.



아래 그림을 보면 Content View Controller는 데이터를 보여주기 위해 데이터와 뷰에 대한 참조를 가지고 있습니다.

![3](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/VCPG_CustomSubclasses_fig_1-3_2x.png)

* 개발자는 항상 명확하게 뷰 컨트롤러가 해야할 일과 데이터 객체가 해야할 일을 구분해야 합니다.
* 뷰 컨트롤러에서는 실제 데이터에 관한 컨트롤을 최소화 해야                               합니다. (이는 뷰컨트롤러의 역할이 아니다!)
* 뷰컨트롤러는 뷰에서 생성되는 데이터의 validation처리, 이 데이터를 데이터 객체에 알맞게 페키징 하는 역할이 주된 임무이다.
* UIDocument 객체를 이용하는 것이 데이터와 뷰컨트롤러의 역할을 분리할 수 있는 한 가지 방법입니다.
* document 객체는 어떻게 데이터를 읽고 쓰는지를 알고 있는 객체입니다. 서브클래싱해서 원하는 로직과 메소드를 추가할 수 있으며, 데이터를 추출해 뷰컨트롤러나 앱의 다른 파트들로 보낼 수 있습니다.
* 다시말해, 뷰컨트롤러에 있는 데이터들은 document 객체에 있는 데이터들을 복사하여 뷰에 반영하는 역할만 하고, 진짜 데이터들은 document에 있는 있다고 말할 수 있습니다.



### User Interactions

> 뷰컨트롤러는 responder 객체입니다. 따라서 responder chain을 따라 내려오는 이벤트를 헨들링할 수 있습니다.

사실 뷰 컨트롤러에서 터치 이벤트를 직접 받는 일은 거의 없습니다. 대신 각 뷰들이 터치 이벤트를 받으면 그 이벤트를 관련된 delegate나 target object에 넘겨주는데 delegate 역할이나 target object의 역할을 뷰 컨트롤러가 주로 담당합니다.



### Resource Management

* 뷰 컨트롤러는 내부적으로 뷰를 자동 관리 하고 있습니다. 예를 들어, UIKit은 자동으로 뷰에 관련된 리소스들이 더 이상 필요가 없을때 메모리를 반환하는데 사용 가능한 메모리가 적을 때, UIKit은 앱에게 사용하지 않는 리소스들을 해제하라고 요청합니다.
* 그 한 가지 예가 didReceiveMemoryWarning이라는 View Controller의 메소드 호출이다. 이 메소드 내에 다시 생성 가능한 것들이나 더 이상 사용하지 않는 것들을 해제하는 코드를 넣는 용도로 사용할 수 있습니다.



+++ 메모리가 부족해서 앱에 문제가 생길 정도의 앱을 구현해 본 적이 없는데... 혹시 공유할 만한 경험들이 계신가요?



### Adaptivity

> 뷰 컨트롤러는 뷰들이 어떻게 보여질지에 대한 책임을 가지고 있습니다. 그래서 다양한 사이즈의 아이패드와 아이폰에서 각각의 뷰 컨트롤러에 계층을 구성하는것 보다 하나의 뷰 컨트롤러에서 다양한 사이즈의 디바이스에서 모두 호환될 수 있도록 구현하는 것이 좋습니다.

뷰컨트롤러는 coarse-grained changes(조잡한 변화)와 fine-grained changes(부드러운 변화) 모두 핸들링해야 합니다.

* coarse-grained changes 
  * 화면 비율과 같은 디스플레이의 전체적인 환경 자체가 바뀌는 것을 의미합니다.
* fine-grained changes 
  * 작은 변화는 가로/세로 모드 전환과 같이 디스플레이의 비율은 안 변하고 크기만 변하는 것을 의미합니다. 이때, 오토레이아웃을 쓴다면 뷰의 사이즈와 위치를 새로운 크기에 맞게 잡아줍니다.

결과적으로 이렇게 화면에 작용하는 변화들에 대한 처리들은 뷰컨트롤러가 담당해야 합니다.



![4](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/VCPG_SizeClassChanges_fig_1-4_2x.png)





## The View Controller Hierarchy

> 앱의 뷰 컨트롤러 간의 관계를 올바르게 유지해야 자동적으로 실행될 동작들이 정상적으로 동작할 수 있습니다. 즉, 각 뷰 컨트롤러의 특성과 정해진 사용법을 익히고 그에 맞춰서 사용해야 합니다.



### The Root View Controller

> 루트 뷰컨트롤러는 뷰 컨트롤러 계층에서 anchor와 같은 역할을 하고 있습니다. 모든 window는 단 한개의 루트 뷰 컨트롤러를 가지며 루트 뷰 컨트롤러는 유저들에게 보여줄 첫번째 컨텐츠를 정의합니다.



![5](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/VCPG-root-view-controller_2-1_2x.png) 

위 그림에서 볼 수 있듯이 UIWindow는 한 개의 rootViewController를 가지고 있고, 그 루트 뷰 컨트롤러의 뷰가 화면의 컨텐츠를 구성하게 됩니다.

루트 뷰 컨트롤러는 UIWindow 객체의 rootViewController 프로퍼티에서 접근 할 수 있습니다. 스토리 보드를 사용해서 뷰 컨트롤러를 구성하면 실행 시 UIKit은 해당 속성의 값을 자동으로 설정합니다. 코드로 생성한 윈도우의 경우는 루트 뷰 컨트롤러를 직접 설정해야합니다.



### Container View Controllers

> 컨테이너 뷰 컨트롤러를 사용하면 더욱 관리가 쉽고 재사용 가능하게 해서  정교한 인터페이스를 만들 수 있습니다. 컨테이너 뷰 컨트롤러는 하나 이상의 자식 뷰 컨트롤러의 내용을 optional custom view와 함께 혼합하여 최종 인터페이스를 만듭니다.  컨테이너 뷰 컨트롤러에는 UINavigationController, UISplitViewController, UIPageViewController 등이 있습니다. 



컨테이너 뷰 컨트롤러의 뷰는 항상 지정된 공간을 채우게 됩니다. 컨테이너는 자식 뷰를 적절하게 위치시키는데 그림에서 컨테이너는 자식 뷰를 나란히 배치하고 있습니다.



![6](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/VCPG-container-acting-as-root-view-controller_2-2_2x.png)



### Presented View Controllers

> 어떤 뷰 컨트롤러를 하나 present하게 되면 새 컨트롤러의 내용으로 바뀌고 일반적으로 이전의 뷰 컨트롤러의 내용이 숨겨집니다. present는 새로운 콘텐츠를 모달 표시하는 데 가장 자주 사용됩니다. 예를 들어, 사용자로부터 입력을 수집하기 위해뷰 컨트롤러를 present 할 수 있습니다. 



뷰 컨트롤러를 present하게 되면 다음 그림과 같이 presenting view controller와 presented view controller 사이의 관계가 생기게 됩니다. 이러한 관계는 뷰 컨트롤러 계층의 일부를 형상하며 런타임에 다른 뷰 컨트롤러를 찾는 방법입니다.

![7](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/VCPG-presented-view-controllers_2-3_2x.png)



아래 그림을 보면, UINavigationController와 같은 컨테이너 뷰 컨트롤러는 자기의 자식 뷰 컨트롤러들을 가지고 있으며, 만약 이 안에서 새로운 뷰컨트롤러를 present한다면, UIKit은 가장 가까운 컨테이너 뷰 컨트롤러인 UINavigationController를 찾아 거기서 present를 하게 되는 방식입니다.



![8](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/VCPG-container-and-presented-view-controller_2-4_2x.png)





## Design Tips

>   Design Tips에서는 뷰컨트롤러를 이용할 때, 오동작을 막기 위한 가이드를 제시하고 있습니다.





### Use System-Supplied View Controllers Whenever Possible

> 시스템에서는 다양한 뷰컨트롤러들을 제공하는데 이를 이용하면 사용자들에게 일관성 있는 경험을 줄 수 있고 개발 시간도 절약해줍니다. 예를 들어, UIImagePickerController를 이용하면 손쉽게 카메라 롤의 이미지와 비디오를 보여주는 표준 화면을 보여줄 수 있습니다.



커스텀 뷰 컨트롤러를 만들기 전에 프레임워크에서 해당 작업을 수행할 수 있는 기능을 하는 뷰 컨트롤러가 있는지를 확인해야 합니다.

* UIKit : alert와 사진, 비디오를 촬영하는 기능, iCloud에서 파일을 관리하는 기능
* GameKit : 각 플레이어와 점수판, 경험치 등 여러 게임에 필요한 feature들을 매칭해주는 기능
* AddressBookUI : 연락처를 보여주고 선택할 수 있는 기능
* MediaPlayer : 비디오를 재생하고 사용자의 library에서 미디어 asset을 선택할 수 있는 기능
* EventKitUI : 사용자의 켈린더 데이터를 보여주고 수정할 수 있는 기능
* GLKit : OpenGL 렌더링을 해주는 기능
* MultipeerConnectivity : 다른 사용자를 detect하고 연결될 수 있도록 초대하는 기능
* MessageUI : 이메일과 SMS메시지를 다룰 수 있는 기능
* PassKit : 애플 페이와 관련된 기능(passes들을 보여주고 passbook에 등록해주는 기능이라 되어있다.)
* Social : 트윗, 페이스북 등의 여러 소셜미디어 사이트로의 메시지를 다룰 수 있는 기능
* AVFoundation : 미디어 asset들을 보여주는 기능

```
주의할 점은 시스템에서 제공하고 있는 뷰컨트롤러들의 뷰 하이라키를 수정하지 말아야 합니다. 제공되는 public 메소드들을 이용해야 합니다.
```



### Make Each View Controller an Island

> 뷰 컨트롤러는 항상 self-contained 객체이며 내부 동작이나 다른 뷰컨트롤러의 뷰 계층에 대해서 몰라야 합니다. 두 개의 뷰 컨트롤러가 데이터들을 계속해서 주고 받는 경우, 해당 뷰 컨트롤러들은 항상 명시적으로 정의된 public한 인터페이스를 사용해서 커뮤니케이션 해야한다. 델리게이트를 이용해서 뷰 컨트롤러간 커뮤니케이션을 자주 사용합니다.



델리게이트 패턴을 자주 이용하게 되는 이유는 델리게이트 패턴을 사용하면 델리게이트 객체의 타입에 상관 없이 해당 프로토콜을 구현하고 있는 객체가 델리게이트로 등록되어 있다면 델리게이트의 메소드를 호출할 수 있으므로 다른 뷰컨트롤러와 커뮤니케이션 함에 있어 의존성을 없앨 수 있습니다.



### Use the Root View Only as a Container for Other Views

> 뷰 컨트롤러의 루트 뷰는 컨테이너의 역할로만 사용해야 합니다. 루트 뷰를 컨테이너로 하위에 뷰를 추가하면 해당 뷰 컨트롤러에 속한 모든 뷰들이 공통된 parent뷰를 가지게 되므로 코드가 심플해지고 레이아웃 적용에 적합해집니다.



+++ 루트 뷰를 컨테이너로 사용하지 않는 경우가 있나요?



### Know Where Your Data Lives

> MVC 패턴에서 뷰 컨트롤러의 역할은 모델 객체와 뷰 객체 사이의 데이터 이동을 편리하게 해주는 역할 입니다. 뷰컨트롤러는 데이터들을 임시 변수에 저장하고 validation 체크를 하며 뷰 객체에 정확한 데이터를 전달해주는 것이 주된 역할 입니다. 데이터 자체의 integrity 등을 판단하는 것은 데이터 객체(Model)의 역할이다.

이는 UIDocument와 UIViewController의 관계가 한 가지 예시가 될 수 있다. UIDocument에 진짜 데이터가 들어있으며, 뷰컨트롤러에서는 단지 데이터를 캐싱만 하면서 뷰에 캐싱 데이터를 보여주는 역할을 합니다.



### Adapt to Changes

> 앱은 다양한 iOS디바이스에서 실행되기 때문에 뷰컨트롤러는 다양한 디바이스의 스크린 사이즈에서 사용 될 수 있도록 설계되어야 합니다. 다른 사이즈에 관해 각각 다른 뷰 컨트롤러를 개발하기보다는 디바이스의 사이즈나 사이즈 변화를 감지하는 기능들을 통해 하나의 뷰 컨트롤러가 다양한 변화에 호환될 수 있도록 해야합니다. 