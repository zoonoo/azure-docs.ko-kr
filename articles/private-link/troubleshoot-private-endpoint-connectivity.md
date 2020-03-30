---
title: Azure 프라이빗 엔드포인트 연결 문제 해결
description: 개인 엔드포인트 연결을 진단하기 위한 단계별 지침
services: private-endpoint
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
ms.openlocfilehash: fcc482e6231bbd925fd500a37989052765dede58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538537"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Azure 프라이빗 엔드포인트 연결 문제 해결

이 문서에서는 Azure 개인 엔드포인트 연결 설정의 유효성을 검사하고 진단하는 단계별 지침을 제공합니다.

Azure 개인 엔드포인트는 개인 링크 서비스에 개인적으로 안전하게 연결하는 네트워크 인터페이스입니다. 이 솔루션을 사용하면 가상 네트워크에서 Azure 서비스 리소스에 대한 개인 연결을 제공하여 Azure에서 워크로드를 보호할 수 있습니다. 이 솔루션은 이러한 서비스를 가상 네트워크에 효과적으로 제공합니다.

다음은 개인 엔드포인트에서 사용할 수 있는 연결 시나리오입니다.

- 동일한 리전의 가상 네트워크
- 지역별 피어가상 네트워크
- 전 세계적으로 피어잉된 가상 네트워크
- VPN 또는 Azure ExpressRoute 회로를 통해 온-프레미스 고객

## <a name="diagnose-connectivity-problems"></a>연결 문제 진단 

다음 단계를 검토하여 모든 일반적인 구성이 예상대로 개인 엔드포인트 설정으로 연결 문제를 해결하는지 확인합니다.

1. 리소스를 탐색하여 개인 끝점 구성을 검토합니다.

    a. 개인 **링크 센터로 이동합니다.**

      ![프라이빗 링크 센터](./media/private-endpoint-tsg/private-link-center.png)

    b. 왼쪽 창에서 비공개 **끝점을**선택합니다.
    
      ![프라이빗 엔드포인트](./media/private-endpoint-tsg/private-endpoints.png)

    다. 진단할 전용 끝점을 필터링하고 선택합니다.

    d. 가상 네트워크 및 DNS 정보를 검토합니다.
     - 연결 상태가 **승인되어**있는지 확인합니다.
     - VM에 개인 끝점을 호스팅하는 가상 네트워크에 연결되어 있는지 확인합니다.
     - FQDN 정보(복사) 및 개인 IP 주소가 할당되어 있는지 확인합니다.
    
       ![가상 네트워크 및 DNS 구성](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. [Azure 모니터를](https://docs.microsoft.com/azure/azure-monitor/overview) 사용하여 데이터가 흐르는지 확인합니다.

    a. 개인 끝점 리소스에서 **모니터를**선택합니다.
     - 데이터 에서 또는 **데이터 꺼내기를** **선택합니다.** 
     - 개인 끝점에 연결하려고 할 때 데이터가 흐르는지 확인합니다. 약 10분의 지연이 예상됩니다.
    
       ![개인 엔드포인트 원격 분석 확인](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Azure 네트워크 감시자의 **VM 연결 문제 해결을** 사용합니다.

    a. 클라이언트 VM을 선택합니다.

    b. **연결 문제 해결을**선택한 다음 **아웃바운드 연결** 탭을 선택합니다.
    
      ![네트워크 감시자 - 아웃바운드 연결 테스트](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    다. **자세한 연결 추적을 위해 네트워크 감시자 사용을**선택합니다.
    
      ![네트워크 감시자 - 연결 문제 해결](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. **FQDN으로 테스트를**선택합니다.
     - 개인 끝점 리소스에서 FQDN을 붙여넣습니다.
     - 포트를 제공합니다. 일반적으로 Azure 저장소 또는 Azure Cosmos DB에 443을 사용하고 SQL에 1336을 사용합니다.

    e. **테스트를**선택하고 테스트 결과의 유효성을 검사합니다.
    
      ![네트워크 감시자 - 테스트 결과](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. 테스트 결과의 DNS 확인은 개인 끝점에 할당된 동일한 개인 IP 주소가 있어야 합니다.

    a. DNS 설정이 올바르지 않으면 다음 단계를 따르십시오.
     - 전용 영역을 사용하는 경우: 
       - 클라이언트 VM 가상 네트워크가 개인 영역과 연결되어 있는지 확인합니다.
       - 개인 DNS 영역 레코드가 있는지 확인합니다. 존재하지 않는 경우 만듭니다.
     - 사용자 지정 DNS를 사용하는 경우:
       - 사용자 지정 DNS 설정을 검토하고 DNS 구성이 올바른지 확인합니다.
       지침은 개인 [끝점 개요: DNS 구성을](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration)참조하십시오.

    b. NSG(네트워크 보안 그룹) 또는 사용자 정의 경로로 인해 연결이 실패하는 경우:
     - NSG 아웃바운드 규칙을 검토하고 트래픽을 허용하는 적절한 아웃바운드 규칙을 만듭니다.
    
       ![NSG 아웃바운드 규칙](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. 연결에 결과가 유효성을 검사한 경우 연결 문제는 응용 프로그램 계층의 암호, 토큰 및 암호와 같은 다른 측면과 관련이 있을 수 있습니다.
   - 이 경우 개인 끝점과 연결된 개인 링크 리소스의 구성을 검토합니다. 자세한 내용은 Azure [개인 링크 문제 해결 가이드를](troubleshoot-private-link-connectivity.md)참조하십시오.

1. 문제가 아직 해결되지 않고 연결 문제가 여전히 있는 경우 [Azure 지원](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 팀에 문의하십시오.

## <a name="next-steps"></a>다음 단계

 * [업데이트된 서브넷(Azure 포털)에서 개인 끝점 만들기](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)
 * [Azure 개인 링크 문제 해결 가이드](troubleshoot-private-link-connectivity.md)
