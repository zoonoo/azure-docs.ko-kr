## 이벤트 허브 만들기

1. [Azure 클래식 포털][]에 로그온한 다음 화면 맨 아래에서 **새로 만들기**를 클릭합니다.

2. **앱 서비스**, **서비스 버스**, **이벤트 허브**, **빨리 만들기**를 차례로 클릭합니다.

	![][1]

3. 이벤트 허브의 이름을 입력하고 원하는 지역을 선택한 후 **새 이벤트 허브 만들기**를 클릭합니다.

	![][2]

4. 지정된 지역에서 기존 네임스페이스를 명시적으로 선택하지 않은 경우 포털은 네임스페이스를 만들어줍니다(일반적으로 ***event hub name*-ns**). 해당 네임스페이스(이 예제에서는 **eventhub-ns**)를 클릭합니다.

	![][3]

5. 페이지 맨 아래에 있는 **연결 정보**를 클릭합니다. 복사 단추(다음 그림에 표시)를 클릭하여 클립보드에 대한 **RootManageSharedAccessKey** 연결 문자열을 복사합니다. 이 자습서 뒷부분에서 사용할 연결 문자열을 저장합니다.

	![][4]

이제 이벤트 허브가 만들어졌고 이벤트를 보내고 받기 위한 연결 문자열이 있습니다.

[1]: ./media/event-hubs-create-event-hub/create-event-hub1.png
[2]: ./media/event-hubs-create-event-hub/create-event-hub2.png
[3]: ./media/event-hubs-create-event-hub/create-event-hub3.png
[4]: ./media/event-hubs-create-event-hub/create-conn-str1.png