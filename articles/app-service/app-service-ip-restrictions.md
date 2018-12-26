---
title: Azure App Service IP 제한 | Microsoft Docs
description: Azure App Service에 IP 제한을 사용하는 방법
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
ms.date: 7/30/2018
ms.author: ccompy
ms.openlocfilehash: 9ffeb01a46b62202292f6e1ea91f47cd87954387
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219131"
---
# <a name="azure-app-service-static-ip-restrictions"></a>Azure App Service 고정 IP 제한 #

IP 제한을 사용하여 앱 액세스가 허용되는 IP 주소의 허용/거부 목록을 우선 순위대로 정의할 수 있습니다. 허용 목록에는 IPv4 및 IPv6 주소가 포함될 수 있습니다. 하나 이상의 항목이 있는 경우 목록 끝에 암시적 모두 거부가 표시됩니다. 

IP 제한 기능은 웹앱 API 앱, Linux 앱, Linux 컨테이너 앱 및 함수를 포함하는 모든 App Service 호스티드 워크로드에 작동합니다. 

앱에 대한 요청이 수행될 경우 FROM IT 주소가 IP 제한 목록에 속하는지 평가됩니다. 주소가 목록의 규칙을 기준으로 허용되지 않을 경우 서비스는 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 상태 코드로 답변합니다.

IP 제한 기능은 코드가 실행되는 작업자 역할의 업스트림에 해당하는 App Service 프런트 엔드 역할에 구현됩니다. 따라서 IP 제한은 사실상 네트워크 ACL입니다.  

![IP 제한 흐름](media/app-service-ip-restrictions/ip-restrictions-flow.png)

이전에 포털의 IP 제한 기능은 IIS의 ipSecurity 기능 맨 위에 있는 계층이었습니다. 현재 IP 제한 기능은 다릅니다. ipSecurity를 응용 프로그램 web.config 내에서 구성할 수 있지만 트래픽이 IIS에 도달하기 전에 프런트 엔드 기반 IP 제한 규칙이 적용됩니다.

## <a name="adding-and-editing-ip-restriction-rules-in-the-portal"></a>포털에서 IP 제한 규칙 추가 및 편집 ##

앱에 IP 제한 규칙을 추가하려면 메뉴를 사용하여 **네트워크**>**IP 제한**을 열고 **IP 제한 구성**을 클릭합니다.

![App Service 네트워킹 옵션](media/app-service-ip-restrictions/ip-restrictions.png)  

IP 제한 UI에서 앱에 대해 정의된 IP 제한 규칙 목록을 검토할 수 있습니다.

![IP 제한 나열](media/app-service-ip-restrictions/ip-restrictions-browse.png)

규칙이 이 이미지와 같이 구성된 경우 앱은 131.107.159.0/24의 트래픽만 수락하며, 다른 IP 주소의 트래픽은 거부됩니다.

**[+] 추가**를 클릭하여 새 IP 제한 규칙을 추가할 수 있습니다. 규칙을 추가하면 즉시 적용됩니다. 규칙은 가장 작은 번호부터 우선적으로 적용됩니다. 단일 규칙을 추가해도 암시적 모두 거부가 적용됩니다. 

![IP 제한 규칙 추가](media/app-service-ip-restrictions/ip-restrictions-add.png)

IP 주소 표기법은 IPv4 및 IPv6 주소 둘 다에 대해 CIDR 표기법으로 지정해야 합니다. 정확한 주소를 지정하려면 1.2.3.4/32와 같이 처음 4개의 8진수로 IP 주소를 나타내고 /32로 마스크를 나타낼 수 있습니다. 모든 주소에 대한 IPv4 CIDR 표기법은 0.0.0.0/0입니다. CIDR 표기법에 대한 자세한 내용은 [Classless Inter-domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)을 참조하세요.  

기존 IP 제한 규칙을 편집하려면 아무 행이나 클릭할 수 있습니다. 우선 순위 변경을 비롯한 편집 내용은 즉시 적용됩니다.

![IP 제한 규칙 편집](media/app-service-ip-restrictions/ip-restrictions-edit.png)

규칙을 삭제하려면 규칙에서 **...** 를 클릭하고 **제거**를 클릭합니다. 

![IP 제한 규칙 삭제](media/app-service-ip-restrictions/ip-restrictions-delete.png)

## <a name="programmatic-manipulation-of-ip-restriction-rules"></a>IP 제한 규칙의 프로그래밍 방식 조작 ##

현재 새 IP 제한 기능에 대한 CLI 또는 PowerShell이 없지만 Resource Manager에서 앱 구성의 PUT 작업을 사용하여 값을 수동으로 설정할 수 있습니다. 예를 들어, resources.azure.com을 사용하고, ipSecurityRestrictions 블록을 편집하여 필요한 JSON을 추가할 수 있습니다. 

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
