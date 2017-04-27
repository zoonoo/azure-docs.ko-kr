---
title: "고객 Virtual Network에 배포되는 Azure RemoteApp용 허용 목록에 대한 포트 및 URL 목록 | Microsoft Docs"
description: "Azure RemoteApp을 통해 통신하기 위해 구성해야 하는 포트 및 URL을 알아봅니다."
services: remoteapp
documentationcenter: 
author: mghosh1616
manager: mbaldwin
ms.assetid: 5a001ff7-14c9-47fa-9b39-78fd5a5f0250
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 9390af174262e0dd2bb5beb30ae08b3063c1a5e6
ms.lasthandoff: 03/31/2017


---
# <a name="list-of-ports-and-urls-to-permit-access-for-azure-remoteapp-deployed-in-customer-virtual-network"></a>고객 가상 네트워크에 배포된 Azure RemoteApp에 대한 액세스를 허용하는 포트 및 URL 목록입니다.
> [!IMPORTANT]
> Azure RemoteApp은 2017년 8월 31일에 중단되었습니다. 자세한 내용은 [알림](https://go.microsoft.com/fwlink/?linkid=821148) 을 읽어보세요.
> 
> 

Azure RemoteApp 클라우드나 하이브리드 컬렉션을 VNET(Virtual Network)에 배포하는 경우 다음의 포트 정보를 검토하세요. Virtual Network에 대한 자세한 내용은 [Virtual Network 개요](../virtual-network/virtual-networks-overview.md)를 참조하세요. 컬렉션에 트래픽을 Virtual Network 리소스로 제한하는 NSG(네트워크 보안 그룹)를 만든 경우 다음 포트에 액세스할 수 있는지와 Virtual Network의 보안 정책을 통해 허용되는지 확인하세요. 네트워크 보안 그룹에 대한 자세한 내용은 다음을 참조하세요. [네트워크 보안 그룹이란? (NSG)](../virtual-network/virtual-networks-nsg.md).

## <a name="azure-remoteapp-subnet-needs-access-to-these-endpoints-and-urls"></a>Azure RemoteApp 서브넷에 다음 끝점 및 URL에 대한 액세스 권한 필요:
* *.servicebus.windows.net
* *. servicebus.net
* https://*.remoteapp.windowsazure.com  
* https://www.remoteapp.windowsazure.com 
* https://*remoteapp.windowsazure.com  
* https://*.core.windows.net  
* 아웃바운드: TCP: TCP: 443, 9351, 9352, 10101-10175 
* 선택 사항 - UDP: 10201-10275  

## <a name="azure-remoteapp-clients-need-access-to-these-endpoints-and-urls"></a>Azure RemoteApp 클라이언트에 다음 끝점 및 URL에 대한 액세스 권한 필요:
데스크톱, 장치 등의 클라이언트로 Azure RemoteApp 컬렉션에서 배포된 앱에 연결합니다.

* https://telemetry.remoteapp.windowsazure.com  
* https://*.remoteapp.windowsazure.com(선택적 UDP 포트는 이 주소를 위한 것임) 
* https://login.windows.net  
* https://login.microsoftonline.com  
* https://www.remoteapp.windowsazure.com 
* https://*.core.windows.net  
* 아웃바운드: TCP: 443  
* 선택 사항 - UDP: 3391 


