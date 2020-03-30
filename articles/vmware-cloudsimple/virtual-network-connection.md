---
title: 익스프레스루트를 사용하여 Azure 가상 네트워크를 클라우드단순에 연결 - 클라우드간이별 Azure VMware 솔루션
description: Azure 가상 네트워크와 CloudSimple 환경 간의 연결에 대한 피어링 정보를 가져오는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6b20ee4e04a4443529ecceca8c6fc2206f7df39d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77563983"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>익스프레스루트를 사용하여 Azure 가상 네트워크를 클라우드단순에 연결

프라이빗 클라우드 네트워크를 Azure 가상 네트워크 및 Azure 리소스로 확장할 수 있습니다. ExpressRoute 연결을 사용하면 프라이빗 클라우드에서 Azure 구독에서 실행 중인 리소스에 액세스할 수 있습니다.

## <a name="request-authorization-key"></a>권한 부여 키 요청

프라이빗 클라우드와 Azure 가상 네트워크 간의 ExpressRoute 연결에는 권한 부여 키가 필요합니다. 키를 얻으려면 <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">지원</a>으로 티켓을 제출합니다.  요청에서 다음 정보를 사용합니다.

* 문제 유형: **기술**
* 구독: **CloudSimple 서비스가 배포되는 구독을 선택합니다.**
* 서비스: **클라우드에 의한 VMware 솔루션단순**
* 문제 유형: **서비스 요청**
* 문제 하위 유형: **Azure VNET 연결에 대한 권한 부여 키**
* 제목: **Azure VNET 연결에 대한 권한 부여 키 요청**

## <a name="get-peering-information-from-cloudsimple-portal"></a>CloudSimple 포털에서 피어링 정보 받기

연결을 설정하려면 Azure 가상 네트워크와 CloudSimple 환경 간의 연결을 설정해야 합니다.  절차의 일부로 피어 회로 URI 및 권한 부여 키를 제공해야 합니다. [CloudSimple 포털에서](access-cloudsimple-portal.md)URI 및 권한 부여 키를 가져옵니다.  사이드 메뉴에서 **네트워크를** 선택한 다음 **Azure 네트워크 연결을**선택합니다. 또는 사이드 메뉴에서 **계정을** 선택한 다음 **Azure 네트워크 연결을**선택합니다.

피어 회로 URI및 복사 아이콘을 사용하여 각 지역의 권한 부여 키를 *복사합니다.* 연결하려는 각 CloudSimple 지역에 대해:

1. URI를 복사하려면 **복사를** 클릭합니다. Azure 포털에 추가할 수 있는 파일에 붙여넣습니다.  
2. **복사를** 클릭하여 권한 부여 키를 복사하고 파일에 붙여넣습니다.

**사용 가능한** 상태에 있는 권한 부여 키 및 피어 회로 URI를 복사합니다.  **사용된** 상태는 키가 가상 네트워크 연결을 만드는 데 이미 사용되었음을 나타냅니다.

![가상 네트워크 연결 페이지](media/virtual-network-connection.png)

Azure 가상 네트워크를 CloudSimple 링크로 설정하는 방법에 대한 자세한 내용은 ExpressRoute 를 [사용하여 CloudSimple 프라이빗 클라우드 환경을 Azure 가상 네트워크에 연결](azure-expressroute-connection.md)참조하세요.

## <a name="next-steps"></a>다음 단계

* [프라이빗 클라우드에 대한 Azure 가상 네트워크 연결](azure-expressroute-connection.md)
* [Azure ExpressRoute를 사용하여 온-프레미스 네트워크에 연결](on-premises-connection.md)
