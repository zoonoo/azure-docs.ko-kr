
1. [Azure Portal](https://portal.azure.com) 로 이동하여 Azure 계정으로 로그인합니다.

2. **+새로 만들기** > **Compute** > **함수 앱**을 클릭하고 , **구독**을 선택하고 함수 앱을 식별하는 고유한 **앱 이름**을 입력한 후 다음 설정을 지정합니다.
   
   * **[리소스 그룹](../articles/azure-resource-manager/resource-group-overview.md)**: **새로 만들기**를 선택하고 새 리소스 그룹에 대한 이름을 입력합니다. 
   * **[호스팅 계획](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)**은 이러한 계획 중 하나일 수 있습니다. 
     * **소비 계획**: Azure Functions의 기본 계획 유형입니다. 소비 계획을 선택하는 경우 **위치**를 선택하고 **메모리 할당**(MB)도 설정해야 합니다. 메모리 할당이 비용에 미치는 영향에 대한 자세한 내용은 [Azure Functions 가격 책정](https://azure.microsoft.com/pricing/details/functions/)을 참조하세요. 
     * **App Service 계획**: App Service 계획을 사용하려면 **App Service 계획/위치**를 만들거나 기존 계획이나 위치를 선택해야 합니다. 이러한 설정은 [위치, 기능, 비용을 결정하고 앱과 연결된 리소스를 계산](https://azure.microsoft.com/pricing/details/app-service/)합니다.  
   * **저장소 계정**: 각 함수 앱에 저장소 계정이 필요합니다. 기존 저장소 계정을 선택하거나 [저장소 계정을 만들](../articles/storage/storage-create-storage-account.md#create-a-storage-account) 수 있습니다. 
     
    ![Azure Portal에서 함수 앱 만들기](./media/functions-create-function-app-portal/function-app-create-flow.png)

3. **만들기** 를 클릭하여 새 함수 앱을 프로비전하고 배포합니다.  