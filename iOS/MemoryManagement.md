# MemoryManagement

* Java와 달리 Objective-C에서는 가비지 컬렉션은 존재하지 않는다. 
* 대신에 MRC와 ARC 방식을 이용해서 메모리를 관리한다.


## 왜 메모리 관리를 해야하는가?


<p><img src=“https://github.com/Yongjai/TIL/tree/master/img/1.png” alt=“Memory Graph”></p>


위 그래프는 iPhone의 기본적인 메모리 할당을 보여줍니다.
예를 들어, 파란색 그래프를 보면 전체 128 중의 12를 Grapics 처리를 위한 메모리가 기본적으로 할당되어 있습니다.
이렇게 기본적으로 할당되어 있는 그래프들을 뺀 나머지 부분들이 우리에게 남습니다.


<p><img src=“https://github.com/Yongjai/TIL/tree/master/img/2.png” alt=“Memory Overview”></p>


위 그래프는 남은 부분을 사용하며 생기는 메모리 부하를 나타낸 그래프 입니다.
일정 수준을 넘어가면 Warning이 발생하고 더 올라가면 사용하지 않는 가장 오래된 앱들을 꺼버리며 메모리를 정리합니다.
그럼에도 메모리가 부족하게 되면 현재 켜져있는 앱이 강제로 killed 됩니다.

이와 같은 메모리 관리에서 알아야 하는 것이 Reference Counting입니다.
Reference Counting은 메모리에 할당된 객체에 참조중인 개수를 세는 것 입니다.
**즉, 특정 객체에 얼마나 많은 참조가 있는지를 숫자로 세는 것!**


## MRC
* 수동 레퍼런스 카운트(MRC, Manual Reference Count)
* 말 그대로 개발자가 수동으로 Reference Counting을 하는 것 입니다.
* Objective-c에서만 지원하며 Swift에서는 지원하지 않습니다.
* 객체를 참조하고 싶을 때 [retain] // 참조를 풀고 싶을 때 [release]


<p><img src=“https://github.com/Yongjai/TIL/tree/master/img/2.png” alt=“MRC Example Code”></p>


위 그림을 보면,
첫 번째 줄 코드를 보면 Person 클래스에서 me 객체를 만들었더니 Reference Count가 1이 되었습니다.
두 번째 줄 코드를 보면 retain을 이용해 객체를 참조했더니 Reference Count가 2가 되었습니다.
그리고 3번째 4번째에서 release를 통해 참조를 풀어서 0을 만들었습니다.
이렇게 되면 dealloc이 자동으로 호출되어 me 객체는 완전히 소멸됩니다.

**간단한 코드였지만 이런식으로 직접 메모리를 관리하는 방식이 MRC 방식입니다.**

하지만 이 방식에는 문제가 있습니다. 어떤 메소드에서 retain이 되어 release를 해주는데 만약 값을 return 해줘야 한다면 이미 release를 통해 Reference Count가 0이 되어 객체가 이미 죽어있게 됩니다. 그럴경우, 어쩔 수 없이 return 값을 받는 곳에서 release를 해줘야 하는데 이럴 경우 코드가 더럽습니다^^

이때, 사용할 수 있는 것이 바로 Autorelease입니다.

 
## Autorelease
* autorelease는 AutoreleasePool에 release가 필요한 객체들을 보관합니다.
* 어떤 시점이 되면 pool에서 drain이 되고 release가 됩니다.

autorelease는 AutoreleasePool에 객체를 push 해놓고 release를 나중에 처리해줍니다. push 된 객체는 pool에서 drain이 호출 될 때 release 되고 dealloc됩니다.

하지만 autorelease는 쓰지 않는 것이 좋다고 애플에서도 직접 밝혔습니다.
(만들어 놓고보니 release를 쓰는 것보다 고비용…)

## MRC Summary

* alloc/new/copy/retain을 사용했다면 release를 해주어야 합니다.
* retain을 사용했다는 말은 언젠가 release를 호출해야 합니다.
* release를 하더라도 Reference Count가 0이 되기 전까진 메모리에서 없어지지 않습니다.
* iOS에서는 release가 더 좋다고 합니다.
* 앱이 종료되면 모든 객체는 release 됩니다.


## ARC
* 자동 레퍼런스 카운트(Automatic Reference Count)
* 말 그대로 자동으로 메모리 관리를 처리합니다.

### ARC Rules
* Memory Method에 접근할 수 없습니다.
	* retain/release/autorelease 등을 부를 수 없습니다.
	* compiler가 알아서 처리합니다.

* C 구조체에 객체를 넣을 수 없습니다.

* id와 void 간에 Casual Casting을 할 수 없습니다.
	* compiler는 void*가 retain 되었는지 알아야 합니다.
	* __bridge를 이용해서 casting을 할 수 있습니다. (__bridge, __bridge_retain 등)

* NSAutoreleasePool을 사용할 수 없습니다.
	* @autoreleasepool 블럭을 사용해야 합니다. (ARC가 아니더라도)

