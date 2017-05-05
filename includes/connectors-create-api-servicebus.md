### <a name="prerequisites"></a>필수 조건
[Service Bus](https://azure.microsoft.com/services/service-bus/) 계정이 있어야 합니다.  

논리 앱에서 Azure 서비스 버스 계정을 사용하려면 먼저 서비스 버스 계정에 연결하도록 논리 앱에 권한을 부여해야 합니다. 다행히 Azure 포털의 논리 앱 내에서 이를 쉽게 수행할 수 있습니다.  

서비스 버스 계정에 연결하도록 논리 앱에 권한을 부여하는 단계는 다음과 같습니다.  

1. 논리 앱 디자이너에서 Service Bus에 대한 연결을 만들려면 드롭다운 목록에서 **Microsoft 관리 API 표시**를 선택합니다. 그런 다음 검색 상자에 **Service Bus**를 입력합니다. 사용할 트리거 또는 작업을 선택합니다.  
    ![Service Bus 연결 이미지 1](./media/connectors-create-api-servicebus/servicebus-1.png)  
2. 이전에 서비스 버스에 대한 연결을 만들지 않은 경우 서비스 버스 자격 증명을 제공하라는 메시지가 표시됩니다. 이러한 자격 증명을 사용하여 서비스 버스 계정의 데이터에 연결하도록 논리 앱에 권한을 부여하고 해당 데이터에 액세스할 수 있습니다. 서비스 버스 커넥터에는 서비스 버스 네임스페이스에 대한 연결 문자열이 필요합니다. 또한 **관리** 사용 권한도 필요합니다. 연결 문자열이 네임스페이스에 대한 것인지 또는 특정 엔티티에 대한 것인지를 알려면 `EntityPath` 매개 변수가 포함되어 있는지 확인합니다. 포함된 경우 논리 앱에 적절한 연결 문자열이 아닙니다.  
    ![Service Bus 연결 문자열](./media/connectors-create-api-servicebus/connectionstring.png)
3. 네임스페이스에 대한 연결 문자열이 수신되면 Logic Apps에서 API 연결에 사용할 수 있습니다.  
    ![서비스 버스 연결 이미지 2](./media/connectors-create-api-servicebus/servicebus-2.png)  
4. 연결이 만들어졌으므로 이제 논리 앱의 다른 단계를 진행할 수 있습니다.  
    ![서비스 버스 연결 이미지 3](./media/connectors-create-api-servicebus/servicebus-3.png)   

