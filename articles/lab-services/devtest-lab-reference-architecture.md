---
title: Azure DevTest 랩용 엔터프라이즈 참조 아키텍처
description: 이 문서에서는 엔터프라이즈의 Azure DevTest 랩에 대한 참조 아키텍처 지침을 제공합니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 77e6ab588f74c8b810f211e069c1c24043155111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132847"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Azure DevTest 연구소는 기업을 위한 아키텍처를 참조합니다.
이 문서에서는 엔터프라이즈의 Azure DevTest 랩을 기반으로 솔루션을 배포하는 데 도움이 되는 참조 아키텍처를 제공합니다. 여기에는 다음이 포함됩니다.
- Azure ExpressRoute를 통한 온-프레미스 연결
- 가상 시스템에 원격으로 로그인할 수 있는 원격 데스크톱 게이트웨이
- 개인 아티팩트에 대한 아티팩트 리포지토리에 대한 연결
- 실험실에서 사용되는 기타 PaaS 서비스

![참조 아키텍처 다이어그램](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architecture
다음은 참조 아키텍처의 핵심 요소입니다.

- **Azure Active Directory(Azure AD)**: DevTest 랩은 [ID 관리를 위해 Azure AD 서비스를](../active-directory/fundamentals/active-directory-whatis.md)사용합니다. 개발자 테스트 랩을 기반으로 하는 환경에 대한 액세스 권한을 사용자에게 부여할 때 다음 두 가지 주요 측면을 고려하십시오.
    - **리소스 관리**: Azure 포털에 대한 액세스를 제공하여 리소스를 관리합니다(가상 시스템 생성, 환경 만들기, 시작, 중지, 다시 시작, 삭제 및 아티팩트 적용 등). 리소스 관리는 역할 기반 액세스 제어(RBAC)를 사용하여 Azure에서 수행됩니다. 사용자에게 역할을 할당하고 리소스 및 액세스 수준 권한을 설정합니다.
    - **가상 시스템(네트워크 수준)**: 기본 구성에서 가상 시스템은 로컬 관리자 계정을 사용합니다. 사용 가능한 도메인(Azure[AD 도메인 서비스,](../active-directory-domain-services/overview.md)온-프레미스 도메인 또는 클라우드 기반 도메인)이 있는 경우 컴퓨터를 도메인에 조인할 수 있습니다. 그런 다음 사용자는 도메인 기반 ID를 사용하여 VM에 연결할 수 있습니다.
- **온-프레미스 연결**: 아키텍처 다이어그램에서 [ExpressRoute가](../expressroute/expressroute-introduction.md) 사용됩니다. 그러나 사이트 간 [VPN을](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)사용할 수도 있습니다. 익스프레스루트는 DevTest 랩에 필요하지 않지만 기업에서 일반적으로 사용됩니다. ExpressRoute는 회사 리소스에 액세스해야 하는 경우에만 필요합니다. 일반적인 시나리오는 다음과 같습니다.
    - 클라우드로 이동할 수 없는 온-프레미스 데이터가 있습니다.
    - 랩의 가상 컴퓨터를 온-프레미스 도메인에 조인하는 것을 선호합니다.
    - 보안/규정 준수를 위해 온-프레미스 방화벽을 통해 클라우드 환경 안팎의 모든 네트워크 트래픽을 강제로 강제로 지정하려고 합니다.
- **네트워크 보안 그룹**: 원본 및 대상 IP 주소를 기반으로 클라우드 환경(또는 클라우드 환경 내에서)으로 트래픽을 제한하는 일반적인 방법은 [네트워크 보안 그룹을](../virtual-network/security-overview.md)사용하는 것입니다. 예를 들어 회사 네트워크에서 시작된 트래픽만 랩의 네트워크로 허용하려고 합니다.
- **원격 데스크톱 게이트웨이**: 기업은 일반적으로 회사 방화벽에서 나가는 원격 데스크톱 연결을 차단합니다. DevTest Labs의 클라우드 기반 환경에 대한 연결을 활성화하는 몇 가지 옵션이 있습니다.
  - 원격 [데스크톱 게이트웨이를](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)사용하고 게이트웨이 로드 밸러버의 정적 IP 주소를 허용합니다.
  - ExpressRoute/사이트 간 VPN 연결을 통해 [들어오는 모든 RDP 트래픽을 직접](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) 연결합니다. 이 기능은 기업이 DevTest Labs 배포를 계획할 때 일반적으로 고려해야 합니다.
- **네트워크 서비스(가상 네트워크, 서브넷)**: [Azure 네트워킹](../networking/networking-overview.md) 토폴로지는 DevTest Labs 아키텍처의 또 다른 핵심 요소입니다. 랩의 리소스가 통신할 수 있는지 여부를 제어하고 온-프레미스 및 인터넷에 액세스할 수 있습니다. 아키텍처 다이어그램에는 고객이 DevTest Labs를 사용하는 가장 일반적인 방법: 모든 랩은 [허브 스포크 모델을](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) 사용하여 가상 [네트워크 피어링을](../virtual-network/virtual-network-peering-overview.md) 통해 온-프레미스에 대한 ExpressRoute/site-site VPN 연결에 연결됩니다. 그러나 DevTest Labs는 Azure 가상 네트워크를 직접 사용하므로 네트워킹 인프라를 설정하는 방법에 대한 제한은 없습니다.
- **개발자 테스트 연구소**: DevTest 연구소는 전체 아키텍처의 핵심 부분입니다. 서비스에 대한 자세한 내용은 [DevTest 랩 소개를](devtest-lab-overview.md)참조하십시오.
- **가상 컴퓨터 및 기타 리소스(SaaS, PaaS, IaaS)**: 가상 머신은 DevTest Labs가 다른 Azure 리소스와 함께 지원하는 주요 워크로드입니다. DevTest Labs를 사용하면 엔터프라이즈에서 Azure 리소스(VM 및 기타 Azure 리소스 포함)에 대한 액세스를 쉽고 빠르게 제공할 수 있습니다. [개발자](devtest-lab-developer-lab.md) 및 [테스터를](devtest-lab-test-env.md)위한 Azure에 대한 액세스에 대해 자세히 알아봅니다.

## <a name="scalability-considerations"></a>확장성 고려 사항
DevTest Lab에는 기본 제공 할당량 이나 제한이 없지만 랩의 일반적인 작업에 사용되는 다른 Azure 리소스에는 [구독 수준 할당량이 있습니다.](../azure-resource-manager/management/azure-subscription-service-limits.md) 따라서 일반적인 엔터프라이즈 배포에서는 DevTest Labs의 대규모 배포를 다루기 위해 여러 Azure 구독이 필요합니다. 기업이 가장 일반적으로 도달하는 할당량은 다음과 같습니다.

- **리소스 그룹**: 기본 구성에서 DevTest Labs는 모든 새 가상 시스템에 대한 리소스 그룹을 만들거나 사용자가 서비스를 사용하여 환경을 만듭니다. 구독에는 [최대 980개의 리소스 그룹이](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)포함될 수 있습니다. 따라서 구독의 가상 컴퓨터 및 환경의 한계입니다. 고려해야 할 두 가지 다른 구성이 있습니다.
    - **[모든 가상 컴퓨터는 동일한 리소스 그룹으로 이동](resource-group-control.md)**: 이 설정은 리소스 그룹 제한을 충족하는 데 도움이되지만 리소스 그룹당 리소스 유형 제한에 영향을 줍니다.
    - **공유 공용 IP 사용**: 크기 및 영역이 같은 모든 VM은 동일한 리소스 그룹으로 이동합니다. 이 구성은 가상 시스템에 공용 IP 주소를 가질 수 있는 경우 리소스 그룹 할당량과 리소스 유형 그룹당 리소스 유형 할당량 간의 "중간 지점"입니다.
- **리소스 유형당 리소스 그룹당 리소스:** [리소스 유형당 리소스 그룹당 리소스 그룹별 기본 제한은 800입니다.](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)  *모든 VM을 사용하면 동일한 리소스 그룹 구성으로 이동하므로 특히 VM에* 추가 디스크가 많은 경우 사용자가 이 구독 제한을 훨씬 빨리 설정합니다.
- **저장소 계정**: DevTest Labs의 랩에는 저장소 계정이 함께 제공됩니다. 구독당 지역별 저장소 계정 수에 대한 Azure [할당량은 250입니다.](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) 같은 지역에 있는 DevTest 연구소의 최대 수도 250개입니다.
- **역할 할당**: 역할 할당은 사용자 또는 보안 주체가 리소스(소유자, 리소스, 사용 권한 수준)에 대한 액세스 권한을 부여하는 방법입니다. Azure에서는 [구독당 2,000개의 역할 할당이 제한됩니다.](../azure-resource-manager/management/azure-subscription-service-limits.md#role-based-access-control-limits) 기본적으로 DevTest Labs 서비스는 각 VM에 대한 리소스 그룹을 만듭니다. 소유자는 DevTest Labs VM에 대한 *소유자* 권한 및 리소스 그룹에 대한 *판독기* 권한이 부여됩니다. 이러한 방식으로 사용자가 만드는 각 새 VM은 사용자에게 랩에 권한을 부여할 때 사용되는 할당 외에 두 가지 역할 할당을 사용합니다.
- **API 읽기/쓰기**: REST API, PowerShell, Azure CLI 및 Azure SDK를 포함하여 Azure 및 DevTest 랩을 자동화하는 다양한 방법이 있습니다. 자동화를 통해 API 요청에 대한 또 다른 제한에 [12,000 read requests and 1,200 write requests per hour](../azure-resource-manager/management/request-limits-and-throttling.md)도달할 수 있습니다. DevTest 랩을 자동화할 때 이 제한을 알고 있어야 합니다.

## <a name="manageability-considerations"></a>관리 효율성 고려 사항
DevTest Labs에는 단일 랩으로 작업할 수 있는 훌륭한 관리 사용자 인터페이스가 있습니다. 그러나 엔터프라이즈에는 여러 Azure 구독과 많은 랩이 있을 수 있습니다. 모든 랩을 일관되게 변경하려면 스크립팅/자동화가 필요합니다. 다음은 DevTest Labs 배포에 대한 몇 가지 예및 모범 관리 사례입니다.

- **랩 설정 변경**: 일반적인 시나리오는 배포의 모든 랩에서 특정 랩 설정을 업데이트하는 것입니다. 예를 들어 새 VM 인스턴스 크기를 사용할 수 있으며 모든 랩을 업데이트하여 허용해야 합니다. PowerShell 스크립트, CLI 또는 REST API를 사용하여 이러한 변경 내용을 자동화하는 것이 가장 좋습니다.  
- **아티팩트 리포지토리 개인 액세스 토큰**: 일반적으로 Git 리포지토리에 대한 개인 액세스 토큰은 90일, 1년 또는 2년 후에 만료됩니다. 연속성을 보장하려면 개인 액세스 토큰을 확장하거나 새 토큰을 만드는 것이 중요합니다. 그런 다음 자동화를 사용하여 모든 랩에 새 개인 액세스 토큰을 적용합니다.
- **랩 설정 변경 제한:** 특정 설정을 제한해야 하는 경우가 많습니다(예: 마켓플레이스 이미지 사용 허용). Azure 정책을 사용하여 리소스 유형에 대한 변경을 방지할 수 있습니다. 또는 사용자 지정 역할을 만들고 랩의 *소유자* 역할 대신 해당 역할을 사용자에게 부여할 수 있습니다. 랩의 대부분의 설정(내부 지원, 랩 공지, 허용된 VM 크기 등)에 대해 이 작업을 수행할 수 있습니다.
- **VM이 명명 규칙을 따르도록 요구**: 관리자는 일반적으로 클라우드 기반 개발 및 테스트 환경의 일부인 VM을 쉽게 식별하려고 합니다. [Azure 정책을](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns)사용하여 이 작업을 수행할 수 있습니다.

DevTest Labs는 네트워킹, 디스크, 계산 등과 같은 방식으로 관리되는 기본 Azure 리소스를 사용합니다. 예를 들어 Azure 정책은 랩에서 만든 가상 시스템에 적용됩니다. Azure 보안 센터는 VM 규정 준수를 보고할 수 있습니다. 또한 Azure 백업 서비스는 랩의 VM에 대한 정기적인 백업을 제공할 수 있습니다.

## <a name="security-considerations"></a>보안 고려 사항
Azure DevTest Labs는 Azure의 기존 리소스(계산, 네트워킹 등)를 사용합니다. 따라서 플랫폼에 내장된 보안 기능의 이점을 자동으로 누릴 수 있습니다. 예를 들어 회사 네트워크에서만 시작되는 원격 데스크톱 연결을 요구하려면 원격 데스크톱 게이트웨이의 가상 네트워크에 네트워크 보안 그룹을 추가하기만 하면 됩니다. 유일한 추가 보안 고려 사항은 매일 랩을 사용하는 팀 구성원에게 부여하는 권한 수준입니다. 가장 일반적인 사용 권한은 [ *소유자* 및 *사용자*](devtest-lab-add-devtest-user.md)입니다. 이러한 역할에 대한 자세한 내용은 [Azure DevTest Labs의 소유자 및 사용자 추가를](devtest-lab-add-devtest-user.md)참조하십시오.

## <a name="next-steps"></a>다음 단계
이 시리즈의 다음 문서: [Azure DevTest Labs 인프라를 확장합니다.](devtest-lab-guidance-scale.md)
