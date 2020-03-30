---
title: 클라우드에 의한 Azure VMware 솔루션단순 - ExpressRoute를 사용하여 온-프레미스 연결
description: CloudSimple 지역 네트워크에서 ExpressRoute를 사용하여 온-프레미스 연결을 요청하는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0dd5ede110255b6e53bbc397e683e66b3beffc65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019624"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>익스프레스루트를 사용하여 온-프레미스에서 클라우드로 연결

외부 위치(예: 온-프레미스)에서 Azure에 대한 Azure ExpressRoute 연결이 이미 있는 경우 CloudSimple 환경에 연결할 수 있습니다. 두 개의 ExpressRoute 회로가 서로 연결할 수 있는 Azure 기능을 통해 연결할 수 있습니다. 이 메서드는 두 환경 간의 안전한 개인, 높은 대역폭, 낮은 대기 시간 연결을 설정 합니다.

[![온-프레미스 익스프레스루트 연결 - 글로벌 도달 범위](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>시작하기 전에

온-프레미스에서 전역 리치 연결을 설정하려면 **/29** 네트워크 주소 블록이 필요합니다.  /29 주소 공간은 ExpressRoute 회로 간의 전송 네트워크에 사용됩니다.  전송 네트워크는 Azure 가상 네트워크, 온-프레미스 네트워크 또는 CloudSimple 프라이빗 클라우드 네트워크와 겹치지 않아야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 회로와 CloudSimple 프라이빗 클라우드 네트워크 간의 연결을 설정하려면 Azure ExpressRoute 회로가 필요합니다.
* 사용자는 ExpressRoute 회로에서 권한 부여 키를 만들 수 있는 권한이 필요합니다.

## <a name="scenarios"></a>시나리오

온-프레미스 네트워크를 프라이빗 클라우드 네트워크에 연결하면 다음과 같은 시나리오를 포함하여 다양한 방법으로 프라이빗 클라우드를 사용할 수 있습니다.

* 사이트 간 VPN 연결을 만들지 않고 프라이빗 클라우드 네트워크에 액세스할 수 있습니다.
* 온-프레미스 Active Directory를 프라이빗 클라우드의 ID 원본으로 사용합니다.
* 온-프레미스에서 실행되는 가상 시스템을 프라이빗 클라우드로 마이그레이션합니다.
* 프라이빗 클라우드를 재해 복구 솔루션의 일부로 사용합니다.
* 프라이빗 클라우드 워크로드 VM에서 온-프레미스 리소스를 사용합니다.

## <a name="connecting-expressroute-circuits"></a>익스프레스루트 회로 연결

익스프레스루트 연결을 설정하려면 ExpressRoute 회로에서 권한 부여를 만들고 CloudSimple에 권한 부여 정보를 제공해야 합니다.


### <a name="create-expressroute-authorization"></a>익스프레스루트 권한 부여 만들기

1. Azure Portal에 로그인합니다.

2. 상단 검색 표시줄에서 **ExpressRoute 회로를** 검색하고 **서비스**에서 **ExpressRoute 회로를 클릭합니다.**
    [![익스프레스루트 회로](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. CloudSimple 네트워크에 연결하려는 ExpressRoute 회로를 선택합니다.

4. ExpressRoute 페이지에서 권한 **부여를**클릭하고 권한 부여의 이름을 입력하고 **저장을**클릭합니다.
    [![익스프레스루트 회로 승인](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. 복사 아이콘을 클릭하여 리소스 ID 및 권한 부여 키를 복사합니다. ID와 키를 텍스트 파일에 붙여넣습니다.
    [![익스프레스루트 회로 승인 사본](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **리소스 ID는** UI에서 복사해야 하며 지원하도록 ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` 제공할 때 형식이어야 합니다.

6. 만들 연결을 <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">지원하기</a> 위해 티켓을 제출합니다.
    * 문제 유형: **기술**
    * 구독: **CloudSimple 서비스가 배포되는 구독**
    * 서비스: **클라우드에 의한 VMware 솔루션단순**
    * 문제 유형: **서비스 요청**
    * 문제 하위 유형: **온-프레미스에 ExpressRoute 연결 만들기**
    * 세부 정보 창에 복사하여 저장한 리소스 ID 및 권한 부여 키를 제공합니다.
    * 전송 네트워크에 대한 /29 네트워크 주소 공간을 제공합니다.
    * ExpressRoute를 통해 기본 경로를 보내고 있습니까?
    * 프라이빗 클라우드 트래픽이 ExpressRoute를 통해 전송되는 기본 경로를 사용해야 합니까?

    > [!IMPORTANT]
    > 기본 경로를 전송하면 온-프레미스 인터넷 연결을 사용하여 프라이빗 클라우드에서 모든 인터넷 트래픽을 보낼 수 있습니다.  프라이빗 클라우드에서 구성된 기본 경로를 사용하지 않도록 설정하고 온-프레미스 연결 기본 경로를 사용하려면 지원 티켓에 세부 정보를 제공합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 네트워크 연결에 대해 자세히 알아보기](cloudsimple-azure-network-connection.md)  
