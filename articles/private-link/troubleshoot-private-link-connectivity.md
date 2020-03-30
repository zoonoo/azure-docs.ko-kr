---
title: Azure Private Link 연결 문제 해결
description: 개인 링크 연결을 진단하기 위한 단계별 지침
services: private-link
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 1e5253d617c87d5869cebc817da6d265ebfdfa7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539470"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Azure Private Link 연결 문제 해결

이 문서에서는 Azure 개인 링크 설정에 대한 연결을 확인하고 진단하는 단계별 지침을 제공합니다.

Azure 개인 링크를 사용하면 Azure 저장소, Azure Cosmos DB 및 Azure SQL Database와 같은 서비스(PaaS) 서비스로 Azure 플랫폼에 액세스할 수 있으며 가상 네트워크의 개인 끝점을 통해 Azure 호스팅 고객 또는 파트너 서비스에 액세스할 수 있습니다. 가상 네트워크와 서비스 간의 트래픽은 공용 인터넷에서 노출되지 않도록 Microsoft 백본 네트워크를 통과합니다. 가상 네트워크에서 사용자 고유의 개인 링크 서비스를 만들어 고객에게 개인적으로 제공할 수도 있습니다.

개인 링크 액세스에 대한 Azure 로드 밸런서의 표준 계층 뒤에 실행되는 서비스를 활성화할 수 있습니다. 서비스 소비자는 가상 네트워크 내부에 개인 엔드포인트를 만들고 이 서비스에 매핑하여 개인적으로 액세스할 수 있습니다.

다음은 개인 링크에서 사용할 수 있는 연결 시나리오입니다.

- 동일한 리전의 가상 네트워크
- 지역별 피어가상 네트워크
- 전 세계적으로 피어잉된 가상 네트워크
- VPN 또는 Azure ExpressRoute 회로를 통해 온-프레미스 고객

## <a name="deployment-troubleshooting"></a>배포 문제 해결

개인 링크 서비스에 대해 선택한 서브넷에서 소스 IP 주소를 선택할 수 없는 경우 를 해결하는 경우개인 링크 서비스의 [네트워크 정책 비활성화에](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) 대한 정보를 검토합니다.

소스 IP 주소를 선택하는 서브넷에 대해 **privateLinkServiceNetworkPolicy** 설정을 사용하지 않도록 설정해야 합니다.

## <a name="diagnose-connectivity-problems"></a>연결 문제 진단

개인 링크 설정에 연결 문제가 발생하는 경우 다음 단계를 검토하여 모든 일반적인 구성이 예상대로 작동하는지 확인합니다.

1. 리소스를 탐색하여 개인 링크 구성을 검토합니다.

    a. 개인 **링크 센터로 이동합니다.**

      ![프라이빗 링크 센터](./media/private-link-tsg/private-link-center.png)

    b. 왼쪽 창에서 개인 **링크 서비스를**선택합니다.

      ![개인 링크 서비스](./media/private-link-tsg/private-link-service.png)

    다. 진단할 개인 링크 서비스를 필터링하고 선택합니다.

    d. 개인 끝점 연결을 검토합니다.
     - 연결을 원하는 개인 끝점이 **승인된** 연결 상태로 나열되어 있는지 확인합니다.
     - 상태가 보류 **중이면**선택하여 승인합니다.

       ![전용 엔드포인트 연결](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - 이름을 선택하여 연결하는 비공개 끝점으로 이동합니다. 연결 상태가 **승인됨으로**표시되는지 확인합니다.

       ![개인 끝점 연결 개요](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - 양측이 승인되면 연결을 다시 시도하십시오.

    e. **속성** 탭의 **개요** 탭 및 **리소스 ID에서** **별칭을** 검토합니다.
     - **별칭** 및 **리소스 ID** 정보가 이 서비스에 대한 개인 끝점을 만드는 데 사용하는 **별칭** 및 **리소스 ID와** 일치하는지 확인합니다.

       ![별칭 정보 확인](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![리소스 ID 정보 확인](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. **개요** 탭에서 **가시성** 정보를 검토합니다.
     - 구독이 **가시성** 범위에 속하는지 확인합니다.

       ![가시성 정보 확인](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g. **개요** 탭에서 **로드 밸러저** 정보를 검토합니다.
     - 로드 밸러블러 링크를 선택하여 로드 밸러울러로 이동하여 로드 밸러울러로 이동하실 수 있습니다.

       ![로드 밸로드서 정보 확인](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - 로드 밸러버 설정이 예상에 따라 구성되었는지 확인합니다.
       - **프런트 엔드 IP 구성을**검토합니다.
       - **백 엔드 풀 을 검토합니다.**
       - **부하 분산 규칙을**검토합니다.

       ![로드 밸러블러 특성 확인](./media/private-link-tsg/pls-ilb-properties.png)

     - 부하 분산이 이전 설정에 따라 작동하는지 확인합니다.
       - 로드 밸로이터 백 엔드 풀을 사용할 수 있는 서브넷 이외의 서브넷에서 VM을 선택합니다.
       - 이전 VM에서 로드 밸러저 프런트 엔드에 액세스해 보십시오.
       - 로드 밸런싱 규칙에 따라 연결하여 백 엔드 풀에 연결하면 로드 밸런서가 작동합니다.
       - Azure Monitor를 통해 로드 밸류에이터 메트릭을 검토하여 데이터가 로드 밸로울러를 통해 흐르는지 확인할 수도 있습니다.

1. [Azure 모니터를](https://docs.microsoft.com/azure/azure-monitor/overview) 사용하여 데이터가 흐르는지 확인합니다.

    a. 개인 링크 서비스 리소스에서 **메트릭을 선택합니다.**
     - **바이트 인** 또는 **바이트 아웃을 선택합니다.**
     - 개인 링크 서비스에 연결하려고 할 때 데이터가 흐르는지 확인합니다. 약 10분의 지연이 예상됩니다.

       ![비공개 링크 서비스 메트릭 확인](./media/private-link-tsg/pls-metrics.png)

1. 문제가 아직 해결되지 않고 연결 문제가 여전히 있는 경우 [Azure 지원](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 팀에 문의하십시오.

## <a name="next-steps"></a>다음 단계

 * [개인 링크 서비스 만들기(CLI)](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)
 * [Azure 개인 엔드포인트 문제 해결 가이드](troubleshoot-private-endpoint-connectivity.md)
