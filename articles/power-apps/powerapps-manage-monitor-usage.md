<properties
	pageTitle="PowerApps 엔터프라이즈에서 앱 사용률 점검 | Microsoft Azure"
	description="Azure 포털에서 모든 앱, API, 사용자, 앱 요청 및 업데이트 권한 참조"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/02/2016"
   ms.author="litran"/>


# PowerApps 관리 및 보호
앱 서비스 환경을 만들고 API 및 해당 연결을 추가합니다. 이제 조직의 사용자가 이러한 API 및 연결을 사용하기 시작할 수 있습니다. 또한 조직에서 만든 모든 앱을 관리할 수 있습니다. 이러한 옵션에는 다음이 포함됩니다.

- PowerApps, 웹 앱, 논리 앱, 모바일 앱 등을 포함하여 앱 서비스 환경 내에서 다양한 앱을 참조하세요.
- 특정 앱에서 사용 하는 모든 API를 참조하세요.
- 앱 서비스 환경 내에서 앱에 대한 사용자 액세스를 확인하고 관리합니다. 
- API 및 해당 연결에 대한 사용자 액세스를 확인하고 관리합니다. 

앱 서비스 환경은 웹 앱 및 논리 앱을 포함한 다른 앱을 본인이 추가하는 것입니다. 그런 다음 PowerApps 엔터프라이즈를 열어 이러한 앱을 확인하고 관리할 수 있습니다.


## PowerApps 관리자 추가
PowerApps 엔터프라이즈를 사용하도록 설정하고 사용할 준비가 된 후에 관리자를 추가하고 앱 서비스 환경 내의 다른 앱을 모니터링할 수 있습니다.

1. [Azure 포털](https://portal.azure.com/)에서 **PowerApps**을 엽니다.
2. **설정**을 선택합니다.
3. **설정**에서 **관리**를 선택합니다. ![][1]  
4. **사용자**에서 **추가**를 선택합니다.
5. **소유자** 역할을 선택합니다. ![][2]  

	> [AZURE.IMPORTANT] 누군가를 PowerApps 관리자로 지정하는 경우 **소유자** 역할을 선택해야 합니다. 나열된 다른 역할은 PowerApps를 관리하는 전체 액세스 권한을 사용자에게 부여하지 않습니다.

6. 사용자 또는 그룹을 선택합니다.
7. **확인**을 선택하여 단계를 완료합니다.

PowerApps 엔터프라이즈에 관리자를 추가하면 관리자로 추가하는 사용자 및 그룹 관리자가 다음을 수행할 수 있습니다.

- 다른 사용자를 PowerApps 관리자 계정으로 추가합니다.
- 모든 앱뿐만 아니라 해당 사용자 액세스도 관리합니다.
- 청구는 변경할 수 없습니다.

> [AZURE.IMPORTANT] PowerApps 관리자는 앱 서비스 환경의 리소스 그룹에 대한 소유자 역할을 부여 받을 때까지 앱 서비스 환경을 변경할 수 없습니다. 이 작업을 수행하려면 [PowerApps 엔터프라이즈 시작](powerapps-get-started-azure-portal.md)을 참조하세요.

앱 서비스 환경의 리소스 그룹에 소유자 역할을 부여하면 PowerApps 관리자가 다음을 수행할 수도 있습니다.

- API 및 해당 연결을 만들고 구성합니다.
- 앱 서비스 환경을 포함하여 PowerApps 설정을 변경합니다.
- 다른 사용자 및 그룹을 추가하고 이들에게 API, 해당 연결 및 앱 서비스 환경에 대한 역할과 사용 권한을 부여합니다. 


## PowerApps 및 다른 유형의 앱 관리
PowerApps 및 앱 서비스 환경을 사용하도록 설정한 후 웹 앱 및 논리 앱 등의 다른 앱을 동일한 앱 서비스 환경에 추가할 수 있습니다. 이렇게 한 후 *모든 앱* 아래에 PowerApps에서 만든 앱과 함께 앱이 나열됩니다. 각 유형의 앱을 클릭하여 앱을 탐색할 수 있습니다.

### PowerApps 확인 및 관리

1. [Azure 포털](https://portal.azure.com/)에서 **PowerApps**을 엽니다.
2. **모든 앱** 타일에서 **PowerApps**을 선택합니다. ![][3]  
3. 다음을 포함하여 앱의 세부 정보를 확인할 앱을 선택합니다.  
	- 앱이 사용하는 API
	- 앱에 대한 액세스 권한이 있는 사용자 및 그룹 
	- 앱의 분석(출시 예정)

#### 앱 추가

Azure 포털을 통해 앱을 추가할 수 없습니다. 현재 [PowerApps 포털](http://go.microsoft.com/fwlink/p/?LinkId=715583)로 이동합니다.

#### PowerApps에서 만든 앱 삭제
PowerApps 관리자는 PowerApps 및 앱 서비스 환경의 다른 유형의 앱에서 만든 앱을 포함하여 어떤 앱이든 삭제할 수 있습니다. 앱을 삭제하려면 **모든 앱** 타일, 앱, **삭제**를 차례로 선택합니다. ![][4]


#### 사용자 또는 그룹에 앱을 사용하는 액세스 권한 부여
PowerApps 관리자는 PowerApps에 대해 사용자 및 그룹을 추가 또는 삭제할 수 있습니다.

1. [Azure 포털](https://portal.azure.com/)에서 **PowerApps**를 엽니다.
2. **모든 앱** 타일에서 **PowerApps**를 선택합니다. ![][3]  
3. **서비스 데스크**와 같은 앱을 선택합니다. 
4. **설정**에서 **앱 사용자 액세스**를 선택합니다. ![][5]  
5. **추가**를 선택하여 새 사용자 또는 그룹을 선택합니다. 
6. 역할 선택:  
	- 편집 가능
	- 확인 가능
7. 사용자 또는 그룹을 선택합니다.
8. **확인**을 선택하여 단계를 완료합니다.

### 논리 앱 확인 및 관리

1. [Azure 포털](https://portal.azure.com/)에서 **PowerApps**를 엽니다.
2. **모든 앱** 타일에서 **논리 앱**을 선택합니다. ![][8]  
3. 앱의 세부 정보를 확인할 논리 앱을 선택합니다. 올바른 논리 앱을 나열하려면 PowerApps에 대한 수정 구독을 선택해야 합니다. ![][7]  

	> [AZURE.IMPORTANT] 공개 미리 보기에서 탐색 블레이드의 논리 앱 개수와 주 PowerApps 블레이드에 표시된 개수에 일부 불일치가 발생할 수 있습니다. 예상된 동작입니다. 포털은 모든 호스팅 계획의 모든 앱을 표시하며 PowerApps에 대해 배포된 앱 서비스 환경 아래의 논리 앱을 필터링하지 않습니다. 이 동작은 이후 업데이트에서 수정될 예정입니다.

	**논리 앱 및 이들을 관리하는 방법에 대해 자세히 알아보려면 [이 지침](https://azure.microsoft.com/documentation/services/app-service/logic/)을 참조하세요.**

### 웹 앱 확인 및 관리

1. [Azure 포털](https://portal.azure.com/)에서 **PowerApps**를 엽니다.
2. **모든 앱** 타일에서 **웹 앱**을 선택합니다. ![][9]  

	**웹 앱 및 이들을 관리하는 방법에 대해 자세히 알아보려면 [이 지침](https://azure.microsoft.com/documentation/services/app-service/web/)을 참조하세요.**

### 모바일 앱 확인 및 관리

1. [Azure 포털](https://portal.azure.com/)에서 **PowerApps**를 엽니다.
2. **모든 앱** 타일에서 **모바일 앱**을 선택합니다. ![][10]  

	**모바일 앱 및 이들을 관리하는 방법에 대해 자세히 알아보려면 [이 지침](https://azure.microsoft.com/documentation/services/app-service/mobile/)을 참조하세요.**


## 보안 옵션 검토
수행하는 작업에 따라 서로 다른 보안 메서드가 사용됩니다. 알아야 할 사항:

- **구독 관리자**: 이 관리자는 청구를 제어하며 PowerApps 엔터프라이즈에 대해 회사를 등록하는 작업을 담당합니다. 구독 관리자만이 회사의 Azure 구독 내에서 PowerApps을 사용하도록 요청할 수 있습니다. 

- **런타임 사용자 액세스**: 세 가지 유형의 런타임 사용자 액세스가 있습니다.
	- **앱 사용자 액세스**: 이 사용 권한은 앱의 사용자가 앱을 *편집할 수 있는지* 또는 앱을 *확인할 수 있는지*를 제어합니다.
	- **API 사용자 액세스**: 이 사용 권한은 런타임 액세스를 제어합니다. 사용자가 이 사용 권한을 가지고 있으면 자신의 앱에 API를 사용할 수 있습니다. 사용자는 런타임에 API를 사용할 권한을 가지고 있거나 권한을 가지고 있지 않습니다. 
	- **연결 사용자 액세스**: *확인 가능* 및 *편집 가능*은 연결에 대해 사용할 수 있는 런타임 사용자 권한입니다. API(또는 연결 프로필)를 추가하고 해당 연결을 만들 때 사용자 및 그룹에 이 특정 권한을 부여합니다. ![][6]  

		예를 들어, 회사 내의 영업 그룹에 SQL 커넥터 API의 연결에 대한 *편집 가능* 권한을 부여할 수 있습니다. *편집 가능* 권한을 가진 사용자는 자신의 앱에 있는 연결을 사용하고 연결 구성을 편집할 수 있습니다. *확인 가능* 권한을 가진 사용자는 자신의 앱에 있는 연결을 사용할 수 있지만 연결 문자열 같은 연결 구성을 수정할 수 없습니다.

- **역할 기반 액세스 제어**(RBAC): 많은 Azure 제품은 역할 기반 액세스 제어를 사용하여 어떤 일을 할 수 있는 사용자를 결정합니다. PowerApps에서 RBAC는 두 위치에서 사용됩니다.
	- PowerApps 포털에 처음 들어갈 때 PowerApps의 관리자여야 하는 사용자를 추가 및 관리할 수 있습니다. 
	- 앱 서비스 환경을 만들 때 PowerApps에 사용자 또는 그룹을 추가하고 PowerApps에서 사용자 또는 그룹을 제거할 수 있습니다. 예를 들어, 회사 내의 특정 관리자 그룹을 *소유자* 역할에 추가할 수 있으며, 그러면 해당 그룹이 API 및 연결을 만들 수 있습니다. 이러한 API 및 연결은 PowerApps에서 만든 앱에 추가됩니다.
	- 웹 앱, 논리 앱, 모바일 앱 또는 논리 앱 같은 앱에 사용자를 추가하는 경우. 이러한 사용자에 대한 역할을 선택할 수 있습니다.  
		
		사용자 추가 및 역할 할당은 Azure 내에서 [역할 기반 액세스 제어](../role-based-access-control-configure.md)를 사용하는 것과 같습니다. 역할은 다음을 포함합니다.

		역할 | 설명
		--- | ---
		참여자 | 사용자에게 액세스 권한 부여를 제외하고 모든 작업을 관리합니다.
		판독기 | 모든 항목을 볼 수 있지만 변경할 수 없습니다.
		소유자 | 모든 작업을 관리하고 사용자 액세스 권한을 부여할 수 있습니다.

이러한 역할을 사용하여 사용자 A에게 Twitter 일일 앱에 대한 **확인 가능** 권한을 부여하고 사용자 B에게 ShuttleBus 앱에 대한 **편집 가능** 권한을 부여할 수 있습니다. 사용자 B에게 모든 API에 대한 액세스 권한을 부여할 수 있습니다. 실제로 이러한 권한을 세분화하거나 특정 역할을 가진 모든 사용자를 추가할 수 있습니다. 실제로 이는 비즈니스 요구에 따라 달라집니다.


## 요약 및 다음 단계
이 항목에서는 PowerApps을 관리하는 여러 가지 옵션 및 PowerApps 내에서 구현되는 보안 메서드에 대해 알아보았습니다.

이제 Azure 포털 환경을 구성했으므로 응용 프로그램을 만들기 시작해 보겠습니다. 좋은 시작점은 다음과 같습니다.

- [PowerApps의 템플릿에서 앱 만들기](http://go.microsoft.com/fwlink/p/?LinkId=715536) 
- [PowerApps의 데이터에서 앱 만들기](http://go.microsoft.com/fwlink/?LinkId=715539) 
- [PowerApps에서 처음부터 앱 만들기](http://go.microsoft.com/fwlink/p/?LinkId=715538)


[1]: ./media/powerapps-manage-monitor-usage/addadmin.png
[2]: ./media/powerapps-manage-monitor-usage/selectrole.png
[3]: ./media/powerapps-manage-monitor-usage/PowerApps.png
[4]: ./media/powerapps-manage-monitor-usage/deleteapp.png
[5]: ./media/powerapps-manage-monitor-usage/appuseraccess.png
[6]: ./media/powerapps-manage-monitor-usage/selectpermission.png
[7]: ./media/powerapps-manage-monitor-usage/alllogicapps.png
[8]: ./media/powerapps-manage-monitor-usage/logicapps.png
[9]: ./media/powerapps-manage-monitor-usage/webapps.png
[10]: ./media/powerapps-manage-monitor-usage/mobileapps.png

<!---HONumber=AcomDC_0309_2016-->