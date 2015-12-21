<properties 
	pageTitle="API 앱 관리" 
	description="Azure 포털 및 Visual Studio 서버 탐색기를 사용하여 Azure 앱 서비스 API 앱을 관리하는 방법을 알아봅니다." 
	services="app-service\api" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na"
	ms.topic="article" 
	ms.date="12/04/2015" 
	ms.author="tdykstra"/>

# Azure 앱 서비스에서 API 앱 관리

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

이 문서에서는 [Azure Preview 포털](https://portal.azure.com/)을 사용하여 API 앱 관리 및 모니터링 작업을 수행하는 방법을 보여 주었습니다. 다음은 일부 가능한 작업입니다:

- 인증 구성 
- 자동 확장 사용
- 로그 보기
- 주어진 요청 수와 API 앱에서 사용 중인 데이터 양을 참조하십시오.
- API 앱 백업 및 알림 만들기
- 역할 기반 액세스 보안 구성

또한 Visual Studio의 서버 탐색기 창에서 일부 관리 작업을 수행하는 방법을 보여 줍니다.

## Azure Preview 포털에서 API 앱 및 게이트웨이 블레이드

Azure 앱 서비스에서 API 앱은 웹 서비스를 호스트하는 추가 기능이 있는 [웹 앱](../app-service-web/app-service-web-overview.md)입니다. Azure 포털에는 API 관련 기능을 관리하는 **API 앱** 블레이드와 기본 웹앱을 관리하는 **API 앱 호스트** 블레이드가 있습니다.

하나 이상의 API 앱이 포함된 모든 리소스 그룹에는 *게이트웨이*도 포함되어 있습니다. 게이트웨이는 프록시 역할을 합니다. 즉, 리소스 그룹의 모든 API 앱에 대한 인증 및 기타 관리 기능을 처리합니다. API 앱과 마찬가지로 게이트웨이는 추가 기능이 있는 웹앱이므로 게이트웨이를 관리하기 위한 두 개의 포털 블레이드, 즉 게이트웨이 관련 기능을 관리하는 **게이트웨이** 블레이드와 기본 웹앱을 관리하는 **게이트웨이 호스트** 블레이드가 있습니다.

### API 앱 블레이드에서만 수행할 수 있는 작업

**API 앱** 블레이드는 다음과 같은 작업에 사용됩니다.

- 액세스 수준 구성 - **설정 > 응용 프로그램 설정**을 클릭합니다. 기본값은 내부입니다. 즉, 동일한 리소스 그룹에 있는 API 앱에서만 API 앱을 호출할 수 있습니다. 자세한 내용은 [API 앱 보호](app-service-api-dotnet-add-authentication.md)를 참조하세요.   
- 업데이트 정책 구성 - **설정 > 응용 프로그램 설정**을 클릭합니다. 기본값은 **설정**입니다. 즉, 새 버전의 API 앱이 마켓플레이스에 게시되면 주요 변경 내용이 아닌 경우 API 앱이 새 버전으로 자동으로 업데이트됩니다.  
- API 앱에서 나가는 호출에 대한 인증 구성 - **설정 > 인증**을 클릭합니다. API 앱에서 인증이 필요한 외부 서비스를 호출한 경우 필요한 구성 값이 여기에 입력됩니다. 예를 들어 Dropbox 커넥터가 Dropbox 서비스에 액세스하려면 클라이언트 ID 및 클라이언트 암호가 필요합니다.
- [RBAC](../role-based-access-control-configure.md) 구성 - **설정 > 사용자**를 클릭합니다. 여기에서 구성한 사용자 액세스는 API 앱 관련 기능에 액세스할 수 있는 사용자만 결정합니다. 웹앱 기능에 대한 RBAC를 구성하려면 **API 앱 호스트** 블레이드를 사용합니다. 일반적으로 API 앱에 대한 RBAC 설정과 API 앱 호스트에 대한 RBAC 설정을 동기화된 상태로 유지할 수 있습니다. API 앱 호스트에는 액세스할 수 없고 API 앱에만 액세스할 수 있는 권한을 제공한 경우 해당 사용자는 **API 앱** 블레이드에서 실제로 API 앱 호스트와 관련된 기능을 사용할 수 없습니다.
- API 정의 보기 - API 앱에서 노출하는 메서드 목록을 보려면 **요약** 섹션에서 **API 정의**를 클릭합니다.
- [하이브리드 연결 관리자 설치](../app-service-logic/app-service-logic-hybrid-connection-manager.md) 하이브리드 연결 관리자는 SQL Server 또는 SAP와 같이 온-프레미스 시스템에 연결 기능을 제공합니다. 이 하이브리드 연결은 Azure 리소스와 온-프레미스 리소스 간의 보안을 연결하고 제어하기 위해 Azure 서비스 버스를 사용합니다.

### API 앱 블레이드와 API 앱 호스트 블레이드 모두에서 수행할 수 있는 작업 

**API 앱** 블레이드를 사용하여 기본 웹앱과 관련된 많은 작업을 수행할 수 있습니다. 예를 들어, 아래 작업을 수행할 수 있습니다.

* API 앱을 호스트하는 웹 앱을 중지, 시작 및 다시 시작합니다.  
- 200, 400, 또는 500 HTTP 상태 코드와 같은 일반적으로 알려진 HTTP 오류 코드를 포함하여 다양한 성능 메트릭을 추가하기 위해 **요청 및 오류**를 선택합니다
- 응답 시간, API 앱에 대한 요청 수 및 유입 및 유출되는 데이터 양을 참조하십시오. 
- 메트릭이 선택한 임계값을 초과하는 경우 전자 메일 경고를 만듭니다. 
- API 앱이 사용하는 **CPU** 양을 참조하고 MB의 현재 **사용 할당량**을 검토하여 비용 계층에 기반한 최대 데이터 사용량을 참조하십시오.
- API 앱 실행의 잠재적인 비용을 확인하려면 **예상 지출**을 참조하십시오.
- 웹 서버 로그 및 FREB 로그를 포함하여 응용 프로그램 로그 및 다른 IIS 로그를 봅니다.
- 프로세스 탐색기를 열려면 **프로세스**를 선택합니다. 이것은 스레드 수 및 메모리 사용을 포함하여 사용자의 웹 인스턴스 및 해당 속성을 보여줍니다.

이러한 작업은 **API 앱 호스트** 블레이드에서도 수행할 수 있습니다. 이는 두 블레이드에서 동일한 UI의 대부분을 공유하기 때문입니다. 예를 들어, **API 앱** 블레이드의 **중지**, **시작** 및 **다시 시작** 단추는 **API 앱 호스트** 블레이드의 **중지**,**시작** 및 **다시 시작** 단추와 효과가 정확히 같습니다. 마찬가지로 **API 앱** 블레이드에서 제공되는 모니터링 정보는 **API 앱 호스트** 블레이드에 표시되는 것과 동일합니다.

**API 앱 호스트** 블레이드와 중복되지 않고 **API 앱** 블레이드에서만 제공되는 기능은 이전 섹션에 나열되어 있습니다.

### API 앱 호스트 블레이드에서만 수행할 수 있는 작업

웹앱에 대해 수행하는 모든 작업에 **API 앱 호스트** 블레이드를 사용할 수 있습니다.

### 게이트웨이 블레이드에서만 수행할 수 있는 작업

**게이트웨이** 블레이드는 다음과 같은 작업에 사용됩니다.

- API 앱으로 들어오는 호출에 대한 인증 공급자 구성 - **설정 > ID**를 클릭합니다. 게이트웨이에서 리소스 그룹의 API 앱을 호출하도록 허용하기 전에 사용자를 인증해야 하는 경우 필요한 구성 값이 여기에 입력됩니다. 자세한 내용은 [Azure 앱 서비스에서 SaaS 커넥터 구성 및 테스트](app-service-api-connnect-your-app-to-saas-connector.md)를 참조하세요. 
- [RBAC](../role-based-access-control-configure.md) 구성 - **설정 > 사용자**를 클릭합니다. 여기에서 구성한 사용자 액세스는 모든 웹 앱에서 공유할 수 있는 기능이 아니라 게이트웨이 관련 기능에 액세스할 수 있는 사용자만 결정합니다.

### 게이트웨이 블레이드와 게이트웨이 호스트 블레이드 모두에서 수행할 수 있는 작업 

게이트웨이 및 게이트웨이 호스트 블레이드는 API 앱 및 API 앱 호스트 블레이드와 동일한 UI를 공유합니다.

### 게이트웨이 호스트 블레이드에서만 수행할 수 있는 작업

웹앱에 대해 수행하는 모든 작업에 **게이트웨이 호스트** 블레이드를 사용할 수 있습니다.

## <a id="navigate"></a>API 앱 및 게이트웨이 블레이드로 이동하는 방법 

**API 앱** 블레이드로 이동하는 한 가지 방법은 Azure 포털의 찾아보기 기능을 사용하는 것입니다. 포털 홈 페이지에서 **찾아보기 > API 앱**을 클릭하여 관리할 수 있는 모든 API 앱을 볼 수 있습니다.

![](./media/app-service-api-manage-in-portal/browse.png)

![](./media/app-service-api-manage-in-portal/apiappslist.png)

### API 앱 블레이드로 이동

**API 앱** 목록 블레이드에서 행을 클릭하면 포털에 **API 앱** 블레이드가 표시됩니다.

![](./media/app-service-api-manage-in-portal/apiappblade.png)

### API 앱 호스트 블레이드로 이동

**API 앱 호스트** 블레이드로 이동하려면 **API 앱** 블레이드에서 **API 앱 호스트**를 클릭합니다.

![](./media/app-service-api-manage-in-portal/apiappbladetohost.png)

![](./media/app-service-api-manage-in-portal/apiapphostbladenocallouts.png)

### 게이트웨이 블레이드로 이동

**게이트웨이** 블레이드로 이동하려면 **API 앱** 블레이드에서 **게이트웨이** 링크를 클릭합니다.
   
![](./media/app-service-api-manage-in-portal/apiappbladegotogateway.png)

![](./media/app-service-api-manage-in-portal/gatewaybladenocallout.png)

### 게이트웨이 호스트 블레이드로 이동

**게이트웨이 호스트** 블레이드로 이동하려면 **게이트웨이** 블레이드에서 **게이트웨이 호스트** 링크를 클릭합니다.
   
![](./media/app-service-api-manage-in-portal/gatewaybladetohost.png)

![](./media/app-service-api-manage-in-portal/gatewayhost.png)

## Visual Studio의 서버 탐색기에서 API 앱 액세스

Visual Studio의 **서버 탐색기**에서 원격 디버깅 세션을 시작하고, 스트리밍 로그를 보고, 포털에서 API 앱 블레이드를 여는 메뉴 항목을 클릭할 수 있습니다.

서버 탐색기에서 API 앱으로 이동하려면 그림과 같이 **Azure > 앱 서비스 > [리소스 그룹 이름] > [API 앱 이름]**을 클릭합니다.

![](./media/app-service-api-manage-in-portal/se.png)

## 다음 단계

이 문서에서는 Azure 포털을 사용하여 API 앱 관리 작업을 수행하는 방법을 보여 주었습니다. API 앱 갤러리에서 설치하는 API 앱의 경우 [기본 제공 API 앱 및 커넥터 관리 및 모니터링](../app-service-logic/app-service-logic-monitor-your-connectors.md)도 참조하세요.

명령줄을 사용하여 API 앱을 관리 하는 방법에 대한 정보는 문서의 왼쪽(넓은 브라우저 창) 또는 위쪽(좁은 브라우저 창)에 표시되는 메뉴의 **자동화** 섹션의 문서를 참조하십시오.

<!---HONumber=AcomDC_1210_2015-->