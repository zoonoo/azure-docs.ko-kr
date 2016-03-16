<properties
	pageTitle="앱 서비스 환경에서 웹앱 만들기"
	description="앱 서비스 환경에서 웹앱 및 앱 서비스 계획을 만드는 방법을 알아봅니다."
	services="app-service"
	documentationCenter=""
	authors="ccompy"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="01/14/2016"
	ms.author="ccompy"/>

# 앱 서비스 환경에서 웹앱 만들기

## 개요

이 자습서는 [앱 서비스 환경](app-service-app-service-environment-intro.md)(ASE)에서 웹앱 및 앱 서비스 계획을 만드는 방법을 보여줍니다.

> [AZURE.NOTE] 웹앱을 만드는 방법을 알아보지만 앱 서비스 환경이 아니어도 된다면 [.NET 웹앱 만들기](web-sites-dotnet-get-started.md) 또는 기타 언어와 프레임워크에 관련된 자습서 중 하나를 참조하세요.

## 필수 조건

이 자습서는 앱 서비스 환경을 만든 적이 있는 개발자를 대상으로 합니다. 만들어 본 적이 없는 경우 [앱 서비스 환경 만들기](app-service-web-how-to-create-an-app-service-environment.md)를 참조하세요.

## 웹앱 만들기

1. [Azure 포털](https://portal.azure.com/)에서 **새로 만들기 > 웹 + 모바일 > 웹앱**을 클릭합니다. 

	![][1]

2. 사용 중인 구독을 선택합니다.

	여러 구독이 있는 경우 앱 서비스 환경의 앱을 만들려면 환경을 만드는 데 사용한 동일한 구독을 사용해야 한다는 점에 주의합니다.

3. 리소스 그룹을 선택하거나 만듭니다.

	*리소스 그룹*은 관련된 Azure 리소스를 하나의 단위로 관리할 수 있도록 하 고 앱에 대한 *역할 기반 액세스 제어*(RBAC) 규칙을 설정할 때 유용합니다. 자세한 내용은 [Azure 리소스 관리][ResourceGroups]를 참조하세요.

4. 앱 서비스 계획을 선택하거나 만듭니다.

	*앱 서비스 계획*은 관리되는 웹앱 집합입니다. 가격 책정을 선택하면 개별 앱이 아니라 앱 서비스 계획에 청구되는 가격이 적용됩니다. 웹앱의 인스턴스 수를 늘리려면 앱 서비스 계획 인스턴스를 늘립니다. 그러면 해당 계획의 모든 웹앱에 적용됩니다. 사이트 슬롯 또는 VNET 통합과 같은 일부 기능에는 계획 내 수량 제한도 있습니다. 자세한 내용은 [Azure 앱 서비스 계획 개요](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)를 참조하세요.

	계획 이름에서 설명한 위치를 확인하여 ASE의 앱 서비스 계획을 식별할 수 있습니다.

	![][5]

	앱 서비스 환경에 이미 존재하는 앱 서비스 계획을 사용하려는 경우 해당 계획을 선택합니다. 새로운 앱 서비스 계획을 만들려는 경우 이 자습서의 다음 섹션인 [앱 서비스 환경에서 앱 서비스 계획 만들기](#createplan)를 참조하세요.

5. 웹앱에 이름을 입력하고 **만들기**를 클릭합니다.

	웹앱 이름은 Azure 앱 서비스에서 고유해야 합니다. 즉, "thisismywebapp"이라는 웹앱을 만든 경우 현재 Azure 앱 서비스에 "thisismywebapp"이라는 다른 웹앱이 있을 수 없습니다.

	ASE에 있는 웹앱의 URL은 다음과 같습니다.

	[*sitename*].[*앱 서비스 환경의 이름*].p.azurewebsites.net

	다음 위치 대신

	[*sitename*].azurewebsites.net

## <a name="createplan"></a> 앱 서비스 계획 만들기

앱 서비스 환경에서 앱 서비스 계획을 만들 때 ASE에 공유 작업자가 없기 때문에 작업자 선택이 다릅니다. 사용해야 하는 작업자는 관리자에 의해 ASE에 할당된 작업자입니다. 즉, 새 계획을 만들려면 ASE 작업자 풀에 할당된 수가 작업자 풀의 모든 계획에서 전체 인스턴스 수보다 많아야 합니다. ASE 작업자 풀의 작업자 수가 부족하여 계획을 만들 수 없는 경우 ASE 관리자와 함께 추가해야 합니다.

앱 서비스 환경에서 호스팅되는 앱 서비스 계획의 또 다른 차이점은 가격 책정을 선택할 수 없다는 점입니다. 앱 서비스 환경이 있는 경우 시스템에서 사용되는 계산 리소스에 대한 비용을 지불하며 해당 환경의 계획에 대한 추가 요금은 없습니다. 일반적으로 앱 서비스 계획을 만들 때 청구를 결정하는 가격 책정 계획을 선택합니다. 앱 서비스 환경은 기본적으로 콘텐츠를 만들 수 있는 개인 위치입니다. 따라서 콘텐츠 호스트 비용이 아니라 환경에 대한 비용을 지불합니다.

다음 지침에는 자습서의 이전 섹션에서 설명한 대로 웹앱을 만드는 동안 앱 서비스 계획을 만드는 방법을 보여줍니다.

1. 계획 선택 UI에서 **새로 만들기**를 클릭하고 ASE 외부에서 일반적으로 하는 대로 계획에 이름을 제공합니다.

2. 위치 선택에서 사용하려는 ASE를 선택합니다.

	앱 서비스 환경이 기본적으로 개인 배포 위치이기 때문에 위치에 표시됩니다.

	![][2]

	위치 선택에서 ASE를 선택한 후에 앱 서비스 계획 생성 UI를 업데이트합니다. 위치는 ASE 시스템의 이름 및 지역을 표시하고 가격 책정 계획 선택은 작업자 풀 선택으로 대체됩니다.

	![][3]

### 작업자 풀 선택

일반적으로 Azure 앱 서비스 내부와 앱 서비스 환경 외부에는 전용 가격 계획을 선택할 때 사용할 수 있는 3가지 계산 크기가 있습니다. 마찬가지로 ASE의 경우 최대 3개의 작업자 풀을 정의하고 해당 작업자 풀에 사용되는 계산 크기를 지정할 수 있습니다. ASE의 테넌트와 관련하여 앱 서비스 계획에 대해 가격 책정 계획을 계산 크기로 선택하는 대신 *작업자 풀*이라는 것을 선택합니다.

작업자 풀 선택 UI에는 작업자 풀 이름 아래에 해당 작업자 풀에 사용되는 계산 크기가 표시됩니다. 사용 가능한 양은 해당 풀에서 사용할 수 있는 계산 인스턴스 수를 나타냅니다. 실제로 전체 풀에는 이보다 많은 인스턴스가 있을 수 있지만 이 값은 현재 사용 중이지 않은 개수를 나타냅니다. 앱 서비스 환경을 조정하여 계산 리소스를 추가해야 하는 경우 [앱 서비스 환경 구성](app-service-web-configure-an-app-service-environment.md)을 참조하세요.

![][4]

이 예제에서는 사용 가능한 작업자 풀이 두 개뿐인 것을 확인합니다. 이는 ASE 관리자가 두 개의 작업자 풀에만 호스트를 할당했기 때문입니다. 세 번째 풀은 할당된 VM이 있으면 표시됩니다.

## 웹앱을 만든 후

ASE에서 웹앱을 실행하고 앱 서비스 계획을 관리하기 위해 고려해야 하는 몇 가지 사항이 있습니다.

앞서 설명한 것처럼 ASE 소유자는 시스템의 크기를 관리하므로 원하는 앱 서비스 계획을 호스트할 충분한 용량이 있는지 확인해야 합니다. 사용 가능한 작업자가 없으면 앱 서비스 계획을 만들 수 없습니다. 또한 웹앱을 확장할 수도 없습니다. 추가 인스턴스가 필요한 경우 앱 서비스 관리자에게 작업자를 추가하도록 요청해야 합니다.

웹앱과 앱 서비스 계획을 만든 후 이를 확장하는 것이 좋습니다. ASE에는 앱에 대한 내결함성을 제공하기 위해 항상 2개 이상의 앱 서비스 계획 인스턴스가 있어야 합니다. ASE에서 앱 서비스 계획의 크기 조정은 앱 서비스 계획 UI 통한 정상적인 조정과 같습니다. 크기 조정에 대한 자세한 내용은 [앱 서비스 환경에서 웹앱 크기 조정하는 방법](app-service-web-scale-a-web-app-in-an-app-service-environment.md)을 참조하세요.

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
[ResourceGroups]: http://azure.microsoft.com/documentation/articles/resource-group-portal/
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/

<!---HONumber=AcomDC_0302_2016-->