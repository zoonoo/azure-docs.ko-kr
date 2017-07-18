---
title: "외부 Azure App Service Environment 만들기"
description: "앱 또는 독립 실행형을 만드는 동안 Azure App Service Environment를 만드는 방법 설명"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 3f1418b71a7327264e29e3f08ef97b254ee9930d
ms.contentlocale: ko-kr
ms.lasthandoff: 06/26/2017

---
# <a name="create-an-external-app-service-environment"></a>외부 App Service 환경 만들기 #

ASE(App Service Environment)는 Azure App Service를 사용자의 Azure VNet(Virtual Network)의 서브넷에 배포한 것입니다. 두 가지 방법으로 ASE를 배포할 수 있습니다.

- _외부 ASE_라고도 하는 외부 IP 주소의 VIP 사용
- 내부 끝점이 ILB(내부 부하 분산 장치)이기 때문에 _ILB ASE_라고도 하는 내부 IP 주소의 VIP 사용

이 문서에서는 외부 ASE를 만드는 방법을 보여줍니다. ASE에 대한 개요는 [App Service 환경 소개][Intro]로 시작할 수 있습니다. ILB ASE를 만드는 방법에 대한 세부 정보는 [ILB ASE 만들기 및 사용][MakeILBASE]을 참조하세요.

## <a name="before-you-create-your-ase"></a>ASE를 만들기 전에 ##

ASE 생성 후에 변경할 수 없다는 점에 주의해야 합니다.

- 위치
- 구독
- 리소스 그룹
- 사용되는 Virtual Network
- 사용되는 서브넷
- 서브넷 크기

> [!NOTE]
> Virtual Network를 선택하고 서브넷을 지정하는 경우 향후 성장을 수용하기에 충분한지 확인합니다. 128개 주소를 사용하는 `/25`가 권장되는 크기입니다.
>

## <a name="three-ways-to-create-an-ase"></a>ASE를 만드는 세 가지 방법 ##

세 가지 방법으로 ASE를 만들 수 있습니다. 다음과 같이 ASE를 만들 수 있습니다.

- App Service 계획을 만들면서 ASE 및 App Service 계획을 한꺼번에 만듭니다.
- 독립 실행형 ASE 생성 UI에서 비어 있는 ASE를 만듭니다. 이 작업은 고급 ASE 생성 UI 환경이며 ILB(내부 부하 분산 장치)로 ASE를 만드는 경우입니다.
- Resource Manager 템플릿에서 만듭니다. 고급 사용자의 경우이며 [템플릿에서 ASE 만들기][MakeASEfromTemplate]에서 다룹니다.

ILB 없이 만든 ASE에는 공용 VIP가 있습니다. 즉, ASE에서 앱에 대한 모든 HTTP 트래픽은 인터넷 액세스 가능한 IP 주소를 사용합니다. ILB가 있는 ASE에는 Virtual Network IP 주소에 대한 끝점이 있습니다. 이러한 앱은 인터넷에 직접 노출되지 않습니다.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>ASE 및 App Service 계획 만들기 ##

App Service 계획은 앱의 컨테이너입니다. App Service에서 앱을 만들 때 항상 App Service 계획을 선택하거나 만들어야 합니다. 컨테이너 모델에서는 환경은 App Service 계획을 포함하고 App Service 계획은 앱을 포함합니다.

App Service 계획을 만드는 동안 ASE를 만들려면:

1. [Azure Portal](https://portal.azure.com/)에서 **새로 만들기 &gt; 웹 + 모바일 &gt; Web App**을 클릭합니다.

    ![][1]
1. 사용 중인 구독을 선택합니다. 여러 구독이 있는 경우 ASE에서 앱을 만들려면 ASE를 만드는 데 사용한 동일한 구독을 사용해야 한다는 점에 주의합니다.
1. 리소스 그룹을 선택하거나 만듭니다. *리소스 그룹*을 통해 관련 Azure 리소스를 하나의 단위로 관리할 수 있으며 앱에 대해 *역할 기반 액세스 제어*(RBAC) 규칙을 설정할 때 유용합니다. 자세한 내용은 [Azure Resource Manager 개요][ARMOverview]를 참조하세요.
1. App Service 계획을 클릭한 다음 **새로 만들기**를 선택합니다.

    ![][2]
1. **위치** 드롭다운에서 ASE를 만들려는 지역을 선택합니다. 기존 ASE을 선택하면 새 ASE 만들 수 없지만 선택한 ASE에 App Service 계획을 만들면 됩니다.
1. **가격 책정 계획** UI를 클릭하고 **격리** 가격 책정 SKU 중 하나를 선택합니다. **격리** SKU 카드 및 위치를 선택하더라도 해당 위치에 새 ASE를 만들지는 않습니다. 그러면 가격 책정 카드 페이지에서 **선택**을 클릭한 후에 ASE 생성 UI를 표시합니다. **격리** SKU는 ASE에서만 사용할 수 있습니다. 또한 ASE에서 **격리**가 아닌 다른 가격 책정 SKU를 사용할 수 없습니다.

    ![][3]
1. ASE의 이름을 입력합니다. ASE의 이름은 앱에 주소를 지정할 수 있는 이름에 사용됩니다. ASE 이름이 _appsvcenvdemo_인 경우 하위 도메인 이름은 *.appsvcenvdemo.p.azurewebsites.net*입니다. 따라서 *mytestapp*이라는 앱을 만든 경우 해당 주소는 *mytestapp.appsvcenvdemo.p.azurewebsites.net*으로 지정될 수 있습니다. ASE의 이름에 공백을 사용할 수 없습니다. 이름에 대문자를 사용한 경우 전체 도메인 이름은 해당 이름의 소문자 버전이 됩니다.

    ![][4]
1. **새로 만들기** 또는 **기존 항목 선택** 중 하나를 선택합니다. 기존 Virtual Network를 선택하는 옵션은 Virtual Network가 선택한 지역에 있는 경우에만 사용할 수 있습니다. **새로 만들기**를 선택하는 경우 Virtual Network의 이름을 입력하고 해당 이름 가진 새 Resource Manager Virtual Network를 선택한 지역의 주소 공간 `192.168.250.0/23`으로 생성합니다. **기존 항목 선택**을 선택하는 경우 다음 항목이 필요합니다.
    1. 여러 개의 항목이 있는 경우 Virtual Network 주소 블록을 선택합니다.
    2. 새 서브넷 이름을 입력합니다.
    3. 서브넷의 크기를 선택합니다. **미리 알림: ASE의 향후 증가에 맞게 충분히 큰 서브넷 크기를 설정해야 합니다.** 권장되는 크기는 `/25`입니다. 여기에는 128개의 주소가 있고 최대 크기의 ASE를 다룰 수 있습니다. 예를 들어 16개의 주소만을 사용할 수 있기 때문에 `/28`은 권장되지 않습니다. 인프라 요구 사항은 최소 5개의 주소를 사용하고 `/28` 서브넷에서 최대 11개의 인스턴스를 크기 조정하도록 합니다.
    4. 서브넷 IP 범위를 선택합니다.

**만들기**를 선택한 후에 ASE 생성 프로세스가 시작됩니다. App Service 계획 및 앱도 만들어집니다. ASE, App Service 계획 및 앱은 동일한 구독 및 동일한 리소스 그룹에 위치합니다. ASE를 App Service 계획 및 앱의 별도 리소스 그룹에 포함해야 하거나 ILB ASE가 필요한 경우 독립 실행형 ASE 생성 환경을 사용합니다.

## <a name="create-an-ase-by-itself"></a>단독으로 ASE 만들기 ##

독립 실행형 ASE 생성 흐름을 통해 비어 있는 ASE가 만들어집니다. 빈 ASE로 인해 인프라에 대한 월별 요금이 청구됩니다. ILB를 사용하여 ASE를 만들거나 고유한 리소스 그룹에서 ASE를 만들기 위해 이 워크플로를 수행합니다. ASE 생성 후에 일반적인 앱 생성 환경을 사용하고 새 ASE를 위치로 선택하여 ASE에 앱을 만들 수 있습니다.

***App Service 환경***의 Azure Marketplace에서 검색하거나, 새로 만들기 -&gt; 웹 모바일 -&gt; App Service 환경으로 이동하여 ASE 생성 UI에 액세스할 수 있습니다. 독립 실행형 생성 환경을 사용하여 ASE를 만들려면:

1. ASE의 이름을 제공합니다. 지정한 ASE 이름은 ASE에서 만드는 앱에 사용됩니다. ASE 이름이 *mynewdemoase*인 경우 하위 도메인 이름은 *.mynewdemoase.p.azurewebsites.net*입니다. 따라서 *mytestapp*이라는 앱을 만든 경우 해당 주소는 *mytestapp.mynewdemoase.p.azurewebsites.net*으로 지정될 수 있습니다. ASE의 이름에 공백을 사용할 수 없습니다. 이름에 대문자를 사용한 경우 전체 도메인 이름은 해당 이름의 소문자 버전이 됩니다. ILB를 사용하는 경우 ASE 이름이 하위 도메인에서 사용되지 않고 대신 ASE를 만드는 동안에 명시적으로 지정됩니다.

    ![][5]
1.  사용 중인 구독을 선택합니다. ASE에 사용되는 구독은 ASE에 사용되는 모든 앱을 생성하는 구독이기도 합니다. 다른 구독에 있는 Virtual Network에 ASE를 배치할 수 없습니다.
1.  새 리소스 그룹을 선택하거나 지정합니다. ASE에 사용되는 리소스 그룹은 Virtual Network에 사용되는 것과 동일해야 합니다. 기존 Virtual Network를 선택하는 경우 ASE에 대한 리소스 그룹을 선택하는 작업은 Virtual Network의 선택을 반영하도록 업데이트됩니다.

    ![][6]
1. 가상 네트워크 및 위치 선택을 확인합니다. 새 Virtual Network를 만들거나 기존 Virtual Network를 선택할 수 있습니다. 새 Virtual Network를 선택하면 이름 및 위치를 지정할 수 있습니다. 새 Virtual Network의 주소 범위는 192.168.250.0/23이고 192.168.250.0/24로 정의된 **default**라는 서브넷이 있습니다. Resource Manager Virtual Network만을 선택할 수 있습니다. **VIP 형식**은 ASE가 인터넷(외부)에서 직접 액세스할 수 있는지 또는 ILB(내부 부하 분산 장치)를 사용하는지를 결정합니다. 자세한 내용은 [App Service 환경에서 내부 부하 분산 장치 만들기 및 사용][MakeILBASE]을 참조하세요. **외부**의 VIP 형식을 선택하면 시스템이 IP 기반 SSL 목적으로 만들 수 있는 외부 IP 주소의 개수를 선택할 수 있습니다. **내부**를 선택하는 경우 ASE에서 사용할 하위 도메인을 지정해야 합니다. 공용 주소 범위 *또는* RFC1918 주소 공간(즉, 개인 주소) *중 하나*를 사용하는 가상 네트워크에 ASE를 배포할 수 있습니다. 공용 주소 범위를 가진 가상 네트워크를 사용하기 위해 사전에 Virtual Network를 만들어야 합니다. 기존 Virtual Network를 선택하면 ASE를 만드는 동안 새 서브넷을 만들어야 합니다. **포털에서 미리 생성된 서브넷을 사용할 수 없습니다. Resource Manager 템플릿을 사용하여 ASE를 만드는 경우 기존 서브넷으로 ASE를 만들 수 있습니다.** 템플릿에서 ASE를 만들려면 [템플릿에서 App Service 환경 만들기][MakeASEfromTemplate]를 참조하세요.

## <a name="app-service-environment-v1"></a>App Service 환경 v1 ##

여전히 첫 번째 버전의 ASE 기능(ASEv1) 인스턴스를 만들 수 있습니다. 해당 환경을 찾으려면 **App Service 환경 v1**의 경우 Marketplace에서 검색합니다. 생성 환경은 독립 실행형 ASE 생성 환경과 같습니다. 작업이 완료되면 ASEv1은 두 개의 프런트 엔드 및 두 명의 작업자를 사용하여 생성됩니다. ASEv1을 사용하면 프런트 엔드와 작업자를 관리해야 합니다. App Service 계획을 만들 때 자동으로 추가되지 않습니다. 프런트 엔드는 HTTP/HTTPS 끝점으로 작동하여 앱 호스트 역할을 갖는 작업자로 트래픽을 전송합니다. ASE 생성 후에 수량을 조정할 수 있습니다. ASEv1에 대해 자세히 알아보려면 [App Service Environment v1 소개][ASEv1Intro]를 참조하세요. ASEv1의 크기 조정, 관리 및 모니터링에 대한 자세한 내용은 [App Service 환경을 구성하는 방법][ConfigureASEv1]을 참조하세요.

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png



<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md

