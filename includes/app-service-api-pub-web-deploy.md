1. **솔루션 탐색기**에서 API 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택하여 게시 대화 상자를 엽니다. 앞에서 만든 게시 프로필을 미리 선택해야 합니다. 
2. **게시**를 클릭하여 배포 프로세스를 시작합니다.
   
    ![API 앱 배포](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v3.png)
   
    **Azure App Service Activity(Azure 앱 서비스 활동)** 창에 배포 진행률이 표시됩니다.
   
    ![Azure 앱 서비스 활동 창의 상태 알림](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v4.png)
   
    이 배포 과정 중에 Visual Studio가 *게이트웨이*를 자동으로 다시 시작하려 합니다. 게이트웨이는 리소스 그룹의 모든 API 앱에 대한 관리 기능을 처리하는 웹앱이며, API 앱의 API 정의 또는 *apiapp.json* 파일에서 변경 사항을 인식하도록 다시 시작해야 합니다.
   
    다른 메서드를 사용하여 API 앱을 배포하는 경우 또는 Visual Studio가 게이트웨이를 다시 시작하는 데 실패한 경우, 게이트웨이를 수동으로 다시 시작해야 합니다. 다음 단계는 수행하는 방법을 설명합니다.
3. 브라우저에서 [Azure Preview 포털](https://portal.azure.com)로 이동합니다.
4. 배포하려는 API 앱의 **API 앱** 블레이드로 이동합니다.
   
    **API 앱** 블레이드 및 블레이드를 찾는 방법에 대한 자세한 내용은 [관리 API 앱](../articles/app-service-api/app-service-api-manage-in-portal.md)을 참조하십시오.
5. **게이트웨이** 링크를 클릭합니다.
6. **게이트웨이** 블레이드에서 **다시 시작**을 클릭합니다.
   
    ![](./media/app-service-api-pub-web-deploy/restartgateway.png)

<!---HONumber=Oct15_HO3-->