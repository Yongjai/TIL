# InterView Questions

#### 추가 하든지 말든지... 




## iOS

- 자신만의 Custom View를 만들려면 어떻게 해야하는지 설명하시오.
````
그냥 만들면 되지 뭘 어떻게 해야하는지 설명을 해…
````

- Fast Enumration 이란 무엇인지 설명하시오. 
````objective-c
C언어에서 사용하는 일반적인 for문의 형태가 아닌 새로운 형태의 for문이다.<br />
fast enumration은 열거형 변수들 즉, collection data들이 있을 때 사용할 수 있다. NSArray, NSDictionary가 대표적이다. 
예제 코드는 다음과 같다.
NSArray *array = [NSArray arrayWithObjects:@"One", @"Two", @"Three", @"Four", nil];
for (NSString *element in array) {
NSLog(@"element: %@", element);
}
// element: One	
// element: Two
// element: Three
// element: Four
````

- Struct 가 무엇이고 어떻게 사용하는지 설명하시오.
````
그냥 C 구조체랑 똑같은 건데,,, 한번도 써본 적 없고 잘 안쓰는 것 같다.
````

- Mutable 객체와 Immutable 객체는 어떤것이 있는지 예를 들고, 차이점을 설명하시오.
````
간단한 비교를 위한 예로는 NSMutableArray/NSMutableDictionary와 NSArray/NSDictionary가 있다.<br />
Mutable 객체는 생성 후 변경이 가능합니다. 크기를 늘린다던가, 값을 바꾼다던가… 하지만 Immutable 객체는 생성 후 절대 변경이 불가능합니다.<br />
즉, 변경을 하려면 선언부를 고쳐서 다시 선언을 해주어야 합니다.  
````
- Retain Count 방식에 대해 설명하시오.
````
Objective-C로 메모리를 관리하는 방식은 ARC와 MRC 방식이 존재합니다. 여기서 Retain Count는 MRC방식입니다.<br />
직접 메모리 관리를 해주는 것인데 Retain Count를 이용해 메모리를 관리합니다.<br />
객체를 할당해주면 Retain Count가 1 증가하고, 객체를 release해주면 Retain Count가 1감소합니다.<br />
0이 되면 메모리에서 없어지게 되어 혹시나 생길 수 있는 메모리 누수를 관리할 수 있습니다.<br />
자세한 설명은 TIL에 정리되어 있음.<br />
````
- Bounds 와 Frame 의 차이점을 설명하시오.
- Delegate란 무언인가 설명하고, retain 되는지 안되는지 그 이유를 함께 설명하시오.
- NSObject부터 UIButton 까지 상속 과정의 계층과 역할을 설명하시오.
- dynamic과 property 의미와 차이를 설명하시오.
- -performSelector:withObject:afterDelay: 메시지를 보내면 인자값의 객체는 retain되는가? 그 이유를 함께 
  설명하시오.
- 특정 객체를 autorelease 하기 위해 필요한 사항과 과정을 설명하시오.
- NSCoder 클래스는 어떤 상황에서 어떻게 써야 하는지 설명하시오.
- NSOperationQueue는 어떤 역할을 담당하고 무엇을 위해 사용하는지 설명하시오.
- 다음 코드를 실행하면 어떤 일이 발생할까 추측해서 설명하시오.  
  `Ball *ball = [[[[Ball alloc] init] autorelease] autorelease];`
````
Crash가 발생할 것 같다.(아마도?)<br />
Ball이라는 객체를 할당하고 초기화 시켜주었다. 그리고 autorelease 풀에 넣어 선언부가 포함된 함수가 끝나면 자동으로 release 될텐데 여기서 다시 release를 시킨다?<br />
이미 풀에 있는 객체를 어떻게 풀에 또 넣겠는가? 고로 crash가 발생할 것 같다.
````
- NSOperationQueue 와 GCD Queue 의 차이점을 설명하시오.
- @property로 선언한 NSString* title 의 getter/setter 메서드를 구현해보시오.
- ARC 대신 Manual Reference Count 방식으로 구현할 때 꼭 사용해야 하는 메서드들을 쓰고 역할을 설명하시오.
- ARC란 무엇인지 설명하시오.
- retain 과 assign 의 차이점을 설명하시오.
- Category 방식에 대해 설명하시오.
- 코코아에서 Delegation 방식을 사용하는 경우와 메서드 이름 규칙에 대해 설명하시오.
- Delegate 패턴을 활용하는 경우를 예를 들어 설명하시오.
- Singleton 패턴을 활용하는 경우를 예를 들어 설명하시오.
- Category 확장과 Subclass 확장의 차이점을 설명하시오.
- NotificationCenter 동작 방식과 활용 방안에 대해 설명하시오.
- Delegates와 Notification 방식의 차이점에 대해 설명하시오.
- @property에서 atomic과 nonatomic 차이점을 설명하고, 어떤것이 안전한지, 어느것이 기본인지 설명하시오.
- 실제 디바이스가 없을 경우 개발 환경에서 할 수 있는 것과 없는 것을 설명하시오.
- JSON 데이터를 처리하는 방식과 파서, 객체 변환 방식에 대해 설명하시오.
- iOS 앱을 만들고, User Interface를 구성하는 데 필수적인 프레임워크 이름은 무엇인가?
- UIKit 클래스들을 다룰 때 꼭 처리해야하는 애플리케이션 쓰레드 이름은 무엇인가?
- 앱이 foreground에 있을 때와 background에 있을 때 어떤 제약사항이 있고, 상태 변화에 따라 다른 동작을 처리하기 
  위한 델리게이트 메서드들을 설명하시오.
- 앱이 In-Active 상태가 되는 시나리오를 설명하시오.
- UIApplication 객체의 컨트롤러 역할은 어디에 구현해야 하는가?
- 앱이 시작할 때 main.c 에 있는 UIApplicaionMain 함수에 의해서 생성되는 객체는 무엇인가?
- 앱의 콘텐츠나 데이터 자체를 저장/보관하는 특별학 객체를 무엇이라고 하는가?
- 앱 화면의 콘텐츠를 표시하는 로직과 관리를 담당하는 객체를 무엇이라고 하는가?
- 모든 View Controller 객체의 상위 클래스는 무엇이고 그 역할은 무엇인가?
- UIWindow 객체의 역할은 무엇인가?
- View 객체에 대해 설명하시오.
- UIView 에서 Layer 객체는 무엇이고 어떤 역할을 담당하는지 설명하시오.
- App Bundle의 구조와 역할에 대해 설명하시오.
- @property로 선언한다는 것의 의미를 설명하고, .h에 넣을 경우와 .m에 넣을 경우 차이점을 설명하시오.
- XML Parser를 사용하려면 어떻게 해야 하는지 설명하시오.
- 웹 서버와 HTTP 연결을 사용해서 데이터를 주거나 받으려면 사용해야 하는 클래스와 동작을 설명하시오.
- DOM 방식과 SAX 방식 XML Parser의 차이점을 설명하고 iOS XML Parser는 어떤 방식인지 설명하시오.
- NSURLConnection 에서 사용하는 Delegate 메서드들에 대해 설명하시오.
- TableView를 동작 방식과 화면에 Cell을 출력하기 위해 최소한 구현해야 하는 DataSource 메서드를 설명하시오.
- MVC 구조에 대해 블록 그림을 그리고, 각 역할과 흐름을 설명하시오.
- instance 메서드와 class 메서드의 차이점을 설명하시오.
- Objective-C 에서 캡슐화된 데이터를 접근하기 위한 방법들을 설명하시오.
- shallow copy와 deep copy의 차이점을 설명하시오.
- KVO 동작 방식에 대해 설명하시오.
- 하나의 View Controller 코드에서 여러 TableView Controller 역할을 해야 할 경우 어떻게 구분해서 구현해야 
  하는지 설명하시오.
- Push Notification 방식에 대해 설명하시오.
- Responder Chain 구조에 대해 설명하고, First Responder 역할에 대해 설명하시오.
- Objective-C 에서 Protocol 이란 무엇인지 설명하시오.
- Autorelease Pool을 사용해야 하는 상황을 두 가지 이상 예로 들어 설명하시오. 
- In-App Purchase Product type 을 설명하시오.
- Synchronous 방식과 Asynchronous 방식으로 URL Connection을 처리할 경우의 장단점을 비교하시오.
- UINavigationController 의 역할이 무엇인지 설명하시오.
- Objective-C++ 방식이 무엇인지 설명하고, 어떤 경우 사용해야 하는지 설명하시오.
- Foundation Kit은 무엇이고 포함되어 있는 클래스들은 어떤 것이 있는지 설명하시오.
- 멀티 쓰레드로 동작하는 앱을 작성하고 싶을 때 고려할 수 있는 방식들을 설명하시오.
- Plist 파일 구조와 Plist 파일에 저장된 데이터를 다루기 적합한 클래스를 설명하시오.
- Core Data와 Sqlite 같은 데이터 베이스의 차이점을 설명하시오.
- Foundation 과 Core Foundation 프레임워크의 차이점을 설명하시오.
- unnamed category 방식에 대해 설명하시오.
- Strong 과 Weak 참조 방식에 대해 설명하시오.
- strong, weak, unsafe_unretained, autoreleasing 예약어에 대해 설명하시오.
- GCD API 동작 방식과 필요성에 대해 설명하시오.


## Basic

- 프로그램과 프로세스의 차이(+쓰레드)
````
- 프로그램 
  - 사용자가 원하는 일을 처리할 수 있도록 프로그래밍 언어를 사용하여 올바른 수행절차를 표현해 놓은 명령어들의 집합

- 프로세스
  - 간단하게 말하면 '실행 중인 프로그램' 입니다.
  - 프로그램을 실행하면 OS로부터 실행에 필요한 자원(메모리)을 할당받아 프로세스가 됨
  - 프로세스는 데이터와 메모리등의 자원과 `쓰레드`로 구성

- 쓰레드
  - 쓰레드란 프로세스의 자원을 이용해서 실제로 작업을 수행하는 것을 말해요.
  - 독립적으로 실행되는 코드의 집합. 
  - 모든 프로세스는 하나 이상의 쓰레드를 가진다. 
````
- HTTP란 무엇인가?

````
HTML을 전송하는데 사용되는 통신 규약.
````

- Array vs Linked List

  [Array vs Linked List](https://github.com/Yongjai/TIL/blob/master/DataStructure/ArrayAndLinkedList.md/)

- 2진법에서의 보수 사용 방법

  ```
  5 - 9 를 예를 들어 설명하겠습니다. 우선 2진법으로 바꿔야 합니다. 
  5 = 0101(2)
  9 = 1001(2)
  5 - 9는 5 + (-9)로 바꿀 수 있습니다. 그러면 1001을 보수로 바꿔야 합니다. 그러면 1111-1001 = 0110 + 1 = 0111 이 됩니다. 0101 + 0111 = 1100 이 됩니다.<br /> 그런데  올림 수가 없으므로 음수라는 뜻이 됩니다.
  다시 1100을 보수로 바꾸면 1111 - 1100 = 0011 + 1 = 0100 즉, -4 가 됩니다. 이런식으로 뺄셈을 하면 됩니다.
  ```

- Stack vs Queue


  [Stack vs Queue](https://github.com/Yongjai/TIL/blob/master/DataStructure/StackAndQueue.md/)


## Network

- TCP vs UDP


````
TCP는 stream 인터페이스 활용, UDP datagram 활용
TCP(전송 제어 프로토콜) : 신뢰성 있음 / 연결형 프로토콜(연결형. 전송 전에 연결을 먼저 맺어야 한다. 3way-handshake)/ 속도 느림/ 손실된 데이터 재전송함/ 신뢰할 수 있는 방법으로 데이터를 송신해야 하는 대부분의 프로토콜과 애플리케이션. 대부분의 파일/메시지 전송 프로토콜을 포함함 / 스트림기반의 인터페이스 / 슬라이딩윈도우 시스템으로 데이터 흐름 관리 가능
TCP 에는 connection overhead (최소 latency 2번 )가 있고, flow control 과정에서 발생할 수 있는 문제가 있다.

UDP(사용자 데이터그램 프로토콜) : 신뢰성 없음 / 비 연결형 프로토콜 / 속도 빠름 / 재전송안함/ 데이터 완전성보다 전달속도가 중요하고 소량의 데이터를 송신하고, 멀티캐스트/ 브로드캐스트를 사용하는 어플리케이션에서 사용 / 메시지 기반/ 
````

- OSI 7 Layer


````
Physical Layer : 네트워크 통신을 할 때, 물리적인 전기 신호를 보낸다. 전기적, 기계적, 기능적인 특성을 이용해서 bit 단위로 케이블로 전송하는데 사용되는 장비로는 케이블, 허브, 리피터가 있다.

Data Link Layer : 물리적인 주소를 담당하고 데이터 전송의 신뢰성을 제공한다. 물리적인 MAC Address를 사용해 오류와 흐름을 관리한다. Frame을 데이터 단위로 사용하며 사용되는 장비로는 스위치와 브릿지가 있다.

Network Layer : 논리적인 주소를 담당하고 데이터의 이동 경로를 결정한다. 논리적인 IP 주소를 사용하고 데이터의 목적지까지 최적 경로를 찾아서 Packet 단위로 전달한다. 사용되는 장비로는 라우터와 L3 스위치가 있다.

Transport Layer : 통신 데이터 자체가 분할되어 전송되고 다시 분할된 데이터가 합쳐지는 과정을 담당하고, 에러 제어와 흐름 제어를 담당하는데 Segment를 데이터 단위로 프로토콜은 UDP와 TCP로 구분한다.

Session Layer : 네트워크 통신에서 상호 호스트 간의 연결과 그 연결을 지속시켜 통신을 제어하는 역할을 수행한다.

Presentation Layer : 우리가 사용하고 있는 데이터 형식을 압축과 암호화, 복호화와 같은 변환기능을 수행한다. 두 시스템 사이에서 교환되는 메시지의 syntax, sementic을 일치시킨다. 예를들어, JPEG, AVI....

Application Layer : 최상위 계층으로 사용자의 인터페이스를 담당하는데 우리가 흔히 사용하는 네트워크 관련 프로그램이라고 생각하면 된다. 
````

- Bandwidth vs Latency


````
bandwidth : “단위시간”동안에 얼마나 많은 비트를 전송했는지
latency : receiver에 도달하는데 걸리는 시간

속도랑 지연이랑은 확연히 다른 기능입니다.<br />
네이버에 접속했는데 정말 빠른 속도로 접속 됬다는건 속도 즉, bandwidth가 빠른것이지 Latency는 0이라는 뜻이 아닙니다.<br /> Latency는 절대로 0이 나올 수 없다는 것을 이해하시기 바랍니다. (물리적 전송 시간이 필요합니다)
````

- Datalink vs Network


````
가장 큰 차이점은 Datalink layer는 Physical layer와 직접 연결되어있고, Network layer는 간접 연결되어있다는 점이다.
````

- 네트워크 혼잡이 발생하는 이유


```
너무 많은 패킷들이 라우터에 머물고 있을 때 성능이 나빠진다.<br /> 이러한 상황을 네트워크 혼잡이라고 하는데 혼잡을 제어하기 위해서 가장 효과적인 방법은 네트워크 상에 존재하는 부하를 줄이는 방법이다.<br /> 부하를 줄이려면 라우터들이 다룰 수 없는 패킷들로 인하여 넘치게 되었을 때, 패킷들을 버려서 문제를 해결하면 된다.<br />
전송가능한 용량을 넘어서는 데이터가 라우터에 도착할때 발생한다.<br />
속도의 차이가 나면, 버퍼나 큐를 사용하는데, 버퍼공간을 다 소진해서 packet drop이 발생하게되고, 전송하는 쪽에서는
이를 보완하기 위해서 packet을 retransmit하는 상황이 되어 악순환에 빠지게 됨.
```

- DNS


````
Domain Name Server. 브라우저에서 URL을 통해 접근할 때, 이 URL의 IP 주소를 찾아서 알려준다.(맞나?)
````

- ARQ


```
ARQ는 데이터 전송에서 에러를 제어하기 위해 사용되는 프로토콜이다.<br /> 수신자가 패킷에 에러가 있는 것을 감지하면 자동으로 정보를 전송한 기기에서 패킷을 재전송 할 것을 요청한다.<br /> 이 과정은 패킷에 에러가 없거나 에러가 미리 정해진 정송 횟수 이하로 발생할 때만 계속 된다.
```

- Protocol
- IPv4 vs IPv6
- 메시지 교환 vs 패킷 교환
- 실시간 음성통신에서 UDP의 장점
- 방화벽 vs 프록시



## Data Structure

- 삽입/선택/버블 정렬
- 퀵 정렬 vs 합병 정렬
- Binary Search
- Quick Sort
- Quick Sort를 활용한 k번째 값 찾기
- Graph
- Minimum Spaning Tree(최소 비용 신장 트리)
- 최단 거리 알고리즘
- Btree와 B+tree
- Heap Sort



## Operating System

- 프로세스와 프로그램과 쓰레드
- Busy-Waiting이란?
- 동기화란?
- IPC
- RPC
- Context Switching
- 가상 메모리
- 모놀리식 커널
- Monitor
- Page Table
- Locality(참조 지역성)
- Thrashing
- Interrupt
- Process Status
- 커널모드 vs 유저모드
- 멀티 쓰레드
- System Call



## Data Base

- Transaction
- Index
- SQL Injection
- 1 대 N으로 서버 성능 개선
- Collate를 사용하는 이유




## Line

### 2014년

- Array와 LinkedList에 대해 설명하시오.

  [Array vs Linked List](https://github.com/Yongjai/TIL/blob/master/DataStructure/ArrayAndLinkedList.md/)

- Hash에 대해 설명하시오.

- HTTP에서 POST와 GET의 차이는?

- OS - 멀티스레딩(개념 및 개발 경험 포함)

- www.abc.com 이라는 곳을 어떻게 찾는가?(DNS, Router)

````
귀찮아... 누가 대신 써줘..
````

- TCP/IP가 무엇인가? 각각 설명해보아라

````
데이터 송수신을 담당하는 TCP와 패킹 송수신 동작을 제어하는 IP.<br /> 브라우저나 메일 등 일반적인 애플리케이션이 데이터를 송수신 할 경우 TCP 프로토콜을 사용한다.<br /> IP 내부에는 ICMP와 ARP를 가지고 있는데 ICMP는 패킷 운반 시 발생하는 오류를 알려주거나 제어용 메시지를 알려줄 때 사용하고 ARP는 IP 주소에 대응하는 이더넷의 MAC 주소를 조사할 때 사용한다.
````

- 동적으로 움직이는 시계를 나타내기 위해 서버는 어떤 일을 하게 될까?

````
1초 마다 클라이언트 측에 신호를 보낸다. 특정 시간이 흘렀을 때, 다시 동기화를 시켜서 시간을 맞춰준다.(?)
````

- 데이터베이스란 무엇인가?
- 데이터베이스에 인덱스를 추가하면 무엇이 좋은가?
- 만약 좋다고 하면 왜 모든 칼럼에 인덱스를 추가하지 않는가?
- 비전공자인데 NEXT에서 공부한 것만으로 LINE에서 일할 수 있을 것 같아요?

### 2015년

- 자신의 프로젝트에 대해서 설명해봐라
  - 프로젝트에서 어려웠던 부분은
  - 문제가 무엇이였고 어떻게 해결했는가

- 자주 사용하는 디자인 패턴은?
  - 왜 그 디자인패턴을 쓰는가
  - 그거 말고 다른건 안쓰는가
  - 왜 그 디자인패턴이 좋은가
  - 멀티스레드에서 그 디자인 패턴은 왜 적합한가/하지 않은가

- 인덱싱의 장점과 단점은?
  - 인덱싱을 구현한다고 하면 무슨 자료구조를 쓸건가
  - 왜 그 자료구조를 사용한다고 했는가
  - 그 자료구조의 시간복잡도는?
  - 그 자료구조가 최선인가
  - 그 자료구조보다 좋은 방법은?
  - 좋은 방법이 있으면 왜 좋은가
  - 그 좋은 방법의 시간 복잡도는 뭐가될까?

- 왜 객체지향으로 프로그래밍 하는가
  - 객체지향의 장점/점은?
  - 왜 그런 장점이 있는가
  - 그 단점을 보안할 방법은 없는가?

- TDD로 개발해본적 있는가
- TDD로 자신의 프로젝트를 개발했다면 어떤게 좋았을까
- 멀티스레드 병목현상 등을 해결하려고 무슨 노력을 해봤는가

### 2016년

- 운영체제
  운영체제 스케줄러 종류, 각각의 특징
  멀티 스레딩 프로그래밍 경험이 있나요?(hashtable vs hashmap vs synchronizedMap() vs ConcurrentHashMap)

- 자료구조
  Stack vs Queue의 차이

  [Stack vs Queue](https://github.com/Yongjai/TIL/blob/master/DataStructure/StackAndQueue.md/)

  Stack, Queue를 사용해서 프로그래밍을 한 경험
  Java Collection List, Set, Map 의 특징과 차이점에 대해서
  Set에서 중복을 어떻게 검사하는가?

- 데이터베이스
  데이터베이스를 사용하는 이유 - 데이터의 중복 / 일관성 / 사용성
  데이터베이스 쿼리 작성(inner join, group, where)
  유저가 천만명이나 되었을 때 발생할 수 있는 성능상 이슈에는 어떠한 것이 있을까?
  Statement vs PreparedStatement

- Network
  url을 웹 브라우저에 입력했을 때 내부적으로 일어나는 원리
  TCP 3-way-handshaking 에 대해서 설명하세요.
````
<용어 더해서 좀 더 자세히 작성!>
TCP(Transmission Control Protocol)은 OSI 7 layer의 transport 계층에 속해있는 프로토콜이다.<br /> TCP는 IP(Internet Protocol)와 함께 TCP/IP를 구성하고 있다.<br />
다시 돌아가서, TCP는 기본적으로 3-way-handshaking를 이용해 통신을 하고 있다.<br /> 클라이언트와 서버가 3번에 걸쳐서 통신을 한다는 의미를 이해하면 쉽다.<br /> 
<1단계> 클라이언트에서 서버에 접속을 요청하는 신호인 SYN 패킷을 보낸다. 그리고 클라이언트는 서버의 SYN/ACK 응답을 받기 위한 대기 상태인 SYN_SENT 상태로 전환이 된다.<br /> 
<2단계> 서버측에서 SYN 요청을 받게 되면 요청을 허락한다는 신호인 ACK와 SYN flag가 설정된 패킷을 같이 보내고 클라이언트에서 ACK 응답을 받기 위한 대기 상태인 SYN_RECEIVED 상태로 전환이 된다.<br />
<3단계> 서버로부터 요청을 받게 되면 클라이언트는 다시 한 번 서버에게 알았다는 ACK를 보내게 됩니다. 그리고 서버가 ACK를 받으면 ESTABLISHED 상태가 된다. 이후부터 데이터가 오가게 된다.<br />
이렇게 3번의 절차를 통해 연결하는 과정을 3-way-handshaking이라고 한다.
````
- 프로젝트
  현재 진행 중인 프로젝트 ERD를 그려보세요.
  프로젝트에서 mapper 어떤 걸 사용해보셨나요?
  MVC 구조에 대해서 설명하세요.
- Spring
  DI란 무엇인가?
  AOP란 무엇인가?
  bean의 생명주기를 설명해보세요.
- 튜닝
  Full GC stop the world 때 트러블 슈팅 경험
  GC 뭐 써봤어요?
- 그 외
  블로그에서 불법으로 포스트를 스크랩해가는 것을 어떻게 찾아낼 수 있을까요?
  url을 압축하려고 할 때 어떻게 하면 좋을까요? - Universal hashing
  클라이언트가 좋아요, 백엔드가 좋아요?
  자기 자신의 필요에 의해서 만들어본 소프트웨어가 있나요?
  Native App 개발 경험이 있나요?
  현업에서 접해보지 않은 새로운 분야에서 일할 수도 있는데 어떻게 극복할 수 있나요?
  본인 자기소개 간단하게 해주세요.
  마지막으로 할 말이 있다면?

Guide Line

- 전산 기본 지식
  - 동기와 비동기에 대해서 설명하시오.
  - 쓰레드와 프로세스의 차이에 대하여 설명하시오.
  - Database에서 index의 장점과 단점
  - TCP/UDP의 차이
  - 객체지향 개발 원칙 을 나열하고 설명 해 주세요.
  - 자신의 친구리스트를 메모리에 올린다고 했을때 어떠한 자료 구조를 선택 하곘습니까? 사용패턴을 고려해서 선택의 이유를 설명해주세요.
  - blocking io 와 non-blocking io 에 대해서 설명하시오.
  - context-switching 에 대해서 설명하시오
  - cpu 의 캐슁 라인에 대하여 설명하시오
  - ssd 는 왜 빠른지 설명하시오.
  - 파일 시스템의 내부 구조에 대하여 설명하시오. ( ext3, ntfs 등 )
  - linux 시스템에서 함수를 호출할때에 유저스페이스와 커널 스페이스에 어떠한 일이벌어지는지 설명하시오 ( 파일을 쓴다, 파일을 연다, tcp 연결, udp 전송등 )
  - cpu 가 2개 이상일때 ( smp ) 2개이상의 cpu 에서 돌아가는 시스템을 설계한다면 lock 을 효율적으로 구현하는 방법에 대하여 설명하시오. ( 하드웨어, 소프트웨어 무관, 다른 아키텍쳐도 좋음 )
  - process와 thread의 차이, multi-process와 multi-thread의 차이는?
  - OAuth의 개념 설명
  - 대칭키, 비대칭키, 메시지 다이제스트 개념, 어떤 용도인지 설명
    * 메모리 및 성능 최적화를 위해서 사용한 툴이 있다면 설명해주세요
- 코딩 기본 지식
  - Quicksort가 최악의 성능을 보이는 조건
  - Java로 Singleton Class를 만들 수 있나요?
  - 자료구조 Set과 Map의 차이점
  - mutable, immutable 객체 차이
  - 환경별로 다른 동작이 필요한 경우 코드레벨의 분기 처리는 어떻게 구현 할 수 있을까요?
  - 프로그램 질문 Persion객체의 List가 있을때 age가 10이상 코드를 작성 해주세요
  - 2^64을 계산하는 코드를 작성 해주세요
  - JAVA , C++ 차이점을 설명 해주세요
  - C++에서 자동화된 메모리 관리를 할 수 있는 기법
  - String 관련된 클래스 StringBuilder, StringBuffer의 각각의 차이점을 설명해주세요
  - hashmap 과 hashTable차이는?
  - synchronized 를 사용하는 이유는?
  - synchronized의 사용하면 성능이 떨어지는 부작용이 있는데 이유를 설명하고 , 이것을 좀 더 최적화 할 수 있는 방법은?r
  - Reflection 에 대하여 설명하고 어떻게 구현가능한지 설명하시오. 
  - Java의 Map 구현체 종류. 차이는 뭔가?
  - gc에 대해서 간단한 설명
- DB
  - 메시지 100개를 빨리 저장하려면 어떻게 해야 하나요
  - 메시지를 시간순으로 정렬해서 빨리 가져오려면 어떻게 해야하나요
  - Query에서 hint란 무엇인가요
  - Transaction의 장단점에 대해서 설명해주세요
  - Indexing에 대해서 설명해주세요
- Network
  - 이미지 다운로드할 때 HTTP Protocol은 어떻게 되는가
  - 하나의 Connection으로 여러 번의 요청을 하려면 어떤 옵션을 사용하는가
  - 이미지 다운로드를 중단이 되어 다시 다운로드할 때 최소한의 비용으로 가져오려면 어떻게 해야하는가
  - 이미지 업로드할 때 HTTP Protocol은 어떻게 되는가
- Logic
  - ImageView에 디폴트 이미지를 보여주다가 다운로드된 이미지를 보여주는 디자인패턴은 무엇인가
  - SingleTon Pattern이란
    - SingleTon Object를 멀티쓰레드에서 1개만 생성하려면 어떻게 해야 하는가
    - 이 때 synchronize를 최소한 하려면 어떻게 해야하는가
    - volatile 이란 무언인가



## Naver

## CouPang

## kakao

- TCP에서 한 쪽이 죽었는지 어떻게 알 수 있는가? (카카오 면접)

```
세 가지 관점에서 말할 수 있을 것 같습니다. 통신을 준비하는 과정, 통신하는 과정, 통신이 끝나는 과정.<br />
TCP(Transmission Control Protocol)은 OSI 7 layer의 transport 계층에 속해있는 프로토콜이다.<br /> TCP는 IP(Internet Protocol)와 함께 TCP/IP를 구성하고 있다.<br />
<br />
통신을 준비하는 과정의 관점에서 TCP는 기본적으로 3-way-handshaking을 이용해 통신을 준비합니다.<br /> 클라이언트와 서버가 3번에 걸쳐서 통신을 한다는 의미를 이해하면 쉽습니다.<br />
<1단계> 클라이언트 측에서 서버 측에 접속을 요청하는 신호를 보냅니다. 그리고 클라이언트는 서버의 응답을 받기 위한 대기 상태로 전환이 됩니다.<br /> 
<2단계> 서버 측에서 요청을 받게 되면 요청을 허락한다는 신호와 함께 다시 클라이언트 측의 응답을 받기 위해 대기 상태로 전환이 됩니다.<br />
<3단계> 서버 측으로부터 요청을 받게 되면 클라이언트 측은 다시 한 번 서버에게 알았다는 신호를 보내게 됩니다. 이러한 핸드쉐이크를 통해 한 쪽이 죽었는지 살았는지 알 수 있습니다.<br />
<br />
통신을 하는 관점에서 바라보면 데이터를 보낼 때, 사실 한 번에 보내는 게 아니라 쪼개서 패킷을 이용해서 보내게 됩니다.<br /> 패킷 하나 하나는 라우터를 통해 목적지로 도달하게 됩니다. 그리고 서버 측에서는 데이터가 담긴 각각의 패킷을 받게 되면 데이터를 받았다고 답을 해줍니다.<br /> 그러나 일정 시간이 지나도 데이터를 받았다는 응답을 클라이언트 측에서 받지 못한다면 송신측은 다시 한 번 패킷을 보내줍니다.<br /> 이런 과정이 여러 번 발생하면 클라이언트 측은 서버에게 문제가 있다는 것을 깨닫고 연결을 끊어버리게 됩니다.<br />
<br />
통신을 끝내는 과정에서 바라보면 TCP는 4-way-handshaking을 이용해 통신을 끊습니다.<br />
<1단계> 클라이언트 측이 연결을 종료하겠다는 메시지인 FIN 플래그를 서버 측에 보냅니다.<br />
<2단계> 서버 측은 이를 확인하고 알았다는 메시지를 클라이언트 측에 보냅니다. 이 때, 서버는 통신을 끝내기 위해 TIME-WAIT이라는 상태가 됩니다.<br />
<3단계> 서버가 통신을 종료할 시점이 오면 서버는 클라이언트에 FIN 플래그를 보냅니다.<br />
<4단계> 클라이언트를 이를 받아 확인했다는 메시지를 마지막으로 보내게 됩니다.
```

## Timon
