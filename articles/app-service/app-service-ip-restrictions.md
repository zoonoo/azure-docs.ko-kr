---
title: Azure App Service-액세스 제한 | Microsoft Docs
description: Azure App Service를 사용 하 여 액세스 제한을 사용 하는 방법
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e408439c4868a9fadfd15ab8ae303b2d881c481e
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494291"
---
# <a name="azure-app-service-access-restrictions"></a>Azure App Service 액세스 제한 #

액세스 제한 사용 앱에 대 한 네트워크 액세스를 제어 하는 우선 순위 순서가 지정 된 허용/거부 목록을 정의할 수 있습니다. Azure Virtual Network 서브넷 또는 IP 주소 목록이 포함할 수 있습니다. 하나 이상의 항목의 경우는 다음 암시적인 "모두 거부" 목록 끝에 있는 합니다.

호스 티 드 작업 등을 로드 하는 모든 App Service를 사용 하 여 작동 액세스 제한 기능 web apps, API 앱, Linux 앱, Linux 컨테이너 앱 및 함수.

앱에는 요청이 수행 될 때 보낸 사람 주소 액세스 제한 목록에 IP 주소 규칙에 대해 평가 됩니다. 보낸 사람 주소 Microsoft.Web 서비스 끝점으로 구성 된 서브넷에 있으면 원본 서브넷에 대 한 액세스 제한 목록에서 가상 네트워크 규칙에 대해 비교 됩니다. 주소가 목록의 규칙을 기준으로 허용되지 않을 경우 서비스는 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 상태 코드로 답변합니다.

액세스 제한 기능이 업스트림 작업자 호스트 프로그램 코드가 실행 되는 App Service 프런트 엔드 역할에서 구현 됩니다. 따라서 액세스 제한을 효과적으로 네트워크 Acl 됩니다.

Azure Virtual Network (VNet)에서 웹 앱에 대 한 액세스를 제한 하는 기능 이라고 [서비스 끝점][serviceendpoints]합니다. 서비스 끝점을 사용 하 여 선택한 서브넷에서 다중 테 넌 트 서비스에 대 한 액세스를 제한할 수 있습니다. 네트워킹 측면 뿐만 아니라 사용 되는 서비스에서 사용할 수 있어야 합니다. App Service Environment에서 호스트 되는 앱에 트래픽을 제한 하는 작동 하지 않습니다.  App Service Environment에 있는 경우에 IP 주소 규칙을 사용 하 여 앱에 대 한 액세스를 제어할 수 있습니다.

![액세스 제한 흐름](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>추가 하 고 포털에서 액세스 제한 규칙 편집 ##

앱에 액세스 제한 규칙 추가 하려면 메뉴 사용 하 여 열려는 **네트워크**>**액세스 제한** 클릭 하 고 **액세스 제한 구성**

![App Service 네트워킹 옵션](media/app-service-ip-restrictions/access-restrictions.png)  

액세스 제한 사항 UI에서 앱에 대해 정의 된 액세스 제한 규칙 목록을 검토할 수 있습니다.

![목록 액세스 제한](media/app-service-ip-restrictions/access-restrictions-browse.png)

목록에는 모든 앱에 있는 현재 제한 사항이 표시 됩니다. 앱에서 VNet 제한이 있는 경우 테이블 서비스 끝점 Microsoft.Web 설정 된 경우 표시 됩니다. 앱에서 정의 된 제한이 없는 경우 앱 어디서 나 액세스할 수 됩니다.  

클릭할 수 있습니다 **[+] 추가** 새 액세스 제한 규칙을 추가 합니다. 규칙을 추가하면 즉시 적용됩니다. 규칙은 가장 작은 번호부터 우선적으로 적용됩니다. 단일 규칙을 추가해도 암시적 모두 거부가 적용됩니다.

### <a name="adding-ip-address-rules"></a>IP 주소 규칙 추가

![IP 액세스 제한 규칙 추가](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

규칙을 만들 때 허용/거부 및 규칙의 유형을 선택 해야 합니다. 또한 해야 하는 제한 및 우선 순위 값에 대 한 액세스를 제공 합니다.  필요에 따라 규칙에 이름 및 설명을 추가할 수 있습니다.  

규칙 기반 IP 주소를 설정 하려면, IPv4 또는 IPv6 유형을 선택 합니다. IP 주소 표기법은 IPv4 및 IPv6 주소 둘 다에 대해 CIDR 표기법으로 지정해야 합니다. 정확한 주소를 지정하려면 1.2.3.4/32와 같이 처음 4개의 8진수로 IP 주소를 나타내고 /32로 마스크를 나타낼 수 있습니다. 모든 주소에 대한 IPv4 CIDR 표기법은 0.0.0.0/0입니다. CIDR 표기법에 대한 자세한 내용은 [Classless Inter-domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)을 참조하세요. 

### <a name="service-endpoints"></a>서비스 엔드포인트

![VNet 대 한 액세스 제한 규칙 추가](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

선택한 서브넷에 대 한 액세스를 제한 하는 유형의 가상 네트워크를 선택 합니다. 아래에 구독, VNet 및 서브넷을 사용 하 여 액세스 허용 하거나 거부 하려면 선택 하는 일을 할 수 있습니다. 서비스 끝점은 아직 사용 되지 Microsoft.Web를 사용 하 여 선택한 서브넷에 대 한 확인란을 하면 보내지 않도록 요청 하지 않는 한를 활성화 자동으로 됩니다. 앱 되지만 하지 서브넷에서 사용 하려는 경우는 거의 관련이 여부 서브넷에서 서비스 끝점을 사용 하도록 설정 하려면 권한이 있는 경우 있습니다. 서브넷에서 서비스 끝점을 사용 하도록 설정 하려면 다른 사용자를 가져오려는 경우에 확인란을 선택 하 고 나중에 서브넷에서 사용 하도록 설정 되 고이에 대비 하 여 서비스 끝점에 대해 구성 된 앱 수 있습니다. 

App Service Environment에서 실행 되는 앱에 대 한 액세스를 제한 하려면 서비스 끝점을 사용할 수 없습니다. App Service Environment에서 앱이 있는 경우 IP 액세스 규칙을 사용 하 여 앱에 대 한 액세스를 제어할 수 있습니다. 

서비스 끝점을 사용 하 여 Application Gateway 또는 기타 WAF 장치를 사용 하 여 앱을 구성할 수 있습니다. 또한 보안 백 엔드를 사용 하 여 다중 계층 응용 프로그램을 구성할 수 있습니다. 몇 가지에 대 한 자세한 내용은 참조 하세요 [네트워킹 기능 및 App Service](networking-features.md)합니다.

### <a name="managing-access-restriction-rules"></a>관리 액세스 제한 규칙

기존 액세스 제한 규칙을 편집 하려면 모든 행을 클릭할 수 있습니다. 우선 순위 변경을 비롯한 편집 내용은 즉시 적용됩니다.

![액세스 제한 규칙 편집](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

규칙을 편집 하는 경우에 IP 주소 규칙 사이의 가상 네트워크 규칙 유형을 변경할 수 없습니다. 

![액세스 제한 규칙 편집](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

규칙을 삭제하려면 규칙에서 **...** 를 클릭하고 **제거**를 클릭합니다.

![액세스 제한 규칙 삭제](media/app-service-ip-restrictions/access-restrictions-delete.png)

### <a name="blocking-a-single-ip-address"></a>단일 IP 주소를 차단합니다. ##

첫 번째 IP 제한 규칙 추가, 서비스 명시적인 추가 됩니다 **모두 거부** 2147483647의 우선 순위를 사용 하 여 규칙입니다. 실제로 명시적인 **모두 거부** 규칙 마지막 규칙이 실행 되 고 사용 하 여 명시적으로 허용 되지 않은 모든 IP 주소에 대 한 액세스를 차단 합니다는 **허용** 규칙입니다.

사용자가 있는 단일 IP 주소 또는 IP 주소 블록을 명시적으로 차단 하려는 시나리오의 경우 하나는 허용 모든 다른 액세스 해야 하는 명시적인 추가 **모든 허용** 규칙입니다.

![단일 ip 주소 블록](media/app-service-ip-restrictions/block-single-address.png)

### <a name="scm-site"></a>SCM 사이트 

앱에 대 한 액세스를 제어할 수 있을 뿐만 앱에서 사용 되는 scm 사이트에 액세스를 제한할 수도 있습니다. Scm 사이트는 웹 끝점 및 Kudu 콘솔을 배포 합니다. 별도로 앱에서 액세스 제한 scm 사이트에 할당 하거나 동일한 앱과 scm 사이트에 대 한 집합을 사용 하 여 수 있습니다. 앱과 동일한 제한 하도록 상자를 선택 하는 경우 모든 항목은 사라짐. 확인란의 선택을 취소 하는 경우에 scm 사이트에 앞서 사용 했던 모든 설정이 적용 됩니다. 

![목록 액세스 제한](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>액세스 제한 규칙의 프로그래밍 방식으로 조작 ##

현재 CLI 또는 PowerShell 없습니다 새 액세스 제한 기능이 있지만 Resource Manager에서 앱 구성에서 PUT 작업을 사용 하 여 값을 수동으로 설정할 수 있습니다. 예를 들어, resources.azure.com을 사용하고, ipSecurityRestrictions 블록을 편집하여 필요한 JSON을 추가할 수 있습니다.

Resource Manager에서 이 정보는 다음 위치에 제공됩니다.

management.azure.com/subscriptions/**구독 ID**/resourceGroups/**리소스 그룹**/providers/Microsoft.Web/sites/**웹앱 이름**/config/web?api-version=2018-02-01

앞의 예제에 대한 JSON 구문은 다음과 같습니다.

    "ipSecurityRestrictions": [
      {
        "ipAddress": "131.107.159.0/24",
        "action": "Allow",
        "tag": "Default",
        "priority": 100,
        "name": "allowed access"
      }
    ],

## <a name="function-app-ip-restrictions"></a>함수 앱 IP 제한

IP 제한은 동일한 App Service 계획 기능을 사용 하 여 함수 앱 모두에 대해 사용할 수 있습니다. IP 제한을 사용 하도록 설정 하면 허용 되지 않는 모든 Ip에 대 한 포털 코드 편집기 사용 되지 것입니다.

[여기를 참조하세요](../azure-functions/functions-networking-options.md#inbound-ip-restrictions).


<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
