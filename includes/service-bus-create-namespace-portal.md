1. Azure 클래식 포털에 로그온합니다.

2. 포털의 왼쪽 탐색 창에서 **서비스 버스**를 클릭합니다.

3. 포털의 아래쪽 창에서 **만들기**를 클릭합니다.

    ![만들기 선택][select-create]
   
4. **Add a new namespace** 대화 상자에서 네임스페이스 이름을 입력합니다. 시스템에서 사용 가능한 이름인지 즉시 확인합니다.

    ![네임스페이스 이름][namespace-name]
  
5. 네임스페이스 이름이 사용 가능한지 확인한 후 해당 네임스페이스를 호스트할 국가 또는 지역을 선택합니다.

6. 대화 상자의 다른 필드는 기본값으로 그대로 두고(**메시징** 및 **표준 계층**) 확인 표시를 클릭합니다. 이제 시스템이 네임스페이스를 만들고 사용하도록 설정합니다. 시스템이 계정에 대한 리소스를 프로비전하는 동안 몇 분 정도 기다려야 할 수도 있습니다.
 
    ![만들기 완료][created-successfully]

###자격 증명 얻기
1. 왼쪽 탐색 창에서 **Service Bus** 노드를 클릭하여 사용 가능한 네임스페이스 목록을 표시합니다.
 
    ![서비스 버스 선택][select-service-bus]
  
2. 표시된 목록에서 방금 만든 네임스페이스를 선택합니다.
 
    ![네임스페이스 선택][select-namespace]
 
3. **Connection Information**을 클릭합니다.

    ![연결 정보][connection-information]
  
4. **Access 연결 정보** 창에서 SAS 키 및 키 이름이 포함된 연결 문자열을 찾습니다.

    ![연결 정보 액세스][access-connection-information]
  
5. 키를 적어 두거나 클립보드에 복사해 둡니다.

<!--Image references-->

[select-create]: ./media/service-bus-create-namespace-portal/select-create.png
[namespace-name]: ./media/service-bus-create-namespace-portal/namespace-name.png
[created-successfully]: ./media/service-bus-create-namespace-portal/created-successfully.png
[select-service-bus]: ./media/service-bus-create-namespace-portal/select-service-bus.png
[select-namespace]: ./media/service-bus-create-namespace-portal/select-namespace.png
[connection-information]: ./media/service-bus-create-namespace-portal/connection-information.png
[access-connection-information]: ./media/service-bus-create-namespace-portal/access-connection-information.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[classic-portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0615_2016-->