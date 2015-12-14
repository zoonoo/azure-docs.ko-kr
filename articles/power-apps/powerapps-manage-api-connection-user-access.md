<properties
	pageTitle="PowerApps에서 새 API를 추가하거나 만들고 사용자에게 권한 부여 | Microsoft Azure"
	description="Azure 포털에서 새 API, 연결 또는 연결 프로필을 추가, 생성 및 구성하고 사용자에게 액세스 권한 부여"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>


# 새 API 추가, 연결 추가 및 사용자에게 액세스 권한 부여

API는 [앱 서비스 환경](powerapps-get-started-azure-portal.md) 내에 존재합니다. API는 PowerApps용으로 제공되는 API나 앱 서비스 환경에 호스트되는 API 앱 또는 Swagger 2.0을 사용하여 만들 수 있습니다. PowerApps에 손쉽게 추가할 수 있는 미리 만들어진 API도 많이 있습니다. JSON 형식으로 된 자신의 API 또는 Swagger 2.0을 업로드할 수도 있습니다.

이 문서는:

- PowerApps에 API를 추가하고 회사 내 사용자에게 API 사용 권한(API 속성 변경 포함)을 부여하는 단계를 나열합니다.
- API에 연결을 추가하고 회사 내 사용자에게 연결 사용 권한을 부여하는 단계를 나열합니다.


#### 시작하기 위한 필수 조건

- [Azure 구독에서 PowerApps](powerapps-get-started-azure-portal.md)을 사용하도록 설정합니다.
- [앱 서비스 환경](powerapps-get-started-azure-portal.md)을 만듭니다.
- 다음 메서드를 사용하여 API를 만듭니다.  
	- [Microsoft 관리되는 API 또는 IT 관리되는 API](powerapps-register-from-available-apis.md)를 만듭니다.
	- [앱 서비스 환경](powerapps-register-api-hosted-in-app-service.md) 내에서 호스트되는 API를 만듭니다.
	- [Swagger 2.0 API 정의](powerapps-register-existing-api-from-api-definition.md)를 사용하여 만듭니다.


## 사용자에게 API에 대한 액세스 부여
API를 만들고 앱 서비스 환경에 추가했으니, 이제 회사 내 사용자에게 사용 권한을 부여할 차례입니다.

1. PowerApps에서 **Manage APIs**(API 관리)를 선택하고 자신의 API를 선택합니다. 예를 들어, *MS Power BI* API를 만들었으면 해당 API를 선택하여 블레이드를 엽니다. **API 사용자 액세스**를 선택합니다. ![][1]  

2. **추가**를 선택하여 사용자를 추가하고 권한을 선택합니다. 완료되면 **추가**를 선택하여 변경 내용을 저장합니다. **API 사용자 액세스** 창의 사용자 또는 그룹 수가 증가됩니다.


## API에 대한 새 연결 만들기
다음은 API에 대한 “연결”을 생성하는 단계이며, 연결 문자열과 유사합니다. 이 단계를 통해 API를 “백 엔드” 시스템에 성공적으로 연결할 수 있습니다. PowerApps Enterprise 공개 미리 보기에서는 SQL Server 연결만 추가하고 구성할 수 있습니다. 더 많은 옵션이 추가되고 있습니다.

- [SQL Server 연결 만들기](powerapps-create-api-sqlserver.md)

## 사용자에게 연결에 대한 런타임 액세스 부여
회사 내 사용자에게 연결을 사용할 수 있는 권한을 부여합니다.

1. API를 열고 **연결**을 선택한 후 특정 연결을 선택합니다. 그러면 새 블레이드가 열리고 연결 이름이 위쪽에 나열됩니다. 
2. 새 블레이드에서 **Connection user access**(연결 사용자 액세스)를 선택합니다. 아래 예제에서는 **Hybrid Tunnel** 연결이 선택되었습니다. 새 블레이드가 열리면 그 블레이드에서 **Connection user access**(연결 사용자 액세스)를 선택합니다. ![][2]
  
3. **Connection user access**(연결 사용자 액세스)에서 **추가**를 선택한 후에 부여할 권한을 선택합니다. ![][3]
  
4. 사용자 또는 그룹을 추가합니다. **추가**를 선택하여 변경 내용을 저장합니다.

사용자에게 API 및 API의 연결에 대한 권한이 있으면, PowerApps에서 만든 자신의 앱에 해당 API를 추가할 수 있습니다. 구체적으로 살펴보면 다음과 같습니다.

- 사용자는 PowerApps의 **사용 가능한 연결**에 나열되는 API를 볼 수 있습니다.
- 사용자는 PowerApps의 **내 연결** 아래 표시되는 연결을 볼 수 있습니다.


## API 삭제
이전에 추가한 API를 삭제할 수 있습니다. PowerApps에서 **Manage APIs**(API 관리)를 선택하고 해당 API를 선택한 후 **삭제**를 선택합니다. ![][4]


## 요약 및 다음 단계
이 문서에서:

- API를 추가하고 회사 내 사용자에게 API 사용 권한을 부여했습니다. 이 단계를 사용하여 런타임 액세스를 언제든 관리할 수도 있습니다. 예를 들어, userA가 회사를 떠나면 Azure 포털을 사용하여 이 사용자의 권한을 손쉽게 제거할 수 있습니다. UserB가 회사에 입사해도 시나리오는 같습니다.
- 연결을 추가했습니다. (연결 문자열과 유사합니다.) 이 단계를 통해 Azure에 호스트되는 API를 시스템(예: 온-프레미스 SQL Server)에 연결할 수 있습니다. 회사 내 사용자에게 연결을 사용할 수 있는 권한도 부여했습니다. 
- 작업에 따라서 다양한 블레이드 작업을 했습니다. 연결을 추가하려면, API를 열고 해당 블레이드를 사용합니다. 사용자에게 액세스를 부여하려면, 무엇에 대한 액세스를 부여하는지에 따라서 API 또는 연결을 엽니다. 
- 앱 서비스 환경 내에 만든 API를 삭제할 수 있습니다.

이제 [PowerApps을 관리하고 모니터링](powerapps-manage-monitor-usage.md)할 수 있습니다.

[1]: ./media/powerapps-manage-api-connection-user-access/apiuseraccess.png
[2]: ./media/powerapps-manage-api-connection-user-access/connectionuseraccess.png
[3]: ./media/powerapps-manage-api-connection-user-access/selectpermission.png
[4]: ./media/powerapps-manage-api-connection-user-access/deleteapi.png

<!---HONumber=AcomDC_1203_2015-->