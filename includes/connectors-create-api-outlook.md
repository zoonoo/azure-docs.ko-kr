## <a name="connect-to-outlookcom"></a>Outlook.com에 연결
### <a name="prerequisites"></a>필수 조건
* Outlook.com 계정

논리 앱에서 Outlook.com 계정을 사용하려면 먼저 Outlook.com 계정에 연결하도록 논리 앱에 권한을 부여해야 합니다. 다행히 Azure 포털의 논리 앱 내에서 이를 쉽게 수행할 수 있습니다. 

Outlook.com 계정에 연결하도록 논리 앱에 권한을 부여하는 단계는 다음과 같습니다.

1. 모든 논리 앱은 트리거로 시작해야 하므로 논리 앱을 만든 후 디자이너가 열리면서 논리 앱을 시작하는 데 사용할 수 있는 트리거 목록을 표시합니다.
   
   ![](./media/connectors-create-api-outlook/office365-outlook-0.png)
2. 검색 상자에 "outlook"을 입력합니다. 이름에 "Outlook"이 있는 모든 트리거를 나열하도록 목록이 필터링됩니다. ![](./media/connectors-create-api-outlook/office365-outlook-0-5.png)
3. **Office 365 Outlook - 새 전자 메일에**를 선택합니다.   
   이전에 Outlook에 대한 연결을 만들지 않은 경우 Outlook.com 자격 증명을 제공하라는 메시지가 표시됩니다. 이러한 자격 증명을 사용하여 Outlook.com 계정의 데이터에 연결하도록 논리 앱에 권한을 부여하고 해당 데이터에 액세스할 수 있습니다. ![](./media/connectors-create-api-outlook/office365-outlook-1.png)
4. Outlook에 대한 자격 증명을 제공하고 로그인합니다. ![](./media/connectors-create-api-outlook/office365-outlook-2.png)  
   이것으로 끝입니다. 이제 Outlook에 대한 연결이 만들어졌습니다. 이 연결은 만들어진 다른 논리 앱에서 사용할 수 있습니다.



<!--HONumber=Nov16_HO3-->


