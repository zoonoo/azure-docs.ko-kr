<properties 
	pageTitle="Azure 앱 서비스에서 API 앱 배포" 
	description="Azure 구독에 API 앱 프로젝트를 배포하는 방법을 알아봅니다." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="bradyg;tarcher"/>

# Azure 앱 서비스에서 API 앱 배포 

## 개요

이 자습서에서는 [이전 자습서](app-service-dotnet-create-api-app.md)에서 만든 Web API 프로젝트를 새로운 [API 앱](app-service-api-apps-why-best-platform.md)에 배포합니다. Visual Studio를 사용하여 [Azure 앱 서비스](../app-service/app-service-value-prop-what-is.md)에서 API 앱 리소스를 만들고 Web API 코드를 Azure API 앱에 배포합니다.

### 다른 배포 옵션

API 앱을 배포할 수 있는 다른 여러 방법이 있습니다. API 앱은 웹 서비스를 호스트하기 위한 추가 기능이 있는 [웹앱](../app-service-web/app-service-web-overview.md)으로, [웹앱에 사용할 수 있는 모든 배포 방법은](../app-service-web/web-sites-deploy.md) API 앱에도 사용할 수 있습니다. API 앱을 호스트하는 웹앱을 Azure Preview 포털에서는 API 앱 호스트라고 하며, API 앱 호스트 포털 블레이드를 사용하여 배포를 구성할 수 있습니다. API 앱 호스트 블레이드에 대한 자세한 내용은 [API 앱 관리](app-service-api-manage-in-portal.md)를 참조하세요.

API 앱이 웹앱을 기반으로 한다는 사실은 ASP.NET 이외의 플랫폼용으로 작성된 코드를 API 앱에 배포할 수 있음을 의미하기도 합니다. Git를 사용하여 Node.js 코드를 API 앱에 배포하는 예제는 [Azure 앱 서비스에서 Node.js API 앱 만들기](app-service-api-nodejs-api-app.md)를 참조하세요.
 
## API 앱 배포 

이 섹션에서는 Azure 구독에 API 앱을 배포하는 데 필요한 단계를 보여 줍니다.

1. **솔루션 탐색기**에서 솔루션이 아니라 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시...**를 클릭합니다. 

	![프로젝트 게시 메뉴 옵션](./media/app-service-dotnet-deploy-api-app/20-publish-gesture-v3.png)

2. **프로필** 탭을 클릭하고 **Microsoft Azure API 앱(미리 보기)**을 클릭합니다.

	![웹 게시 대화 상자](./media/app-service-dotnet-deploy-api-app/21-select-api-apps-for-deployment-v2.png)

3. **새로 만들기**를 클릭하여 Azure 구독에서 새 API 앱을 프로비전합니다.

	![기존 API 서비스 선택 대화 상자](./media/app-service-dotnet-deploy-api-app/23-publish-to-apiapps-v3.png)

4. **API 앱 만들기** 대화 상자에서 다음을 입력합니다.

	- **API 앱 이름**에 ContactsList를 입력합니다. 
	- Azure 구독이 여러 개 있는 경우 사용할 구독을 선택합니다.
	- **앱 서비스 계획**에 대해 기존 앱 서비스 계획 중에서 선택하거나 **새 앱 서비스 계획 만들기**를 선택하고 새 계획의 이름을 입력합니다. 
	- **리소스 그룹**에 대해 기존 리소스 그룹 중에서 선택하거나 **새 리소스 그룹 만들기**를 선택하고 이름을 입력합니다. 이름은 고유해야 합니다. 앱 이름을 접두사로 사용하고 Microsoft ID(@ 기호 없이)와 같은 일부 개인 정보를 추가하는 것이 좋습니다.  
	- **액세스 수준**에 대해 **Available to anyone(누구나 사용 가능)**을 선택합니다. 이 옵션은 API를 완전히 공용으로 만들며, 이 자습서에서는 이렇게 해도 괜찮습니다. 나중에 Azure Preview 포털을 통해 액세스를 제한할 수 있습니다.
	- **지역**에 대해 가까운 지역을 선택합니다.  

	![Microsoft Azure 웹앱 구성 대화 상자](./media/app-service-dotnet-deploy-api-app/24-new-api-app-dialog-v3.png)

5. **확인**을 클릭하여 구독에 API 앱을 만듭니다. 이 프로세스는 몇 분 정도 걸릴 수 있으므로 Visual Studio에서 확인 대화 상자를 표시합니다.

	![API 서비스 만들기 시작 확인 메시지](./media/app-service-dotnet-deploy-api-app/25-api-provisioning-started-v3.png)

6. 확인 대화 상자에서 **확인**을 클릭합니다. 프로비전 프로세스에서는 Azure 구독에 리소스 그룹 및 API 앱을 만듭니다. Visual Studio의 **Azure App Service Activity(Azure 앱 서비스 활동)** 창에 진행률이 표시됩니다.

	![Azure 앱 서비스 활동 창을 통한 상태 알림](./media/app-service-dotnet-deploy-api-app/26-provisioning-success-v3.png)

7. API 앱이 프로비전되면 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택하여 게시 대화 상자를 다시 엽니다. 이전 단계에서 만든 게시 프로필이 미리 선택되어야 합니다. **게시**를 클릭하여 배포 프로세스를 시작합니다.

	![API 앱 배포](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v3.png)

	**Azure App Service Activity(Azure 앱 서비스 활동)** 창에 배포 진행률이 표시됩니다.

	![Azure 앱 서비스 활동 창의 상태 알림](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v4.png)

	이 배포 과정 중에 Visual Studio가 *게이트웨이*를 자동으로 다시 시작합니다. 게이트웨이는 리소스 그룹의 모든 API 앱에 대한 관리 기능을 처리하는 웹앱이며, API 앱에서 변경한 API를 인식하도록 다시 시작해야 합니다. 다른 메서드를 사용하여 API 앱을 배포하는 경우 및 배포가 API를 변경하는 경우, 게이트웨이를 수동으로 다시 시작해야 합니다. 포털에서 게이트웨이를 다시 시작하는 방법에 대한 정보는 [API 앱 관리](app-service-api-manage-in-portal.md)를 참조하십시오.

## Azure Preview 포털에서 앱 보기

이 섹션에서는 포털로 이동하여 API 앱에 사용할 수 있는 기본 설정을 보고 API 앱에 대해 반복적인 변경을 수행합니다. 배포할 때마다 포털에 API 앱에 대해 수행하는 변경 내용이 반영됩니다.

1. 브라우저에서 [Azure Preview 포털](https://portal.azure.com)로 이동합니다. 

2. 사이드바에서 **찾아보기** 단추를 클릭하고 **API 앱**을 선택합니다.

	![Azure 포털의 옵션 찾아보기](./media/app-service-dotnet-deploy-api-app/27-browse-in-portal-v3.png)

3. 구독의 API 앱 목록에서 만든 API를 선택합니다.

	![API 앱 목록](./media/app-service-dotnet-deploy-api-app/28-view-api-list-v3.png)

4. **API 정의**를 클릭합니다. 앱의 **API 정의** 블레이드에 앱을 만들 때 정의한 API 작업 목록이 표시됩니다.

	![API 정의](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

5. 이제 Visual Studio의 프로젝트로 돌아가서 다음 코드를 **ContactsController.cs** 파일에 추가합니다. 이 코드는 새 `Contact` 인스턴스를 API에 게시하는 데 사용할 수 있는 **Post** 메서드를 추가합니다.

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	![컨트롤러에 Post 메서드 추가](./media/app-service-dotnet-deploy-api-app/30-post-method-added-v3.png)

6. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

	![프로젝트 게시 상황에 맞는 메뉴](./media/app-service-dotnet-deploy-api-app/31-publish-gesture-v3.png)

7. **설정** 탭을 클릭합니다.

8. **구성** 드롭다운에서 **디버그**를 선택합니다.

	![웹 게시 설정](./media/app-service-dotnet-deploy-api-app/36.5-select-debug-option-v3.png)

9. **미리 보기** 탭을 클릭합니다.

10. **미리 보기 시작**을 클릭하여 적용될 변경 내용을 확인합니다.

	![웹 게시 대화 상자](./media/app-service-dotnet-deploy-api-app/39-re-publish-preview-step-v2.png)

11. **게시**를 클릭합니다.

12. 게시 프로세스가 완료되면 포털로 돌아가서 **API 정의** 블레이드를 닫았다가 다시 엽니다. 방금 만들어 Azure 구독에 직접 배포한 새 API 끝점을 볼 수 있습니다.

	![API 정의](./media/app-service-dotnet-deploy-api-app/38-portal-with-post-method-v4.png)

## 다음 단계

Visual Studio의 직접 배포 기능을 통해 API를 빠르게 반복하고 배포하며 API가 올바르게 작동하는지 테스트하는 작업을 얼마나 쉽게 수행할 수 있는지 살펴봤습니다. [다음 자습서](../app-service-dotnet-remotely-debug-api-app.md)에서는 Azure에서 API 앱이 실행되는 동안 API 앱을 디버그하는 방법을 확인할 수 있습니다.
 

<!---HONumber=62-->