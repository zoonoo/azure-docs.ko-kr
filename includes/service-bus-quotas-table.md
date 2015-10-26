다음 표에는 서비스 버스 메시징에 특정한 할당량 정보가 나와 있습니다. 이 표에는 이벤트 허브 제한이 포함되지만 이벤트 허브에 대한 자세한 내용은 [이벤트 허브 가격 책정](http://azure.microsoft.com/pricing/details/event-hubs/)을 참조하세요. 서비스 버스에 대한 가격 책정 및 기타 할당량에 대한 정보는 [서비스 버스 가격 책정](http://azure.microsoft.com/pricing/details/service-bus/) 개요를 참조하세요.

|할당량 이름|범위|유형|초과 시 동작|값|
|---|---|---|---|---|
| Azure 구독당 최대 네임스페이스 수|네임스페이스|정적|추가 네임스페이스에 대한 후속 요청은 Azure 관리 포털에서 거부됩니다.|100|
|큐/토픽 크기|엔터티|큐/토픽을 만들 때 정의됨|들어오는 메시지가 거부되며 호출 코드에서 예외를 수신합니다.|1, 2, 3, 4 또는 5GB입니다.<br /><br />[분할](service-bus-partitioning.md)이 활성화된 경우 최대 큐/항목 크기는 80GB입니다.|
|네임스페이스에 대한 동시 연결 수|네임스페이스|정적|추가 연결에 대한 후속 요청이 거부되며 호출 코드에서 예외를 수신합니다. REST 작업은 동시 TCP 연결 계산에 포함되지 않습니다.|NetMessaging: 1,000<br /><br />AMQP: 5,000|
|큐/토픽/구독 엔터티의 동시 연결 수|엔터티|정적|추가 연결에 대한 후속 요청이 거부되며 호출 코드에서 예외를 수신합니다. REST 작업은 동시 TCP 연결 계산에 포함되지 않습니다.|네임스페이스당 동시 연결 수 제한에 의해 제한됩니다.|
|큐/토픽/구독 엔터티의 동시 수신 요청 수|엔터티|정적|후속 수신 요청이 거부되며 호출 코드에서 예외를 수신합니다. 이 할당량은 항목의 모든 구독 전반에 걸쳐 종합된 동시 수신 명령 수에 적용됩니다.|5, 000|
|릴레이의 동시 수신기 수|엔터티|정적|추가 연결에 대한 후속 요청이 거부되며 호출 코드에서 예외를 수신합니다.|25|
|동시 릴레이 수신기 수|시스템 수준|정적|추가 연결에 대한 후속 요청이 거부되며 호출 코드에서 예외를 수신합니다.|2, 000|
|서비스 네임스페이스의 모든 릴레이 끝점당 동시 릴레이 연결 수|시스템 수준|정적|-|5,000|
|서비스 네임스페이스당 릴레이 끝점 수|시스템 수준|정적|-|10,000|
|서비스 네임스페이스당 토픽/큐 수|시스템 수준|정적|서비스 네임스페이스에서 새 토픽 또는 큐 만들기를 위한 후속 요청이 거부됩니다. 따라서 관리 포털을 통해 구성된 경우 오류 메시지가 생성됩니다. 관리 API에서 호출된 경우 호출 코드에서 예외를 수신합니다.|10,000<br /><br />서비스 네임스페이스에서 토픽 및 큐의 총 수는 10,000개 이하여야 합니다.|
|서비스 네임스페이스당 분할된 토픽/큐 수|시스템 수준|정적|서비스 네임스페이스에서 새 분할된 토픽 또는 큐 만들기를 위한 후속 요청이 거부됩니다. 따라서 관리 포털을 통해 구성된 경우 오류 메시지가 생성됩니다. 관리 API에서 호출된 경우 호출 코드에서 **QuotaExceededException** 예외를 수신합니다.|100<br /><br />분할된 각 큐 또는 토픽은 네임스페이스당 10,000개 엔터티 할당량에 합산됩니다.|
|메시징 엔터티 이름의 최대 크기: 네임스페이스, 큐, 토픽, 구독 또는 이벤트 허브|엔터티|정적|-|50자|
|이벤트 허브 이벤트의 최대 크기|시스템 수준|정적|-|256KB|
|큐/토픽/구독 엔터티의 메시지 크기|시스템 수준|정적|이러한 할당량을 초과하는 들어오는 메시지는 거부되며 호출 코드에서 예외를 수신합니다.|최대 메시지 크기: 256KB <br /><br />**참고** 시스템 오버헤드로 인해 이 제한은 일반적으로 256KB보다 약간 작습니다.<br /><br />최대 헤더 크기: 64KB<br /><br />속성 모음의 최대 헤더 속성 수: **MaxValue**<br /><br />속성 모음의 최대 속성 크기: 명시적 제한은 없습니다. 최대 헤더 크기로 제한됩니다.|
|[NetOnewayRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.netonewayrelaybinding.aspx) 및 [NetEventRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.neteventrelaybinding.aspx) 릴레이의 메시지 크기|시스템 수준|정적|이러한 할당량을 초과하는 들어오는 메시지는 거부되며 호출 코드에서 예외를 수신합니다.|64KB
|[HttpRelayTransportBindingElement](https://msdn.microsoft.com/library/microsoft.servicebus.httprelaytransportbindingelement.aspx) 및 [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) 릴레이의 메시지 크기|시스템 수준|정적|-|제한 없음|
|큐/토픽/구독 엔터티의 메시지 속성 크기|시스템 수준|정적|A **SerializationException** 예외가 생성됩니다.|각 속성의 최대 메시지 속성 크기는 32K입니다. 모든 속성의 누적 크기는 64K를 초과할 수 없습니다. 이 제한은 사용자 속성과 시스템 속성([SequenceNumber](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.sequencenumber.aspx), [Label](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.label.aspx), [MessageId](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) 등)을 모두 포함하는 [BrokeredMessage](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.aspx)의 전체 헤더에 적용됩니다.|
|토픽당 구독 수|시스템 수준|정적|항목에서 추가 구독을 만들기 위한 후속 요청은 거부됩니다. 따라서 관리 포털을 통해 구성된 경우 오류 메시지가 표시됩니다. 관리 API에서 호출된 경우 호출 코드에서 예외를 수신합니다.|2,000|
|토픽당 SQL 필터 수|시스템 수준|정적|토픽에서 추가 필터를 만들기 위한 후속 요청이 거부되며 호출 코드에서 예외를 수신합니다.|2,000|
|토픽당 상관 관계 필터 수|시스템 수준|정적|토픽에서 추가 필터를 만들기 위한 후속 요청이 거부되며 호출 코드에서 예외를 수신합니다.|100,000|
|SQL 필터/작업 크기|시스템 수준|정적|추가 필터를 만들기 위한 후속 요청이 거부되며 호출 코드에서 예외를 수신합니다.|필터 조건 문자열의 최대 길이: 1024(1K)<br /><br />규칙 동작 문자열의 최대 길이: 1024(1K)<br /><br />규칙 동작당 최대 식 수: 32|

<!---HONumber=Oct15_HO3-->