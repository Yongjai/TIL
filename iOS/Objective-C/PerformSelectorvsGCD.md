Multi-thread Perform Seletor

이번 주제가 멀티 스레드에 대한 얘기이므로 왜 멀티 스레드를 써야하는지에 대해 먼저 얘기를 해야 할 것 같습니다.

하나의 앱은 하나의 프로세스로 이루어져 있고 그 프로세스 안에는 최소 한 개의 스레드가 동작하고 있습니다. 앱을 실행시키면 메인 스레드가 가장 먼저 동작합니다. 메인 스레드는 이벤트 루프를 돌면서 이벤트가 있을 때마다 함수를 호출합니다. 일반적으로 앱은 메인스레드를 중심으로 실행이 되기 때문에 이 메인 스레드를 멈추면 ‘절대’ 안 됩니다. 그리고 사용자의 모든 인터렉션은 메인 스레드에서 실행이 되도록 해야 합니다. 

예를들어 서버에서 데이터를 받아올 때, 메인 스레드를 사용하게 앱이 멈출 수 있습니다. 이유는 메인 스레드를 사용하게 되면 메인 스레드에서 발생하는 작업이 끝나기 전까지 다음 작업으로 넘어가지 않기 때문에 서버에서 데이터를 받아오는데 시간이 걸리게 되면 걸리는 시간만큼 앱이 멈출 수 밖에 없습니다. 이러한 문제를 해결하기 위해 다른 스레드를 사용해서 서버에서 데이터를 받아오게 해야 합니다. 즉, 메인 스레드에서는 사용자의 인터렉션과 관련된 화면 처리나 기본적인 작동을 처리하게 하고, 시간이 오래 걸리는 작업은 다른 스레드를 이용해서 작업해야 합니다. 

멀티 스레드를 사용하려면 우선 메인 스레드가 아닌 새로운 스레드 객체를 선언해주어야 한다. 

    NSThread *testThread;
    testThread = [[NSThread alloc] initWithTarget:self selector:@selector(test) object:nil];
    [testThread start];

위와 같은 코드로 스레드를 만들고 실행시킬 수 있다. 그러면 만약 어떤 스레드에서 다른 스레드의 함수를 호출하려면 어떻게 해야 할까? 그 때 필요한 것이 바로 이번 주제인 -performSelector: 메소드 입니다.

-performSelector: 메소드는 NSObject 클래스에 구현이 되어 있으며 이 메소드는 Thread 선택과 지연 메시지 전달이 가능하게 합니다. 지연 메시지가 어떤 방식으로 전달이 되는지 자세히 알아보자.

Run Loop. 한국말로 실행 루프라고 불리는 녀석이 있습니다. NSRunLoop 클래스에 속하면서 운영체제가 자고 있는 스레드를 깨우고 이벤트가 들어오면 이 이벤트를 관리하는 게 바로 실행 루프 입니다. 실행 루프는 작업을 예약하고 일정 시간 동안 들어온 이벤트를 처리합니다. iOS에서 각각의 스레드들은 실행 루프를 최대 한 개 가질 수 있으며 메인 스레드에 대한 실행 루프는 자동으로 실행됩니다. 

 iOS 앱을 실행하면 무조건 하나 이상의 스레드가 동작하는데 무조건에 해당하는 것이 메인 스레드입니다. 사용자의 모든 인터렉션은 메인 스레드에서 실행 되며, 프로그램의 동작에 따라 스레드를 추가할 수 있는데 실행 루프는 자신의 이벤트 큐에 있는 이벤트를 불러와 처리하는 작업을 계속 반복합니다. 만약 큐에 이벤트가 없어지면 해당 스레드는 일시적으로 멈추게 됩니다. 

이제 performSelector:에 해당하는 다양한 메소드에 대해 알아보겠습니다.

- performSelecotor :withObject: afterDelay:

    해당 메소드는 메소드를 통한 지연 메시지 전달은 메소드 호출 이벤트를 현재 실행 중인 실행 루프의 이벤트 큐에 쌓음으로써 구현 됩니다. 지연 시간에 대한 afterDelay:는 메시지 전송 시점을 보장하는 것이 아니라 일정 시간 내에 메시지가 전송되지 않음을 보장한다.

- performSelectorOnMainThread:

    메인 스레드가 아닌 다른 작업 스레드에서 -performSelectorOnMainThread:를 이용하면 메인 스레드의 이벤트 큐에 메소드 호출 이벤트를 쌓을 수 있습니다. 타이머와 연동되는 사용자 인터페이스 관련 이벤트 등을 처리할 때 유용합니다.
    -performSelector:onThread: 메소드를 이용하면 특정 스레드의 이벤트 큐에 메소드 호출 이벤트를 쌓을 수 있습니다.

- performSelector:onThread: & -performSelectorOnMainThread:

    위의 두 메소드들은 작업을 실행할 스레드와 -performSelector: 메소드를 호출하는 현재 스레드가 동일하다면 메시지를 이벤트 큐에 쌓지 않고 수신자에게 즉시 전송합니다.

- performSelector:

    -performSelector: 메소드의 여러 인자 중 wait 인자를 이용하면 동기/비동기 구현도 가능합니다. wait 인자를 NO 지정하면 비동기 전송, YES 지정하면 동기 전송이 됩니다. 또한  mode 인자를 통해 실행 루프의 모드를 설정할 수 있습니다. 실행 루프는 각자 특정 모드로 실행되는데, mode는 일부 이벤트를 필터링해 무시하고 처리하지 않는 용도로 사용됩니다.

- performSelectorInBackground:

    -performSelectorInBackground: 메소드는 작업용 스레드를 새로 생성한 후 생성된 스레드에서 지정된 메시지를 처리하게 합니다. 이 메소드는 지정된 메시지 하나를 처리하는 목적으로 스레드를 생성하므로 이벤트 큐와 실행 루프를 사용하지 않습니다. 메시지를 처리하고 나면 새로 생긴 스레드는 제거됩니다.

이렇게 -performSelector : 메소드에 대해 알아보았는데 이 메소드들은 극명한 단점이 존재합니다.

- 메모리 관리가 어렵습니다.
  - 컴파일 시점에 컴파일러가 어떤 메소드를 호출해야 하는 지 알 수 없기 때문에 ARC를 사용할 경우 메모리 누수가 발생 할 수 있습니다.
- 파라미터의 개수가 한정적입니다.
  - 전달 가능한 파라미터의 개수가 매우 한정적입니다.(최대 2개)
- 반환 타입이 정해져 있습니다.
  - 반환 타입이 id(객체 포인터)이기 때문에 만약 정수형 포인터 같은 경우 (id)&tempInteger와 같이 타입 캐스팅을 해서 사용해야 합니다.

위와 같은 단점들 때문에 Effective Objective-C 2.0의 내용을 보면 -performSelector 메소드는 사용하지 말고 GCD 사용을 추천하고 있습니다. 그냥 안 쓰면 될 것 같습니다 :) 그래서 다음으로 알아볼 것이 GCD라는 것입니다.



GCD(Event Source)

이번에 알아볼 것은 Grand Central Dispatch, 줄여서 GCD라고 불리는 녀석입니다. GCD는 애플에서 제공하는 멀티 스레딩을 위한 새로운 방법 입니다. 블록과 함께 사용되기 때문에 함수 내에서 함수를 선언할 수 있어 내부에 선언된 함수가 외부 함수의 파라미터를 참조할 수 있도록 하는 장점이 있습니다. 때문에 전달되는 곳이 객체일 경우 전혀 다른 context에서도 코드가 실행될 수 있고 이로 인해 자신의 정의된 범위 내에서 어떤 것이든 실행할 수 있는데 여기서 GCD라는 기술이 발생했습니다.

스레딩을 dispatch queue로 추상화를 해서 제공을 하는데 블록은 이 큐에 삽입될 수 있으며 GCD가 모든 스케쥴을 관리하게 됩니다. GCD는 각각의 큐를 처리하기 위해 백그라운드 스레드를 시스템 리소스 상용량에 기반을 두고 새로 생성, 재사용, 제거 합니다. 게다가 GCD는 스레드 안전한 단일 코드 실행과 시스템 리소스 사용량에 기반을 둔 병령 처리 같은 작업을 처리하는데 쉬운 솔루션을 제공해 줍니다. 

// 오늘 주제는 GCD의 Event Source이기 때문에 자세한 GCD의 내용은 다음에 다시 정리 하겠습니다.

Event Source를 말하기 전에 Dispatch Source라는 것에 알고 넘어가야 합니다. 

Dispatch Source는 이벤트가 발생하면 자동으로 dispatch queue에 있는 특정 block을 실행시켜주는 객체입니다. GCD는 다양한 dispatch source를 지원합니다. 

- Timer dispatch source : 주기적으로 알람을 사용자에게 알립니다.
- Signal dispatch source : Signal이 도착하면 사용자에게 알립니다. 프로세스와 관련된 것이라고 생각하면 됩니다.
- Descriptor source : 다양한 파일 기반 및 소켓 기반 작업에 대해 알려줍니다.
  - 데이터를 읽을 수 있는 경우
  - 데이터를 쓸 수 있는 경우
  - 파일 시스템에서 파일을 삭제, 이동 또는 이름을 바꾼 경우
  - 파일 메타 정보가 변경된 경우
- Process dispatch source : 프로세스 관련 이벤트를 알려줍니다.
  - 프로세스가 종료 될 경우
  - 프로세스가 fork 또는 exec 호출을 할 경우
  - 신호가 프로세스에 전달 될 경우
- Mach port dispatch source : Mach 관련 이벤트를 알려줍니다. 하드웨어와 관련된 이벤트라고 생각하면 됩니다.
- Custome dispatch source : 사용자가 정의하고 발생시키는 이벤트 입니다.

디스패치 소스는 일반적으로 시스템 관련 이벤트를 처리하는 데 사용되는 비동기 콜백 함수를 대체합니다. 디스패치 소스를 구성 할 때 관찰 할 이벤트와 디스패치 대기열 및 해당 이벤트를 처리하는 데 사용할 코드를 지정합니다. 블록 또는 함수를 사용하여 코드를 지정할 수 있습니다 . 관심있는 이벤트가 도착하면 디스패치 소스는 블록 또는 함수를 지정된 디스패치 대기열에 제출하여 실행합니다.

디스패치 소스는 대기열에 수동으로 제출하는 작업과 달리 응용 프로그램에 대한 지속적인 이벤트 소스를 제공합니다. 디스패치 원본은 명시 적으로 취소 할 때까지 해당 디스패치 대기열에 계속 남아 있습니다. 부착 된 상태에서 해당 이벤트가 발생할 때마다 연관된 작업 코드를 디스패치 대기열에 전송합니다. 타이머 이벤트와 같은 일부 이벤트는 정기적 인 간격으로 발생하지만 대부분 특정 조건이 발생할 때만 산발적으로 발생합니다. 이러한 이유로 디스패치 소스는 관련 디스패치 대기열을 보유하여 이벤트가 아직 보류 중일 때 조기에 해제되지 않도록합니다.

디스패치 대기열에서 이벤트가 백 로그되는 것을 방지하기 위해 디스패치 소스는 이벤트 통합 스키마를 구현합니다. 이전 이벤트에 대한 이벤트 핸들러가 대기열에서 제거되고 실행되기 전에 새 이벤트가 도착하면 디스패치 소스는 새 이벤트 데이터의 데이터를 이전 이벤트의 데이터와 통합합니다. 이벤트 유형에 따라 통합은 이전 이벤트를 대체하거나 보유한 정보를 업데이트 할 수 있습니다. 예를 들어, 신호 기반의 디스패치 소스는 가장 최근의 신호에 대한 정보 만 제공하지만 마지막으로 이벤트 처리기를 호출 한 이후 전달 된 총 신호 수를 보고 합니다.

위에 다양한 종류가 존재하지만 Custom dispatch source를 예를 들어 설명을 해보겠습니다. 

progress bar를 업데이트하는 작업을 한다고 가정해보겠습니다.

가장 먼저 dispatch_source_create를 이용해서 source 객체를 생성해줘야 합니다. 그리고 각각의 작업들이 완료될 때마다 호출할 event handler(block/function)을 dispatch_source_set_event_handler을 이용해 설정해줍니다. 완료 이벤트를 받게 되면 source 객체는 등록된 핸들러를 실행해서 진행바를 업데이트 하게 됩니다. 진행바가 업데이트 되는 것은 화면 처리와 관련된 일이기 때문에 main queue에서 처리해야 합니다. 따라서 source를 생성할 때 dispatch_get_main_queue를 호출해야 합니다. progress bar를 업데이트 할 때 필요한 값들은 global queue에서 가져오게 됩니다. 하나의 작업이 끝날 때마다  dispatch_source_merge_data를 호출하고 1이라는 값을 전달합니다.  dispatch_source_merge_data 호출을  하면 source객체의 핸들러가 호출 됩니다. 따라서 핸들러에서 dispatch_source_get_data 함수를 이용해서 값을 가져올 수 있습니다. 그리고 호출이 완료되면 값이 0으로 reset 됩니다.



    dispatch_source_t source =  dispatch_source_create ( DISPATCH_SOURCE_TYPE_DATA_ADD, 0, 0, dispatch_get_main_queue());
      
    dispatch_source_set_event_handler(source, ^{
             [progressIndicator incrementBy: dispatch_source_get_data(source)];
         });      
    //dispatch source는 항상 suspended 상태로 시작되기때문에
    //생성후 이벤트 처리를 윈하면 resume시켜야한다.
    dispatch_resume(source);
        
    dispatch_apply([array count], globalQueue, ^(size_t index) {         
             // 이 부분에서 인덱스로 구한 데이터에 대한 처리 수행
             // .....
          
             //하나의 작업 완료후 이벤트 발생.
             dispatch_source_merge_data(source, 1);
         } );

dispatch_source_merge_data를 호출 하면 source객체의 event handler가 호출됩니다. 만약 여러 번의 호출이 발생하면 event handler도 여러 번 발생할 것입니다. 그런데 다음 상황을 보겠습니다.

업데이트 작업은 메인 큐에서 수행되는데 만약 메인큐를 처리하는 메인 쓰레드가 다른 작업등으로 인해 바쁜 경우, 메인큐의 작업은 지연됩니다. 이 때, DISPATCH_SOURCE_TYPE_DATA_ADD 타입의 source 객체인 경우, 처리되지 못하고 대기 중인 여러 이벤트들은 하나로 통합됩니다. 그리고 핸들러에서 사용될 값(dispatch_source_merge_data 호출시 넘기는 unsigned long 값)은 dispatch_source_merge_data 호출 할때 마다 계속 더해집니다. 이후 메인 쓰레드가 처리가능시점이 되면 event handler가 한 번 호출되면서 그동안 누적된 값이 전달됩니다. dispatch_source_get_data 호출을 하면 값은 다시 0으로 초기화 됩니다. 즉, 이벤트가 바로 바로 처리 안되는 경우, 이벤트를 계속 누적시키지 않고 처리 가능 시점에 한 번만 처리할수 있게 이벤트를 통합하고 데이터값은 계속 누적시켜 주는 겁니다.

하지만 메인 쓰레드가 여유가 있어서 메인 큐에서 바로 바로 가져와 처리 가능하다면, 이벤트 핸들러는 통합 처리 없이 그때 그때마다 수행됩니다. 이벤트 통합을 원하지 않는다면 dispatch source를 사용할 필요는 없습니다. dispatch_async 호출을 하면 됩니다. dispatch_async 대신 dispatch source를 사용하는 오직 하나의 이유라면 바로 이벤트 통합 처리 때문입니다.





참고 자료

https://soulpark.wordpress.com/2012/08/28/objective_c_sending_messages/

http://linkedlist.tistory.com/category/%EC%8A%A4%EB%A7%88%ED%8A%B8%ED%8F%B0/iPhone

https://developer.apple.com/library/content/documentation/General/Conceptual/ConcurrencyProgrammingGuide/GCDWorkQueues/GCDWorkQueues.html

http://jeremyko.blogspot.kr/2011/03/


