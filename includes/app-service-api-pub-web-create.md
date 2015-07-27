1. **솔루션 탐색기**에서 솔루션이 아니라 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다. 

	![프로젝트 게시 메뉴 옵션](./media/app-service-api-pub-web-create/20-publish-gesture-v3.png)

2. **프로필** 탭을 클릭하고 **Microsoft Azure API 앱(미리 보기)**을 클릭합니다.

	![웹 게시 대화 상자](./media/app-service-api-pub-web-create/21-select-api-apps-for-deployment-v2.png)

3. **새로 만들기**를 클릭하여 Azure 구독에서 새 API 앱을 프로비전합니다.

	![기존 API 서비스 선택 대화 상자](./media/app-service-api-pub-web-create/23-publish-to-apiapps-v3.png)

4. **API 앱 만들기** 대화 상자에서 다음을 입력합니다.

	- **API 앱 이름**에 대해 자습서에 사용 중인 이름을 입력합니다. 
	- Azure 구독이 여러 개 있는 경우 사용할 구독을 선택합니다.
	- **앱 서비스 계획**에 대해 기존 앱 서비스 계획 중에서 선택하거나 **새 앱 서비스 계획 만들기**를 선택하고 새 계획의 이름을 입력합니다. 
	- **리소스 그룹**에 대해 기존 리소스 그룹 중에서 선택하거나 **새 리소스 그룹 만들기**를 선택하고 이름을 입력합니다. 
	- **액세스 수준**에 대해 **Available to anyone(누구나 사용 가능)**을 선택합니다. 나중에 Azure Preview 포털을 통해 액세스를 제한할 수 있습니다.
	- **지역**에 대해 가까운 지역을 선택합니다.  

	![Microsoft Azure 웹앱 구성 대화 상자](./media/app-service-api-pub-web-create/24-new-api-app-dialog-v3.png)

5. **확인**을 클릭하여 구독에 API 앱을 만듭니다.

	이 프로세스는 몇 분 정도 걸릴 수 있으므로 Visual Studio에서 확인 대화 상자를 표시합니다.

	![API 서비스 만들기 시작 확인 메시지](./media/app-service-api-pub-web-create/25-api-provisioning-started-v3.png)

6. 확인 대화 상자에서 **확인**을 클릭합니다.
 
	프로비전 프로세스에서는 Azure 구독에 리소스 그룹 및 API 앱을 만듭니다. Visual Studio의 **Azure App Service Activity(Azure 앱 서비스 활동)** 창에 진행률이 표시됩니다.

	![Azure 앱 서비스 활동 창을 통한 상태 알림](./media/app-service-api-pub-web-create/26-provisioning-success-v3.png)

<!---HONumber=July15_HO3-->