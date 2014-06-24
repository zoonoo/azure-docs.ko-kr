<h2><a name="what-are-service-bus-topics"></a>서비스 버스 토픽 및 구독 정의</h2>


서비스 버스 토픽 및 구독은 **게시/구독 메시징 통신** 모델을 지원합니다. 토픽 및 구독을 사용하는 경우 분산 응용 프로그램의
구성 요소가 서로 직접 통신하지 않고 중간자 역할을 하는 토픽을 통해 메시지를 교환합니다.

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

각 메시지가 단일 소비자에 의해 처리되는 서비스 버스 큐와 반대로, 토픽과 구독은 게시/구독 패턴을 사용하여 **일 대 다**
형태의 통신을 제공합니다. 하나의 토픽에 여러 구독을 등록할 수 있습니다. 토픽에 메시지를 전송하면 각 구독에서 독립적으로 처리할
수 있습니다.

토픽 구독은 토픽에 전송된 메시지의 복사본을 받는 가상 큐와 유사합니다. 선택적으로 구독별 토픽에 대한 필터 규칙을 등록할 수
있으며, 이렇게 하면 각 토픽 구독에서 받는 토픽 메시지를 필터링/제한할 수 있습니다.

서비스 버스 토픽 및 구독을 사용하면 다수의 사용자와 응용 프로그램에 대해 다수의 메시지를 처리하도록 확장할 수 있습니다.

<h2><a name="create-a-service-namespace"></a>서비스 네임스페이스 만들기</h2>


Azure에서 서비스 버스 토픽 및 구독 사용을 시작하려면 먼저 서비스 네임스페이스를 만들어야 합니다. 서비스 네임스페이스는 응용
프로그램 내에서 서비스 버스 리소스의 주소를 지정하기 위한 범위 컨테이너를 제공합니다.

서비스 네임스페이스를 만들려면

1.  [Azure 관리 포털][1]에 로그온합니다.

2.  관리 포털의 왼쪽 탐색 창에서 **Service Bus**를 클릭합니다.

3.  관리 포털의 아래쪽 창에서 **Create**를 클릭합니다.   
     ![](./media/howto-service-bus-topics/sb-queues-13.png)

4.  **Add a new namespace** 대화 상자에서 네임스페이스 이름을 입력합니다. 시스템에서 사용 가능한 이름인지
    즉시 확인합니다.   
     ![](./media/howto-service-bus-topics/sb-queues-04.png)

5.  네임스페이스 이름이 사용 가능한지 확인한 후 해당 네임스페이스를 호스트할 국가 또는 지역을 선택합니다(계산 리소스를 배포할
    국가/지역과 같아야 함).
    
    중요: 응용 프로그램을 배포하도록 선택할 지역과 **같은 지역**을 선택합니다. 그러면 최상의 성능을 얻을 수 있습니다.

6.  확인 표시를 클릭합니다. 이제 시스템이 서비스 네임스페이스를 만들고 사용하도록 설정합니다. 시스템이 계정에 대한 리소스를
    프로비전하는 동안 몇 분 정도 기다려야 할 수도 있습니다.
    
    ![](./media/howto-service-bus-topics/getting-started-multi-tier-27.png)

<h2><a name="obtain-default-credentials"></a>네임스페이스에 대한 기본 관리 자격 증명 얻기</h2>


새 네임스페이스에 대해 토픽 또는 구독 만들기 등의 관리 작업을 수행하려면 네임스페이스에 대한 관리 자격 증명을 받아야 합니다.
이 자격 증명은 관리 포털이나 Visual Studio 서버 탐색기에서 가져올 수 있습니다.
### 포털에서 관리 자격 증명을 가져오려면

1.  왼쪽 탐색 창에서 **Service Bus** 노드를 클릭하여 사용 가능한 네임스페이스 목록을 표시합니다.   
     ![](./media/howto-service-bus-topics/sb-queues-13.png)

2.  표시된 목록에서 방금 만든 네임스페이스를 선택합니다.   
     ![](./media/howto-service-bus-topics/sb-queues-09.png)

3.  **Connection Information**을 클릭합니다.   
     ![](./media/howto-service-bus-topics/sb-queues-06.png)

4.  **Access connection information** 대화 상자에서 **Default Issuer** 및
    **Default Key** 항목을 찾습니다. 아래에서 이 정보를 사용하여 네임스페이스 관련 작업을 수행할 것이므로 이
    값을 기록해 둡니다.
### 서버 탐색기에서 관리 자격 증명을 가져오려면

관리 포털 대신 Visual Studio를 사용하여 연결 정보를 가져오려면 [여기][2] 나오는 **Visual Studio에서
Azure에 연결하려면** 섹션에서 설명하는 절차를 따르십시오. Azure에 로그인하면 서버 탐색기의 **Microsoft
Azure** 트리 아래에 있는 **서비스 버스** 노드에 이미 만들어진 네임스페이스가 자동으로 채워집니다. 네임스페이스를 마우스
오른쪽 단추로 클릭한 후 **속성**을 클릭하여 연결 문자열 및 Visual Studio **속성** 창에 이 네임스페이스와
관련하여 표시되는 다른 메타데이터를 표시합니다.

**SharedAccessKey** 값을 적어 두거나 클립보드에 복사해 둡니다.

![](./media/howto-service-bus-topics/VSProperties.png)



[1]: http://manage.windowsazure.com
[2]: http://http://msdn.microsoft.com/en-us/library/windowsazure/ff687127.aspx
