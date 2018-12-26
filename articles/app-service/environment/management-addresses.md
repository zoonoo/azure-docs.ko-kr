---
title: Azure App Service Environment 관리 주소
description: App Service Environment를 명령하는 데 사용되는 관리 주소를 나열합니다.
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: ccompy
ms.openlocfilehash: 590679daff20f9c469fb8fcfcc0fbbad77f91b5b
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162857"
---
# <a name="app-service-environment-management-addresses"></a>App Service Environment 관리 주소

ASE(App Service Environment)는 Azure App Service를 사용자의 Azure VNet(Virtual Network)의 서브넷에 배포한 것입니다.  ASE는 Azure App Service에 사용된 관리 평면에서 액세스할 수 있어야 합니다.  이 ASE 관리 트래픽은 사용자 제어 네트워크를 통과합니다. 이 트래픽이 차단되거나 잘못 라우팅된 경우 ASE는 일시 중단 됩니다. ASE 네트워킹 종속성에 대한 자세한 내용은 [네트워킹 고려 사항과 App Service Environment][networking]를 참조하세요. 시작하는 데 도움이 되는 ASE에 대한 일반적인 정보는 [App Service Environment 소개][intro]를 참조하세요.

이 문서에서는 ASE에 대한 관리 트래픽의 App Service 원본 주소를 나열하고 두 가지 중요한 목적을 제공합니다.  

1. 이 주소를 사용하여 들어오는 트래픽을 잠글 네트워크 보안 그룹을 만들 수 있습니다.  
2. 강제 터널 배포를 지원하기 위해 이러한 주소로 경로를 만들 수 있습니다. ASE를 아웃바운드 트래픽이 온-프레미스에서 전송되는 환경에서 작동하도록 구성하는 방법에 대한 내용은 [강제 터널링을 사용하여 ASE 구성][forcedtunnel]을 참조하세요.

모든 ASE에는 관리 트래픽이 전달되는 공개 VIP가 있습니다. 이러한 주소에서 들어오는 관리 트래픽은 ASE의 공개 VIP에서 포트 454 및 455로 전달됩니다.  

## <a name="list-of-management-addresses"></a>관리 주소 목록 ##

| 지역 | 주소 |
|--------|-----------|
| 모든 공용 지역 | 70.37.57.58, 157.55.208.185, 52.174.22.21, 13.94.149.179, 13.94.143.126, 13.94.141.115, 52.178.195.197, 52.178.190.65, 52.178.184.149, 52.178.177.147, 13.75.127.117, 40.83.125.161, 40.83.121.56, 40.83.120.64, 52.187.56.50, 52.187.63.37, 52.187.59.251, 52.187.63.19, 52.165.158.140, 52.165.152.214, 52.165.154.193, 52.165.153.122, 104.44.129.255, 104.44.134.255, 104.44.129.243, 104.44.129.141, 23.102.188.65, 191.236.154.88, 13.64.115.203, 65.52.193.203, 70.37.89.222, 52.224.105.172, 23.102.135.246 |
| Microsoft Azure Government(Fairfax 또는 MAG) | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="get-your-management-addresses-from-api"></a>API에서 관리 주소 가져오기 ##

다음 API 호출을 사용하여 ASE와 일치하는 관리 주소를 나열할 수 있습니다.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

이 API는 ASE에 대한 모든 인바운드 주소를 포함하는 JSON 문서를 반환합니다. 주소 목록에는 관리 주소, 즉, ASE 및 ASE 서브넷 주소 범위 자체에서 사용하는 VIP가 포함됩니다.  

[armclient](http://github.com/projectkudu/ARMClient)를 사용하여 API를 호출하려면 다음 명령을 사용하되 구독 ID, 리소스 그룹 및 ASE 이름을 바꿔야 합니다.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
