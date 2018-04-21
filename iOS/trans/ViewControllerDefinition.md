# View Controller Definition



## Defining Your Subclass

> 커스텀 서브클래스를 이용해서 앱의 컨텐츠를 보여줄 수 있습니다. 대부분의 커스텀 뷰 컨트롤러들은 컨텐츠 뷰 컨트롤러인데 모든 뷰를 소유하고 뷰의 데이터를 담당하고 있습니다. 이와는 반대로 컨테이너 뷰 컨트롤러는 모든 뷰를 소유하지 않으며 일부 뷰는 다른 뷰 컨트롤러가 관리하고 있습니다. 

* 뷰 컨트롤러에서 주로 테이블 뷰를 사용한다면 UITabelViewController를 사용합니다.
* 뷰 컨트롤러에서 주로 컬렉션 뷰를 사용한다면 UICollectionViewController를 사용합니다.
* 이 외에 다른 뷰 컨트롤러에서는 UIViewController를 사용합니다.

컨테이너 뷰 컨트롤러의 경우, 부모 클래스는 기존 컨테이너 클래스를 수정할지 사용자 고유의 컨테이너 클래스를 생성할지에 따라 달라집니다. 기존 컨테이너의 경우 수정할 뷰 컨트롤러 클래스를 선택해야 합니다. 새로운 컨테이너 뷰 컨트롤러의 경우, 보통 UIViewController를 서브클래스 합니다.



### Defining Your UI

> Xcode에서 스토리보드 파일을 사용하여 시각적으로 뷰 컨트롤러의 UI를 정의하면 다양한 환경에서 뷰 컨트롤러의 컨텐츠를 시각화하고 필요에 따라 뷰 계층을 커스텀 할 수 있습니다. 시각적으로 UI를 작성하면 빠르게 변경하고 앱을 빌드하고 실행하지 않고도 결과를 볼 수 있습니다.

다음 그림은 스토리보드의 예입니다. 직사각형 영역은 뷰 컨트롤러 및 연관된 뷰를 보여줍니다. Segue는 컨테이너 뷰 컨트롤러를 자식 뷰 컨트롤러에 연결합니다. 

![1](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/storyboard_bird_sightings_2x.png)



각각의 새로운 프로젝트는 메인 스토리보드를 가지고 있는데 일반적으로 하나 이상의 뷰 컨트롤러를 이미 가지고 있습니다. 드래그를 통해 스토리보드에 새로운 뷰 컨트롤러를 추가할 수 있습니다. 새로운 뷰 컨트롤러는 클래스가 지정되어 있지 않기 때문에 인스펙터를 이용해서 지정해줘야 합니다.

스토리보드 에디터는 다음과 같이 사용하면 됩니다.

* 뷰 컨트롤러에 뷰를 추가하고, 배열하고, 구성합니다.
* 아웃렛과 액션을 연결합니다.
* 뷰 컨트롤러에 relationship과 segue를 만듭니다.
* 사이즈 클래스에 따라 레이아웃과 뷰를 커스터마이즈합니다.
* 뷰에 대한 사용자의 인터렉션을 위한 제스쳐를 추가합니다.



### Handling User Interactions

> 앱의 응답 객체는 들어오는 이벤트를 처리하고, 적절한 행동을 하게 됩니다. 뷰 컨트롤러는 응답 객체이지만 직접 터치 이벤트를 거의 처리하지 못합니다. 대신에, 뷰 컨트롤러는 이벤트를 다음과 같은 방식으로 처리하게 됩니다.

* 뷰 컨트롤러는 높은 수준의 이벤트를 처리하기 위해 액션 메소드를 정의합니다. 액션 메소드는 다음 항목들에 반응합니다.
  * **특정 액션** 제어 장치와 일부 뷰는 특정 인터렉션을 알리기 위해 액션 메소드를 부릅니다. 
  * **제스처 인식** 제스처 인식은 현재 제스처의 상태를 알리기 위해 액션 메소드를 부릅니다. 진행 중인 상태 변화 또는 완료된 제스처에 응답하기 위해 뷰 컨트롤러를 이용해야 합니다.
* 뷰 컨트롤러는 다른 객체에 대한 data source 또는 delegate 역할을 합니다. 뷰 컨트롤러는 종종 테이블 뷰나 컬렉션 뷰의 데이터를 관리합니다. 업데이트 된 위치값을 보내는 `CLLocationManager`와 같은 객체의 델리게이트로서 사용할 수 도 있습니다.



(해당 뷰에 참조가 있을 경우)이벤트에 응답하는 것은 종종 뷰의 컨텐츠를 업데이트 하는 것을 포함합니다. 뷰 컨트롤러는 나중에 수정이 필요한 다른 뷰를 위한 outlet을 정의하기에 적합합니다. 

```swift
class MyViewController: UIViewController {
    @IBOutlet weak var myButton : UIButton!
    @IBOutlet weak var myTextField : UITextField!
    
    @IBAction func myButtonAction(sender: id)
}
```

위와 같이 IBOutlet과 IBAction을 지정할 수 있습니다. outlet은 버튼과 텍스트 필드에 대한 레퍼런스를 저장하며, action은 버튼 탭에 응답합니다.

스토리보드에서 뷰컨트롤러에 outlet과 action을 연결하면 뷰가 로드 될 때 구성 됩니다. 



### Displaying Your Views at Runtime

> 스토리보드는 뷰 컨트롤러의 뷰를 로드하고 보여주는 프로세를 간단하게 만듭니다. UIKit은 필요한 경우, 스토리보드 파일에서 뷰를 자동으로 로드합니다. 

로딩 프로세스에서 UIKit은 다음과 같은 순서로 작업을 수행합니다.

* 스토리보드의 파일 정보를 사용해서 뷰를 인스턴스화 합니다.
* outlet과 action을 모두 연결합니다.
* 뷰 컨트롤러의 뷰 프로퍼티에 루트 뷰를 지정합니다.
* 뷰 컨트롤러의 `awakeFromNib` 메소드를 호출합니다. 이 메소드가 불리면 뷰 컨트롤러의 trait collection이 비어있고, 뷰가 최종 위치에 있지 않을 수도 있습니다.     
* `viewDidLoad` 메소드를 부릅니다. 이 메소드를 사용해서 뷰를 추가하거나 제거할 수 있고 layout constraint를 수정하고, 뷰에 데이터를 로드할 수 있습니다.

뷰 컨트롤러의 뷰를 화면에 표시하기 전에 UIKit은 화면 전후에 뷰를 준비할 수 있는 기회를 제공합니다. UIKit은 다음 작업을 수행합니다.

* `viewWillAppear` 메소드를 불러 뷰가 화면에 나타났음을 알립니다.
* 뷰의 레이아웃을 업데이트 합니다.
* 뷰를 화면에 표시합니다.
* 뷰가 화면에 나타나면 `viewDidAppear`  메소드가 부릅니다.

뷰의 크기나 위치를 추가/제거/수정하는 경우 해당 뷰에 적용되는 constraint도 제거해야합니다. 레이아웃 계층을 뷰 계층 구조로 변경하면 레이아웃을 더티로 표시합니다. 다음에 오는 업데이트 사이클에서 레이아웃 엔진은 layout constraint를 이용해서 뷰의 크기와 위치를 계산하고 변경 사항을 뷰 계층 구조에 적용합니다.

```
일반적으로 더티는 일부 데이터가 변경되어 결국 일부 외부 대상에 기록되어야 함을 나타내는 데 사용됩니다. 인접한 데이터가 변경 될 수 있고 대량의 데이터를 쓰는 것이 일반적으로 개별값을 쓰는 것보다 효율적이기 때문에 즉시 작성되지는 않습니다.
```



### Managing View Layout

> 뷰의 크기와 위치가 변경되면 UIKit은 뷰 계층의 레이아웃 정보를 업데이트 합니다. 오토 레이아웃을 사용하여 뷰를 구성하는 경우 UIKit은 오토 레이아웃 엔진을 사용해서 현재 레이아웃에 따라 레이아웃을 업데이트 합니다. UIKit은 관련있는 객체들에게 레이아웃이 변경되어 이에 따라 응답해야 함을 알릴 수도 있습니다.

레이아웃 프로세스 중에 UIKit은 몇 몇 지점에서 사용자에게 추가적인 레이아웃 관련 작업을 할 수 있다고 알립니다. 노티를 사용해서 layout constraint를 수정하거나 layout constraint를 적용한 후 레아아웃을 최종 수정할 수 있습니다. 레이아웃 프로세스 중에 영향을 받은 뷰 컨트롤러에 대해 UIKit은  다음을 수행합니다.

* 필요에 따라 뷰 컨트롤러 및 trait collection을 업데이트합니다.
* `viewWillLayoutSubviews` 메소드를 부릅니다.
* 현재  `UIPresentationController` 객체의  `containerViewWillLayoutSubviews` 메소드를 부릅니다.
* 루트 뷰의 `layoutSubviews`  메소드를 부릅니다. 이 메소드의 기본 구현은 사용 가능한 constraint를 사용해서 새로운 레이아웃의 정보를 계산합니다. 그리고 뷰 계층 구조를 트래버스해서 각 자식 뷰에 대해 layoutSubviews를 호출합니다.
* 계산된 레이아웃 정보를 뷰에 적용합니다.
*  `viewDidLayoutSubviews` 메소드를 부릅니다.
* 현재  `UIPresentationController` 객체의  `containerViewDidLayoutSubviews` 메소드를 부릅니다.

뷰 컨트롤러는 `viewWillLayoutSubviews` 및 `viewDidLayoutSubviews` 메소드를 사용해서 레이아웃 프로세스에 영향을 줄 수 있는 추가 업데이트를 실행할 수 있습니다. 



레이아웃을 효과적으로 사용하는 팁

* **오토 레이아웃** 오토레이 아웃을 이용해서 만드는 constraint는 유연하고 쉬운 방법으로 컨텐츠를 크기가 다른 화면에 배치할 수 있습니다.
* **위.아래 레이아웃 가이드를 활용하자** 이 레이아웃 가이드에 내용을 배치하면 애용을 항상 볼 수 있습니다. 상단 레이아웃 가이드의 위치는 상태 표시 줄과 툴바의 높이를 결정합니다. 마찬가지로 하단 레이아웃 가이드의 위치는 탭 바 또는 툴바의 높이를 결정합니다.
* **뷰를 추가하거나 제거할 때 constraint를 업데이트 하는 것을 잊지 말자** 뷰를 동적으로 추가하거나 제거하는 경우 해당 constraint를 업데이트 해야 합니다.
* **뷰 컨트롤러의 뷰를 애니메이트하는 동안 constraint를 잠깐 제거하자** UIKit Core Animation을 사용하여 뷰에 애니메이션을 적용할 때 애니메이션 지속 시간에 대한 constraint를 제거하고 애니메이션이 끝나면 다시 추가합니다. 애니메이션 중에 뷰의 위치나 크기가 변경되면 constraint를 업데이트 해야 합니다.



### Managing Memory Efficiently

> 다음 표는 메모리를 할당하거나 할당을 취소할 가능성이 가장 높은 UIViewController의 메소드를 보여줍니다. 대부분의 할당 해제는 객체에 대한 강한 참조를 제거하는 것과 관련이 있습니다. 객체에 대한 강한 참조를 제거하려면 해당 객체를 가르키는 프로퍼티 또는 변수를 nil로 설정하면 됩니다.

|                Task                 |          Methods          |                Discussion                |
| :---------------------------------: | :-----------------------: | :--------------------------------------: |
|     뷰 컨트롤러에 필요한 중요한 데이터 구조를 할당      |          초기화 메소드          | 사용자가 정의한 초기화 메소드는 언제나 뷰 컨트롤러 객체를 좋은 상태로 유지할 책임이 있습니다. 적절한 작동을 보장하기 위해 필요한 모든 데이터 구조를 할당하려면 이 방법을 사용하면 됩니다. |
|        뷰에 표시 할 데이터를 할당하거나 로드        |       `viewDidLoad`       | viewDidLoad 메소드를 사용하여 표시 할 데이터 객체를 로드합니다. 이 메소드가 호출 될 때까지 뷰 객체가 존재하고 알려진 양호한 상태로 보장됩니다. |
|            메모리 부족 알림에 응답            | `didReceiveMemoryWarning` | 이 메소드를 사용하여 뷰 컨트롤러와 관련된 모든 중요하지 않은 객체의 할당을 해제합니다. 최대한 많은 메모리를 할당 해제해야 합니다. |
| View Controller에 필요한 중요한 데이터 구조를 해제 |         `dealloc`         | 이 메소드를 재정의하면 View Controller 클래스의 마지막 정리를 수행 할 수 있습니다. 시스템은 클래스의 인스턴스 변수와 속성에 저장된 객체를 자동으로 해제하므로 명시적으로 해제 할 필요가 없습니다. |

-----



## Implementing a Container View Controller

> 컨테이너 뷰 컨트롤러는 여러 뷰 컨트롤러의 컨텐츠를 하나의 사용자 인터페이스로 결합하는 하나의 방법입니다. 컨테이너 뷰 컨트롤러는 탐색을 편리하게 하고 기존 컨텐츠를 기반으로 새로운 사용자 인터페이스를 만드는데 자주 사용됩니다. UIKit의 컨테이너 뷰 컨트롤러 예로는 `UINavigationController`, `UITabBarController`,  `UISplitViewController`가 있습니다. 이것들을 통해 사용자 인터페이스의 다른 부분의 탐색을 쉽게 할 수 있습니다.



### Designing a Custom Container View Controller

> 거의 모든 면에서 컨테이너 뷰 컨트롤러는 루트 뷰와 일부 컨텐츠를 관리한다는 다른 컨텐츠 뷰 컨트롤러와 유사합니다. 차이점은 컨테이너 뷰 컨트롤러는 다른 뷰 컨트롤러에서 해당 내용의 일부를 가져옵니다. 가져오는 내용은 다른 뷰 컨트롤러의 뷰로 제한되며, 이 뷰는 자체 뷰 계층에 포함됩니다. 컨테이너 뷰 컨트롤러는 포함된 뷰의 크기와 위치를 설정하지만 원래의 뷰 컨트롤러는 해당 뷰 내부의 내용을 계속 관리합니다.

컨테이너 뷰 컨트롤러를 디자인 할 때는 항상 컨테이너와 포함된 뷰 컨트롤러 간의 관계를 이해해야 합니다. ViewController의 관계는 화면에 내용이 어떻게 표시되는 지와 컨테이너가 내부적으로 어떻게 관리하는 지를 알려줍니다. 

프로세스를 디자인하는 동안 다음 질문들을 해봐야합니다.

*  컨테이너의 역할은 무엇이며 컨테이너의 자식의 역할은 무엇입니까?
* 얼마나 많은 자식들이 동시에 보여지나요?
* 하위보기 컨트롤러는 컨테이너에 추가되거나 컨테이너에서 어떻게 제거됩니까?
* 아이들의 크기나 위치가 바뀔 수 있습니까? 이러한 변화가 발생하는 조건은 무엇입니까?
* 컨테이너가 자체의 장식 또는 탐색 관련 뷰를 제공합니까?
* 컨테이너와 그 아이 사이에 어떤 종류의 의사 소통이 필요합니까? 컨테이너가 UIViewController 클래스에 정의 된 표준 이벤트가 아닌 특정 이벤트를 자식 이벤트에보고해야합니까?
* 컨테이너 모양을 다른 방식으로 구성 할 수 있습니까? 그렇다면 어떻게?

컨테이너 뷰 컨트롤러의 구현은 다양한 객체의 역할을 정의하고 나면 비교적 간단합니다. UIKit의 유일한 요구사항은 컨테이너 뷰 컨트롤러와 자식 뷰 컨트롤러 사이에 공식적인 부모 - 자식 관계를 설정한다는 것입니다. 부모 - 자식 관계는 자식이 관련 시스템 메시지를 받는 것을 보장합니다. 그 외에도 실제 작업의 대부분은 레이아웃과 포함된 뷰 관리 중에 발생하며 각 컨테이너마다 다릅니다. 컨테이너의 컨텐츠 영역에 원하는 위치에 뷰를 배치하고 원하는 뷰의 크기를 조정할 수 있습니다. 뷰 계층 구조에 커스텀 뷰를 추가하여 decoration을 제공하거나 탐색을 지원할 수도 있습니다.



### Example: Navigation Controller

> UINavigationController 객체는 계층적 데이터 집합을 통한 탐색을 지원합니다. 네비게이션 인터페이스는 한 번에 하나의 하위 뷰 컨트롤러를 제공합니다. 인터페이스 상단의 네비게이션 바는 데이터 계층 구조의 현재 위치를 표시하고 한 수준 뒤로 이동하기 위한 뒤로가기 버튼을 표시합니다. 데이터 계층 구조로 내비게이션은 자식 뷰 컨트롤러에 맡겨지며 테이블이나 버튼을 사용할 수 있습니다.

뷰 컨트롤러 간의 탐색은 네비게이션 컨트롤러와 그 자식에 의해 공동으로 관리됩니다. 사용자가 자식 뷰 컨트롤러의 버튼이나 테이블의 행과 상호 작용할 때 자식은 뷰 컨트롤러에 새 뷰 컨트롤러를 밀어 넣도록 요청합니다. 자식은 새로운 뷰 컨트롤러의 내용을 처리하지만 네비게이션 컨트롤러는 전환 애니메이션을 관리합니다. 내비게이션 컨트롤러는 또한 내비게이션 바를 관리하며 맨 위의 뷰 컨트롤러를 닫기 위한 뒤로가기 버튼을 표시합니다.

다음 그림은 네비게이션 컨트롤러러의 구조와 뷰를 보여줍니다. 대부분의 컨텐츠 영역은 최상위 자식 뷰 컨트롤러로 채워지고 작은 부분만 네비게이션 바에 의해 차지됩니다.

![2](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/VCPG_structure-of-navigation-interface_5-1_2x.png)



네비게이션 컨트롤러는 한 번에 하나의 자식 뷰 컨트롤러만 보여줍니다. 네비게이션 컨트롤러는 사용가능한 공간에 맞게 자식의 크기를 조정합니다.



### Example: Split View Controller

> UISplitViewController 객체는 두 개의 뷰 컨트롤러 내용을 마스터 - 디테일 배열로 표시합니다. 이 배열에서 하나의 뷰 컨트롤러(마스터)의 내용에 따라 다른 뷰 컨트롤러에서 표시되는 세부 정보가 결정됩니다. 두 뷰 컨트롤러의 모습을 구성할 수는 있지만 현재 환경에 의해 관리됩니다. 수평 환경에서 스플릿 뷰 컨트롤러는 두 자식 뷰 컨트롤러를 나란히 표시하거나 마스터를 숨기고 필요에 따라 표시 할 수 있습니다. 컴팩트한 환경에서 스플릿 뷰 컨트롤러는 한 번에 하나의보기 컨트롤러 만 표시합니다.

다음 그림은 수평 환경에서 스플릿 뷰 인터페이스의 구조와 뷰를 보여줍니다. 스플릿 뷰 컨트롤러에는 기본적으로 컨테이너 뷰만 있습니다. 이 예제에서는 두 개의 자식 뷰가 나란히 보여집니다. 

![3](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/VCPG-split-view-inerface_5-2_2x.png)





### Configuring a Container in Interface Builder

> 디자인 시점에 부모-자식 컨테이너 관계를 만드려면 아래 그림처럼 스토리보드 씬에 컨테이너 뷰 객체를 추가해야 합니다. 컨테이너 뷰 객체는 자식 뷰 컨트롤러의 내용을 나타내는 placeholder 객체입니다. 이 뷰를 사용하면 컨테이너의 다른 뷰를 기준으로 자식 뷰의 크기를 조정하고 배치합니다.

![4](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/container_view_embed_2x.png)

하나 이상의 컨테이너 뷰가 있는 뷰 컨트롤러를 로드하면 인터페이스 빌더는 해당 뷰와 연관된 자식 뷰 컨트롤러를 로드합니다. 적절한 부모-자식 관계를 생성할 수 있도록 부모와 동시에 자식을 인스턴스화해야 합니다.

인터페이스 빌더를 사용하여 부모-자식 컨테이너 관계를 설정하지 않은 경우 코드로 직접 관계를 설정해야 합니다. 

```
Design time : 화면 혹은 코드를 작업할 때
Compile time : 컴파일 해서 빌드가 다 될 때까지
Run time : 빌드가 되고나서 실행 할 때
```

​                                                                                                                                                                 

### Implementing a Custom Container View Controller

> 컨테이너 뷰 컨트롤러를 구현하려면 뷰 컨트롤러와 자식 뷰 컨트롤러의 뷰를 관리하기 전에 부모-자식 관계를 설정해야 합니다. 이렇게 하면 UIKit에서 뷰 컨트롤러가 자식의 크기와 위치를 관리하고 있음을 알 수 있습니다. 인터페이스 빌더에서 이러한 관계를 만들거나 코드로 직접 만들 수 있습니다. 프로그래밍 방식으로 부모-자식 관계를 만들 때 뷰 컨트롤러 설정의 일부로 자식 뷰 컨트롤러를 명시적으로 추가 및 제거 합니다.



### Adding a Child View Controller to Your Content

> 자식 뷰 컨트롤러를 프로그래밍 방식으로 내용에 통합하려면 다음을 수행해서 관련 뷰 컨트롤러 간 부모-자식 관계를 만들어야 합니다.

* 컨테이너 뷰 컨트롤러의 `addChildViewController:` 메소드를 호출해야 합니다. 이 메소드는 컨테이너 뷰 컨트롤러가 이제 자식 뷰 컨트롤러의 뷰를 관리하고 있음을 UIKit에 알립니다.
* 컨테이너 뷰 계층 구조에 자식의 루트 뷰를 추가합니다. 이 과정에서 자식 프레임의 크기와 위치를 항상 설정해야 합니다.
* 자식의 루트 뷰의 크기와 위치를 관리하기 위한 constraint를 추가합니다.
* 자식 뷰 컨트롤러의 `didMoveToParentViewController:` 메소드를 호출해야 합니다.



다음 코드는 컨테이너가 자식 뷰 컨트롤러를 컨테이너에 포함하는 방법을 보여줍니다. 부모-자식 관계를 설정한 후 컨테이너는 자식의 프레임을 설정하고 자식 뷰를 자체 뷰 계층 구조에 추가합니다. 자식 뷰의 프레임 크기를 설정하는 것이 중요하며 뷰가 컨테이너에 올바르게 표시되도록 합니다. 뷰를 추가 한 후 컨테이너는 자식의  `didMoveToParentViewController:` 메소드를 호출하여 자식 뷰 컨트롤러에 뷰 소유권 변경에 응답할 수 있도록 해야합니다.

```objective-c
- (void) displayContentController: (UIViewController*) content {
   [self addChildViewController:content];
   content.view.frame = [self frameForContentController];
   [self.view addSubview:self.currentClientView];
   [content didMoveToParentViewController:self];
}
```

위 코드에서 자식의 `didMoveToParentViewController :` 메소드만 호출한다는 것을 알 수 있습니다. `addChildViewController :` 메소드가 자식의 `willMoveToParentViewController :` 메소드를 호출하기 때문입니다. `didMoveToParentViewController : ` 메소드를 직접 호출해야하는 이유는 자식 뷰를 컨테이너의 뷰 계층 구조에 포함해야만 메소드를 호출 할 수 있기 때문입니다.

오토 레이아웃을 사용하는 경우 자식 뷰를 컨테이너의 뷰 계층에 추가한 후 컨테이너와 자식 사이에 constraint를 설정해야 합니다. constraint는 자식의 루트 뷰의 크기와 위치에만 영향을줍니다. 자식 뷰 계층에서 루트 뷰 또는 다른 뷰의 내용을 변경하면 안됩니다.



### Removing a Child View Controller

> 컨텐츠에서 자식 뷰 컨트롤러를 제거하려면 다음을 수행해서 뷰 컨트롤러 간의 부모-자식 관계를 제거해야 합니다.

* 자식의 `willMoveToParentViewController :` 메소드를 값 nil로 호출합니다.
* 자식의 루트 뷰로 구성된 constraint를 제거하십시오.
* 컨테이너의 뷰 계층 구조에서 자식의 루트 뷰를 제거해야 합니다.
* 자식의 `removeFromParentViewController` 메소드를 호출하여 부모-자식 관계를 끝냅니다.

자식 뷰 컨트롤러를 제거하면 부모-자식 간의 관계가 영구적으로 끊어집니다. 더 이상 참조 할 필요가 없는 경우에만 자식 뷰 컨트롤러를 제거해야 합니다. 예를 들어, 내비게이션 컨트롤러는 새로운 자식 뷰 컨트롤러가 네비게이션 스택에 푸시될 때 현재 자식 뷰 컨트롤러를 제거하지 않습니다. 스택에서 pop할 때 제거됩니다.

다음 코드는 컨테이너에서 자식 뷰 컨트롤러를 제거하는 방법을 보여줍니다. 값이 nil 인 `willMoveToParentViewController :` 메소드를 호출하면 자식 뷰 컨트롤러를 변경할 준비를 할 수 있습니다. `removeFromParentViewController` 메소드는 또한 자식의 `didMoveToParentViewController :` 메소드를 호출하여 해당 메소드에 nil 값을 전달합니다. 부모 뷰 컨트롤러를 nil로 설정하면 컨테이너에서 자식 뷰의 제거가 완료됩니다.

```objective-c
- (void) hideContentController: (UIViewController*) content {
   [content willMoveToParentViewController:nil];
   [content.view removeFromSuperview];
   [content removeFromParentViewController];
}
```



### Transitioning Between Child View Controllers

> 하나의 자식 뷰 컨트롤러를 다른 자식 뷰 컨트롤러로 바꾸는 것에 애니메이션을 적용하려면 자식 뷰 컨트롤러의 추가 및 제거를 transition animation 프로세스에 통합해야 합니다. 애니메이션을 시작하기 전에 두 자식 뷰 컨트롤러가 모두 컨텐츠의 일부인지 확인하고 현재 자식에게 그 컨텐츠가 사라질 것임을 알려야 합니다. 애니메이션을 하는 동안 새로운 자식 뷰를 위치로 옮기고 오래된 자식 뷰를 제거합니다. 애니메이션이 완료되면 자식 뷰 컨트롤러가 제거됩니다.

다음 코드는 하나의 자식 뷰 컨트롤러를 트랜지션 애니메이션을 사용하여 다른 자식 뷰 컨트롤러로 바꾸는 방법의 예를 보여줍니다. 이 예제에서 새로운 뷰 컨트롤러는 현재 자식 뷰 컨트롤러가 차지하고 있는 직사각형에 애니메이션을 적용하여 화면 밖으로 내보내고 그 안으로 이동합니다. 애니메이션이 끝나면 컴플리션 블록은 자식 뷰 컨트롤러를 컨테이너에서 제거합니다. 이 예제에서 `transitionFromViewController : toViewController : duration : options : animations : completion :` 메소드는 컨테이너의 뷰 계층 구조를 자동으로 업데이트하므로 뷰를 직접 추가하거나 제거 할 필요가 없습니다.

```objective-c
- (void)cycleFromViewController: (UIViewController*) oldVC
               toViewController: (UIViewController*) newVC {
   // Prepare the two view controllers for the change.
   [oldVC willMoveToParentViewController:nil];
   [self addChildViewController:newVC];
 
   // Get the start frame of the new view controller and the end frame
   // for the old view controller. Both rectangles are offscreen.
   newVC.view.frame = [self newViewStartFrame];
   CGRect endFrame = [self oldViewEndFrame];
 
   // Queue up the transition animation.
   [self transitionFromViewController: oldVC toViewController: newVC
        duration: 0.25 options:0
        animations:^{
            // Animate the views to their final positions.
            newVC.view.frame = oldVC.view.frame;
            oldVC.view.frame = endFrame;
        }
        completion:^(BOOL finished) {
           // Remove the old view controller and send the final
           // notification to the new view controller.
           [oldVC removeFromParentViewController];
           [newVC didMoveToParentViewController:self];
        }];
}
```



### Managing Appearance Updates for Children

> 컨테이너에 자식을 추가하면 컨테이너는 자동으로 appearence 관련 메시지를 자식에게 전달합니다. 이는 모든 이벤트가 올바르게 전송되도록 하기 때문에 일반적으로 이상적인 동작입니다. 그러나 가끔씩 기본 동작이 해당 이벤트를 컨테이너에 적합하지 않은 순서로 보낼 수 있습니다. 예를 들어 여러 자식이 동시에 뷰 상태를 변경하는 경우 appearence 콜백이 모두 더 논리적인 순서로 동시에 발생하도록 변경 내용을 통합 할 수 있습니다.

형 콜백에 대한 책임을 맡으려면 컨테이너 뷰 컨트롤러에 `shouldAutomaticallyForwardAppearanceMethods` 메소드를 재정의하고 아래 코드에서처럼 NO를 반환합니다. NO를 반환하면 UIKit은 컨테이너 뷰 컨트롤러가 자식에게 모양이 변경 되었음을 알리는 것을 알 수 있습니다.

```objective-c
- (BOOL) shouldAutomaticallyForwardAppearanceMethods {
    return NO;
}
```

appearence transition이 발생하면 자식의 `beginAppearanceTransition : animated :` 또는 `endAppearanceTransition` 메소드를 호출합니다. 예를 들어, 컨테이너에 자식 프로퍼티가 참조하는 하나의 자식이 있는 경우 컨테이너는 이러한 메시지를 아래 코드와 같이 자식에 전달합니다.

```objective-c
-(void) viewWillAppear:(BOOL)animated {
    [self.child beginAppearanceTransition: YES animated: animated];
}
 
-(void) viewDidAppear:(BOOL)animated {
    [self.child endAppearanceTransition];
}
 
-(void) viewWillDisappear:(BOOL)animated {
    [self.child beginAppearanceTransition: NO animated: animated];
}
 
-(void) viewDidDisappear:(BOOL)animated {
    [self.child endAppearanceTransition];
}
```



### Suggestions for Building a Container View Controller

> 새로운 컨테이너 뷰 컨트롤러를 설계, 개발 및 테스트하는 데는 시간이 걸립니다. 개별적인 행동은 간단하지만 컨트롤러는 전체적으로 복잡할 수 있습니다. 자체 컨테이너 클래스를 구현할 때 다음을 고려해야 합니다.

* **자식 뷰 컨트롤러의 루트 뷰에만 액세스해야 합니다** 컨테이너는 각각의 자식의 루트 뷰 (즉, 자식의 view 속성에 의해 반환 된뷰)에만 액세스해야합니다. 
* **자식 뷰 컨트롤러는 해당 컨테이너에 대해 최소한의 정보만 가지고 있어야 합니다.** 자식 뷰 컨트롤러는 자체 컨텐츠에 집중해야 합니다. 컨테이너가 자식의 영향을 받는 행동을 허용하는 경우 상호 작용을 관리하기 위해 델리게이션 패턴을 사용해야 합니다.
* **먼저 일반적인 뷰를 사용하여 컨테이너를 디자인해야 합니다.** 자식 뷰 컨트롤러의 뷰 대신 일반 뷰를 사용하면 단순화된 환경에서 레이아웃 constraint와 애니메이션 전환을 테스트 할 수 있습니다. 일반 뷰가 예상대로 작동하면 자식 뷰 컨트롤러의 뷰를 위해 일반 뷰를 스왑합니다.



### Delegating Control to a Child View Controller

> 컨테이너 뷰 컨트롤러는 하나 이상의 자식에게 자체 appearence의 일부 요소를 위임할 수 있습니다. 다음과 같은 방법으로 컨트롤을 위임할 수 있습니다.

* **자식 뷰 컨트롤러가 상태 바 스타일을 결정하게 합니다.** 자식에게 상태 바 appearence를 위임하려면 컨테이너 뷰 컨트롤러에서 `childViewControllerForStatusBarStyle` 및 `childViewControllerForStatusBarHidden` 메소드 중 하나 또는 모두 재정의해야 합니다.
* **자식에게 자신이 선호하는 크기를 지정하게 해야 합니다.** 유연한 레이아웃을 가진 컨테이너는 자식의 preferredContentSize 속성을 사용하여 자식의 크기를 결정할 수 있습니다.

---



## Supporting Accessibility

> 접근성있는 앱은 몸이 불편한 사용자를 포함하여 모든 사람이 사용할 수 있는 기능으로 기능성과 유용성을 유지합니다. 접근이 가능하도록 iOS 앱은 시각 장애가 있는 사용자가 해당 요소와 상호 작용할 수 있도록 VoiceOver에 사용자 인터페이스 요소에 대한 정보를 제공해야합니다

* 컨트롤과 라벨같은 정적인 요소를 포함해서 인터페이싀 모든 사용자 요소에 접근 할 수 있는지 확인해야 합니다.
* 접근성 있는 요소가 정확하고 유용한 정보를 제공하는지 확인해야 합니다.



### Moving the VoiceOver Cursor to a Specific Element

> 앱에서 화면에 새로운 뷰를 제공하면 VoiceOver 커서의 위치를 설정하는 것이 좋습니다. 화면 레이아웃이 변경되면 VoiceOver 커서라고도 하는 VoiceOver 포커스 링이 왼쪽에서 오른쪽으로, 위에서 아래로 화면에 표시된 첫 번째 요소로 위치를 재설정합니다. 좀 더 적절한 요소 위에 커서를 놓으면 사용자의 인터페이스 탐색이 빨라집니다. 예를 들어 새 뷰 컨트롤러를 탐색 컨트롤러의 스택으로 밀어 넣으면 VoiceOver 커서가 네비게이션 바의 뒤로 버튼에 놓입니다. 해당 커서를 네비게이션 바의 제목이나 새로 푸시 된 페이지의 요소로 옮길 수 있습니다.

커서 위치를 변경하려면 `UIAccessibilityPostNotification` 함수를 사용하여 `UIAccessibilityScreenChangedNotification` 알림을 게시할 수 있습니다. 알림은 VoiceOver에 화면의 내용이 변경되었음을 알립니다. 알림을 게시 할 때 포커스를 지정할 요소를 지정해야 합니다.

```objective-c
@implementation MyViewController
- (void)viewDidAppear:(BOOL)animated {
    [super viewDidAppear:animated];
 
    // The second parameter is the new focus element.
    UIAccessibilityPostNotification(UIAccessibilityScreenChangedNotification,
                                    self.myFirstElement);
}
@end
```

회전에 의한 레이아웃 변경을 포함하여 레이아웃 변경은 VoiceOver 커서의 위치를 재설정합니다. 뷰 컨트롤러의 레이아웃이 변경되면 `UIAccessibilityLayoutChangedNotification` 알림을 게시합니다. `UIAccessibilityScreenChangedNotification` 알림과 마찬가지로 VoiceOver의 첫 번째 요소가 될 개체를 지정할 수 있습니다.



### Responding to Special VoiceOver Gestures

> VoiceOver는 앱의 특정 작업을 유발시키기 위한 다섯가지 특별한 제스처를 정의합니다.

* **Escape** 
* **Magic Tap**
* **Three-Finger Scroll**
* **Increment**
* **Decrement**

이러한 제스처를 사용해서 뷰 및 뷰 컨트롤러와 관련된 작업을 수행할 수 있습니다. UIKit은 제스처에 해당하는 메소드를 구현하는 메소드를 찾습니다. VoiceOver 포커스가 있는 요소부터 시작하여 리스폰더 체인을 사용하여 메소드를 검색합니다. 적절한 메소드를 구현하는 객체가 없으면 UIKit은 해당 동작에 대한 기본 시스템 동작을 수행합니다. 예를 들어 Magic Tap 제스처는 현재 뷰에서 앱의 델리게이트까지 Magic Tap 구현이 발견되지 않으면 음악 앱에서 음악 재생을 재생하고 일시 중지합니다.

핸들러에서 원하는 액션을 취할 수 있지만 VoiceOver 사용자는 앱의 액션이 특정 가이드 라인을 준수 할 것으로 기대합니다. 어떤 제스처와 마찬가지로 VoiceOver 동작은 패턴을 따라야만 접근 가능한 앱과의 상호 작용이 직관적이어야합니다.

#### Escape

Escape 제스처를 처리하려면 `accessibilityPerformEscape` 메소드를 사용해야 합니다. 다이얼로그나 경고와 같이 내용을 오버레이하는 뷰의 경우 오버레이를 닫는 방법을 사용하십시오. Escape 제스처의 기능은 컴퓨터 키보드의 Esc 키 기능과 같습니다. 임시 다이얼로그 또는 시트를 취소하여 주요 내용을 표시합니다. 또한 Escape 제스처를 사용하여 사용자 네비게이션 계층에서 한 수준 뒤로 이동할 수 있습니다. 이 제스처를 이미 처리하고있는 UINavigationController 객체를 이미 사용하고 있다면 이 제스처를 구현할 필요가 없습니다.

####Magic Tap

Magic Tap 동작을 처리하려면 `accessibilityPerformMagicTap` 메소드를 사용해야 합니다. Magic Tap 제스처는 자주 사용되거나 의도된 작업을 신속하게 수행합니다. 예를 들어, 전화 앱에서 Magic Tap은 전화를 받거나 끊습니다. Clock 앱에서 Magic Tap은 스톱워치를 시작하고 중지합니다. 이 제스처를 사용하여 VoiceOver 커서가 강조 표시하는 요소와 관련이 없는 작업을 유발 할 수 있습니다. 앱의 어느 곳에서나 Magic Tap 동작을 처리하려면 앱 대리인에 `accessibilityPerformMagicTap` 메소드를 구현하십시오.

####Three-Finger Scroll

VoiceOver 사용자가 세 손가락 제스처를 수행 할 때 `accessibilityScroll : ` 메소드를 사용하여 커스텀 뷰의 내용을 스크롤합니다. 책 페이지를 표시하는 커스텀 뷰는 이 제스처를 사용하여 페이지를 넘길 수 있습니다. 메소드에 전달 된 매개 변수는 스크롤 할 방향을 나타냅니다.

####Increment and Decrement

`accessibilityIncrement` 및 `accessibilityDecrement` 메소드를 사용하여 값을 증가 또는 감소시킵니다. UIAccessibilityTraitAdjustable 특성을 가진 요소는 이 메소드를 구현해야합니다.



### Observing Accessibility Notifications

> UIKit은 접근성 알림을 보내 앱에 관련 이벤트를 알립니다. 앱의 객체는 관련 알림을 관찰하고 이를 사용하여 적절한 작업을 수행 할 수 있습니다. 예를 들어, iBooks 앱은 UIAccessibilityAnnouncementDidFinishNotification 알림을 사용하여 VoiceOver가 페이지의 마지막 줄의 말하기를 마쳤을 때 페이지를 넘기고 계속 읽을 수 있습니다. 이 동작은 원활한 중단없는 독서 환경을 제공합니다.

접근성 알림을 위해 옵저버로 등록하려면 default notification center를 사용할 수 있습니다. 다음 코드는 녹음 메시지 읽기가 성공했는지 또는 사용자가 중단했는지 여부를 기록하는 뷰의 예를 보여줍니다.

```objective-c
@implementation MyViewController
- (void)viewDidLoad
{
    [super viewDidLoad];
 
    [[NSNotificationCenter defaultCenter]
        addObserver:self
           selector:@selector(didFinishAnnouncement:)
               name:UIAccessibilityAnnouncementDidFinishNotification
             object:nil];
}
 
- (void)didFinishAnnouncement:(NSNotification *)dict
{
    NSString *valueSpoken = [[dict userInfo] objectForKey:UIAccessibilityAnnouncementKeyStringValue];
    NSString *wasSuccessful = [[dict userInfo] objectForKey:UIAccessibilityAnnouncementKeyWasSuccessful];
    // ...
}
@end
```

구독 할 유용한 또 다른 알림은 UIAccessibilityVoiceOverStatusChanged 알림입니다. 이 알림을 사용하여 VoiceOver가 켜지거나 꺼질 때를 감지 할 수 있습니다. 앱이 일시 중지된 동안 이 알림이 발생하면 앱이 foreground로 돌아 왔을 때 알림을 받게됩니다.

---



## Preserving and Restoring State

> 뷰 컨트롤러는 상태 보존 및 복원 프로세스에서 중요한 역할을 합니다. 상태 보존은 일시 중지되기 전에 앱 구성을 기록하여 이 후 앱 실행시 구성을 복원 할 수 있도록 합니다. 이전 구성으로 앱을 반환하면 사용자의 시간이 절약되고 사용자 환경이 개선됩니다.

보존 및 복원 프로세스는 대부분 자동으로 수행되지만 iOS에 보존할 앱의 부분을 알려줘야 합니다. 앱의 뷰 컨트롤러를 보존하는 단계는 다음과 같습니다.

* (필수) 구성을 보존하려는 뷰 컨트롤러에 복원 식별자를 할당해야 합니다.
* (필수) iOS가 실행될 때 새로운 뷰 컨트롤러 객체를 생성하거나 찾ㅇ는 방법을 말해줍니다. 
* (선택) 각 뷰 컨트롤러에 대해 해당 뷰 컨트롤러를 원래 구성으로 되돌리기 위해 필요한 특정 구성 데이터를 저장합니다.



### Tagging View Controllers for Preservation

> UIKit은 보존을 위해 지시한 뷰 컨트롤러만 보존합니다. 각 뷰 컨트롤러에는 기본적으로 값이 nil 인 restorationIdentifier 속성이 있습니다. 이 속성을 유효한 문자열로 설정하면 UIKit에 뷰 컨트롤러와 해당 뷰를 유지해야 합니다. 복원 식별자는 프로그래밍 방식으로 또는 스토리 보드 파일에 할당 할 수 있습니다.

복원 식별자를 할당 할 때는 뷰 컨트롤러 계층의 모든 부모 뷰 컨트롤러에도 복원 식별자가 있어야 한다는 점에 유의해야 합니다. 보존 프로세스 중에 UIKit은 윈도우의 루트 뷰 컨트롤러에서 시작하여 뷰 컨트롤러 계층을 탐색합니다. 해당 계층의 뷰 컨트롤러에 복원 식별자가 없으면 뷰 컨트롤러와 모든 자식 뷰 컨트롤러 및 보여진 뷰 컨트롤러가 무시됩니다.



### Choosing Effective Restoration Identifiers

> UIKit은 복원 식별자 문자열을 사용하여 나중에 뷰 컨트롤러를 다시 작성하므로 코드에서 쉽게 식별 할 수있는 문자열을 선택해야 합니다. UIKit에서 뷰 컨트롤러 중 하나를 자동으로 만들 수 없는 경우 뷰 컨트롤러 및 모든 부모 뷰 컨트롤러의 복원 식별자를 제공하여 뷰 컨트롤러를 만들 것을 요청합니다. 이 일련의 식별자는 뷰 컨트롤러의 복원 경로를 나타내며 요청된 뷰 컨트롤러를 결정하는 방법입니다. 복원 경로는 루트 뷰 컨트롤러에서 시작하여 요청된 모든 뷰 컨트롤러를 포함합니다.

복원 식별자는 종종 뷰 컨트롤러의 클래스 이름입니다. 여러 곳에서 동일한 클래스를 사용하는 경우보다 의미있는 값을 할당 할 수 있습니다. 예를 들어보기 컨트롤러가 관리하는 데이터를 기반으로 문자열을 할당 할 수 있습니다.

모든 뷰 컨트롤러의 복원 경로는 고유해야 합니다. 컨테이너 뷰 컨트롤러에 두 개의 자식이있는 경우 컨테이너는 각 자식에게 고유 한 복원 식별자를 할당해야 합니다. UIKit의 일부 컨테이너 뷰 컨트롤러는 자동으로 자식 뷰 컨트롤러를 명확히해서 각 자식에 대해 동일한 복원 식별자를 사용할 수 있도록 합니다. 예를 들어 UINavigationController 클래스는 네비게이션 스택의 위치를 기반으로 각 자식에 정보를 추가합니다. 



### Excluding Groups of View Controllers

> 복원 프로세스에서 뷰 컨트롤러의 전체 그룹을 제외하려면 부모 뷰 컨트롤러의 복원 식별자를 nil로 설정하면 됩니다. 아래 그림은 복원 식별자를 nil로 설정하면 뷰 컨트롤러 계층 구조에 미치는 영향을 보여줍니다. 보존 데이터가 없으므로 나중에 뷰 컨트롤러가 복원되지 않습니다.

![5](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/state_vc_caveats_2x.png)



하나 이상의 뷰 컨트롤러를 제외해도 이후에 있을 복원 중에 이들 모두가 제거되진 않습니다. 실행시 아래 그림과 같이 앱의 기본 설정에 포함된 모든 뷰 컨트롤러가 생성됩니다. 이러한 뷰 컨트롤러는 기본 구성으로 재생성되지만 여전히 생성되어 있습니다.



![6](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/Art/state_vc_caveats_2_2x.png)



자동 보존 프로세스에서 뷰 컨트롤러를 제외해도 수동으로 보존하지 못하는 것은 아닙니다. 복원 아카이브에서 뷰 컨트롤러에 대한 참조를 저장하면 뷰 컨트롤러와 해당 상태 정보가 보존됩니다. 예를 들어, 위의 위의 그림에서 앱의 델리게이트가 네비게이션 컨트롤러의 자식들 3개를 저장한 경우 해당 상태가 유지됩니다. 복원 중에 앱 델리게이트는 뷰 컨트롤러를 다시 만들고 네비게이션 컨트롤러의 스택으로 밀어 넣을 수 있습니다.



### Preserving a View Controller’s Views

> 일부 뷰에는 뷰와 관련이 있지만 부모 뷰 컨트롤러에는 없는 추가 상태 정보가 있습니다. 예를 들어, 스크롤 뷰에는 보존하려는 스크롤 위치가 있습니다. 뷰 컨트롤러가 스크롤 뷰의 내용을 제공하는 역할을하는 동안 스크롤 뷰 자체는 시각적 상태를 유지해야합니다.

뷰의 상태를 유지하려면 다음을 따라야 합니다.

* 뷰의 restorationIdentifier 속성에 유효한 문자열을 할당합니다.
* 유효한 복원 식별자가 있는 뷰 컨트롤러의 뷰를 사용합니다.
* 테이블 뷰 및 콜렉션 뷰의 경우, UIDataSourceModelAssociation 프로토콜을 채택한 데이터 소스를 할당합니다.



뷰에 복원 식별자를 지정하면 UIKit에서 해당 뷰의 상태를 보존 아카이브에 기록해야 함을 알립니다. 나중에 뷰 컨트롤러가 복원되면 UIKit은 복원 식별자가 있는 뷰의 상태도 복원합니다.



### Restoring View Controllers at Launch Time

> 실행시 UIKit은 앱을 이전 상태로 복원하려고 시도합니다. 이때 UIKit은 보존된 사용자 인터페이스를 구성하는 뷰 컨트롤러 객체를 생성 (또는 찾게)하도록 앱에 요청합니다.

UIKit은 뷰 컨트롤러를 찾을 때 다음 순서로 검색합니다.

* **뷰 컨트롤러에 복원 클래스가 있는 경우 UIKit은 해당 클래스에 뷰 컨트롤러를 제공하도록 요청합니다.** UIKit은 연관된 복원 클래스의 `viewControllerWithRestorationIdentifierPath : coder :` 메소드를 호출하여 뷰 컨트롤러를 검색합니다. 이 메소드가 nil을 반환하면 앱이 뷰 컨트롤러를 다시 만들고 싶지 않으며 UIKit이 이를 찾지 않는다고 가정합니다.
* **뷰 컨트롤러에 복원 클래스가 없으면 UIKit은 뷰 위임자에게 뷰 컨트롤러를 제공 할 것을 요청합니다.** UIKit은 앱 델리게이트의 `viewControllerWithRestorationIdentifierPath : coder :` 메소드를 이용해서 복원 클래스가 없는 뷰 컨트롤러를 찾아냅니다. 이 메소드가 nil을 반환하면 UIKit은 뷰 컨트롤러를 암시적으로 찾으려고 시도합니다.
* **올바른 복원 경로가 있는 뷰 컨트롤러가 이미 있는 경우 UIKit은 해당 객체를 사용합니다.** 앱이 실행시 뷰 컨트롤러를 만들고 해당 뷰 컨트롤러에 복원 식별자가 있는 경우 UIKit은 복원 경로를 기반으로 암시적으로 이를 찾습니다.
* **뷰 컨트롤러가 원래 스토리 보드 파일에서 로드 된 경우 UIKit은 저장된 스토리보드 정보를 사용하여 이를 찾아내고 만듭니다.** UIKit은 뷰 컨트롤러의 스토리 보드에 대한 정보를 복원 아카이브에 저장합니다. 복원시 UIKit은 해당 정보를 사용하여 같은 스토리보드 파일을 찾고 다른 방법으로 볼 컨트롤러를 찾을 수 없는 경우 해당 뷰 컨트롤러를 인스턴스화합니다.

복원 클래스를 뷰 컨트롤러에 할당하면 UIKit에서 해당 뷰 컨트롤러를 암시적으로 검색 할 수 없습니다. 복원 클래스를 사용하면 실제로 뷰 컨트롤러를 만들지 여부를 제어 할 수 있습니다. 예를 들어 `viewControllerWithRestorationIdentifierPath : coder :` 메소드는 클래스가 뷰 컨트롤러를 다시 만들지 않아야 한다고 결정하면 nil을 반환 할 수 있습니다. 복원 클래스가 없는 경우 UIKit은 뷰 컨트롤러를 찾거나 만들 수 있는 모든 작업을 수행하고 복원합니다.

복원 클래스를 사용할 때 `viewControllerWithRestorationIdentifierPath : coder :` 메소드는 클래스의 새 인스턴스를 만들고 최소한의 초기화를 수행한 다음 결과 객체를 반환해야 합니다. 아래 코드는 이 메소드를 사용하여 스토리보드에서 뷰 컨트롤러를 로드하는 방법의 예를 보여줍니다. 뷰 컨트롤러는 원래 스토리보드에서 로드되었으므로 이  메소드는 `UIStateRestorationViewControllerStoryboardKey` 키를 사용하여 아카이브에서 스토리보드를 가져옵니다. 이 메소드는 뷰 컨트롤러의 데이터 필드를 구성하려고 시도하지 않습니다. 

```objective-c
+ (UIViewController*) viewControllerWithRestorationIdentifierPath:(NSArray *)identifierComponents
                      coder:(NSCoder *)coder {
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

복원 식별자와 복원 클래스를 재할당하는 것은 뷰 컨트롤러를 수동으로 다시 만들 때 사용하는 것이 좋습니다. 복원 식별자를 복원하는 가장 간단한 방법은 identifierComponents 배열의 마지막 항목을 잡고 뷰 컨트롤러에 할당하는 것입니다.

실행시 앱의 기본 스토리보드 파일에서 생성 된 객체의 경우 각 객체의 새 인스턴스를 만들면 안됩니다. UIKit에서 이러한 객체를 암시적으로 찾거나 `viewControllerWithRestorationIdentifierPath : coder :` 메소드를 이용해서 기존 객체를 찾습니다.



### Encoding and Decoding Your View Controller’s State

> 보존을 위해 예정된 각 객체에 대해 UIKit은 객체의 `encodeRestorableStateWithCoder :` 메소드를 호출하여 상태를 저장할 수있는 기회를 제공합니다. 복원 프로세스 중에 UIKit은 일치하는 `decodeRestorableStateWithCoder :` 메소드를 호출하여 해당 상태를 디코딩하고 이를 객체에 적용합니다. 이 메소드의 구현은 선택 사항이지만 뷰 컨트롤러에 대해서는 권장됩니다.

다음 정보를 저장하고 복원할 수 있습니다.

* 표시되는 데이터에 대한 참조 (데이터 자체는 아님)
* 컨테이너 뷰 컨트롤러의 경우 자식 뷰 컨트롤러에 대한 참조
* 현재 선택 항목에 대한 정보
* 사용자가 구성 가능한 뷰가 있는 뷰 컨트롤러의 경우 해당 뷰의 현재 구성에 대한 정보

인코드 및 디코딩 메소드에서 코더가 지원하는 객체 및 모든 데이터 유형을 인코드 할 수 있습니다. 뷰 및 뷰 컨트롤러를 제외한 모든 객체의 경우 객체는 NSCoding 프로토콜을 채택하고 해당 프로토콜의 메소드를 사용하여 상태를 작성해야합니다. 뷰 및 뷰 컨트롤러의 경우 코더는 객체의 복원 식별자를 저장하고 보존 할 수있는 객체 목록에 추가합니다. 그러면 객체의 `encodeRestorableStateWithCoder :` 메소드가 호출됩니다.

뷰 컨트롤러의 `encodeRestorableStateWithCoder :` 및 `decodeRestorableStateWithCoder :` 메소드는 구현시 어느 시점에서 super를 호출해야합니다. super를 호출하면 부모 클래스에 추가 정보를 저장하고 복원 할 수 있습니다. 아래 코드는 지정된 뷰 컨트롤러를 식별하는 데 사용되는 숫자 값을 저장하는 메소드의 샘플입니다.



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



코더 객체는 인코딩 및 디코딩 프로세스 중에 공유되지 않습니다. 보존 가능 상태를 가진 각 객체는 자체 코더 객체를 받게 됩니다. 이러한 고유 코더의 사용은 키 사이의 네임 스페이스 충돌에 대해 걱정할 필요가 없다습니다. 하지만UIApplicationStateRestorationBundleVersionKey, UIApplicationStateRestorationUserInterfaceIdiomKey, UIStateRestorationViewControllerStoryboardKey 키 이름을 직접 사용하면 안됩니다. 이러한 키는 UIKit에서 뷰 컨트롤러의 상태에 대한 추가 정보를 저장하는 데 사용됩니다.



### Tips for Saving and Restoring Your View Controllers

> 뷰 컨트롤러에서 상태 보존 및 복원에 대한 지원을 추가 할 때 다음 지침을 고려해야 합니다.

* **모든 뷰 컨트롤러를 보존하지 않아야 하는 경우도 있습니다.** 어떤 경우에는 뷰 컨트롤러를 유지하는 것이 적절하지 않을 수 있습니다. 예를 들어 앱이 변경 사항을 표시하는 경우 작업을 취소하고 이전 화면으로 앱을 복원 할 수 있습니다. 이 경우 새 암호 정보를 묻는 뷰 컨트롤러를 보존하지 않습니다.
* **복원 프로세스 중에 View Controller 클래스를 바꾸면 안됩니다.** 상태 보존 시스템은 유지하는 뷰 컨트롤러의 클래스를 인코딩합니다. 복원하는 동안 앱이 클래스가 원래 객체의 클래스와 일치하지 않거나 객체의 자식 클래스가 아닌 객체를 반환하면 시스템은 뷰 컨트롤러에 상태 정보를 디코딩하도록 요청하지 않습니다. 따라서 이전 뷰 컨트롤러를 완전히 다른 것으로 바꾸면 객체가 복원되지 않습니다.
* **상태 보존 시스템은  뷰 컨트롤러가 의도한대로 사용될 것을 기대합니다.** 복원 프로세스는 인터페이스를 재구성하기 위해 View Controller의 포함 관계에 의존합니다. 컨테이너 뷰 컨트롤러를 제대로 사용하지 않으면 보존 시스템에서 뷰 컨트롤러를 찾을 수 없습니다. 예를 들어, 해당 뷰 컨트롤러 간에 포함 관계가 없으면 뷰 컨트롤러의 뷰를 다른 뷰에 포함하면 안됩니다.



