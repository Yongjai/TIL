## Effective Objective-C 

#### Item#47. 시스템 프레임워크를 숙지하라

시스템 프레임워크의 종류는 많이 존재합니다. 시스템 프레임워크는 크게 두 가지로 나눠서 생각할 수 있습니다.

* Cocoa Framework = Foundation + AppKit
* Cocoa-Touch Framework = Foundation + UIKit

Foundation은 화면과 상관없이 공통적으로 사용되는 라이브러리를 모아놓았습니다.

AppKit은 Mac용 앱을 만들 때 사용하는 라이브러리들을 모아놓았습니다.

UIKit은 iOS용 앱을 만들 때 사용하는 라이브러리들을 모아놓았습니다.



Foundation이라는 프레임워크 아래에는 Core Foundation이라는 C API 방식의 구조체가 존재합니다. C로 작성된 API는 Objective-C 런타임에 바로 전달하기 때문에 속도가 빠르다는 장점이 있습니다. 그래서 Foundation에서 사용되는 Objective-C 객체와 Core Foundation에서 사용되는 C 구조체 간의 bridge를 해야하는 경우가 있습니다. 이러한 방식을 무비용 전환(Toll-Free Bridge)라고 불르는데 __bridge의 형식으로 사용할 수 있습니다. 예를들어, Foundation의 문자열은 NSString이지만 CoreFoundation에서는 CFString으로 사용되는데 서로 변환을 시키는게 가능하다는 의미입니다. 이 내용은 잠시 후에 다루겠습니다.

Foundation과 CoreFoundation과 함께 많은 라이브러리가 있는데 많이 사용되는 것들에 대해 알아보고자 합니다. 

- Core Audio : 오디오 하드웨어와 인터페이스를 위한 C 수준 API
- Core Video : 비디오 하드웨어와 인터페이스하는 C 수준 API
- AVFoundation : 오디오/비디오를 보여주고, 녹화, 재생하기 위한 Objective-C 객체 제공
- Core Data : 객체 그래프를 Persistence 저장소에 저장하고, 복원, 관리하기 위한Objective-C 객체 제공
- Core Text : 고성능 타이포그래피 텍스트 조판과 렌더링을 위한 C 수준 API
- Core Graphics : 2D 렌더링을 위한 OpenGL ES 추상화한 C 수준 API
- Core Animation : Quartz 프레임워크의 일부로 화면 요소를 렌더링하고 애니메이션하기 위한 Objective-C 객체와 C 수준 API 제공

 Low-Level로 들어가게 되면 C 수준의 API를 자주 사용해야 합니다. 예를 들어, AVFoundation이라는 오디오, 비디오와 관련해서 만들어진 Objective-C로 만들어진 프레임워크다. 그런데 AVFoundation을 통해 만들어진 비디오에서 Frame 단위로 버퍼를 이용해서 픽셀로 받고 싶다고 한다면 Core Video의 CVBuffer, CVImage와 같은 클래스를 가져다가 사용해야 합니다. 즉, AVFoundation을 사용하게되면 결국 Core Audio와 Core Video를 같이 사용할 일이 생기게 됩니다. 

Core Text는 간단히 말하면 E-Book을 만드는 모든 게 들어있다고 생각하면 편합니다. 레이아웃, 이미지, 텍스트, 폰트와 같은 세부적인 것들에 대해 조절할 수 있습니다. 그래서 E-Book Viewer를 만든다고 한다면 Core Text로 랜더링을 해서 HTML처럼 보이게 할 수 있습니다. 

Core Graphics는 2D 그래픽을 그릴 때 사용됩니다. 재밌는 사실은 내부적으로 3D 렌더링을 위한 OpenGL로 구현이 되어 있으며 그 위에 2D를 그리기 위한 엔진이 올라가 있습니다. 그리고 Core Graphics는 Core Animation과 함께 사용할 수 있습니다. (AVFoundation과 Core Audio/Video 처럼)  



#### Item#48. 반복문에는 블록 열거를 사용하라

한 마디로 말하면 우리가 사용하는 일반적인 for Loop를 사용하지 말라는 의미입니다. 일반적인 for Loop는 다음과 같습니다.

````objective-c
// Array
NSArray *anArray = /* ... */
for (int i = 0; i < anArray.count; i++) {
  id object = anArray[i];
  // ...
}

// Dictionary
NSDictionary *aDictionary = /* ... */
NSArray *keys = [aDictionary allKeys];
for (int i = 0; i < keys.count; i++) {
  id key = keys[i];
  id value = aDictionary[key]; 
  // ...
}

// Set
NSSet *aSet = /* ... */
NSArray *objects = [aSet allObjects];
for (int i = 0; i < objects.count; i++) {
  id object = objects[i]; 
  // ...
}
````

하지만 위의 방식은 사용하지 말아달라는 것이 저자의 요구입니다. (하지만 친숙하긴 하네요ㅎㅎ)

Fast Enumerator 방식도 있습니다.

````objective-c
// Array
NSArray *anArray = /* ... */
for (id object in anArray) {
// ... 
}

// Dictionary
NSDictionary *aDictionary = /* ... */
for (id key in aDictionary) {
    id value = aDictionary[key];
// ...
}

// Set
NSSet *aSet = /* ... */
for (id object in aSet) {
  // ...
}

````

Fast Enumerator 방식은 기존의 for Loop보다 훨씬 간결하고 속도도 가장 빠르다는 장점이 있습니다. 

하지만 블록 기반 열거 방법도 존재합니다. 

````objective-c
// Array
NSArray *anArray = /* ... */
[anArray enumerateObjectsUsingBlock:^(id object, NSUInteger idx, BOOL *stop) { 
  // ...
    if (shouldStop) {
      *stop = YES;
    }
}
 
// Dictionary
NSDictionary *aDictionary = /* ... */
[aDictionary enumerateKeysAndObjectsUsingBlock:^(id key, id object, BOOL *stop) { 
  // ...
   if(shouldStop) {
     *stop = YES; 
   }
}
````





