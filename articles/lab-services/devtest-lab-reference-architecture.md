---
title: Azure DevTest Labs에 대 한 엔터프라이즈 참조 아키텍처
description: 이 문서에서는 기업에서 Azure DevTest Labs에 대 한 참조 아키텍처 지침을 제공 합니다.
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
ms.openlocfilehash: 1bfd1b5b4b7febd98499e338fcb62e339867aef4
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244726"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>엔터프라이즈용 azure DevTest Labs 참조 아키텍처
이 문서에서는 기업에서 Azure DevTest Labs를 기반으로 솔루션을 배포할 수 있도록 참조 아키텍처를 제공 합니다. 다음과 같습니다.
- Azure ExpressRoute를 통해 온-프레미스 연결
- 가상 컴퓨터에 원격으로 로그인 하려면 원격 데스크톱 게이트웨이
- 개인 아티팩트에 대 한 아티팩트 저장소에 연결
- 랩에서 사용 되는 다른 PaaS 서비스

![참조 아키텍처 다이어그램](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>아키텍처
다음은 참조 아키텍처의 핵심 요소입니다.

- **Azure AD(Azure Active Directory)** : DevTest Labs에서 사용 하 여 [Azure AD 서비스 id 관리를 위한](../active-directory/fundamentals/active-directory-whatis.md)합니다. DevTest Labs에 따라 환경에 사용자 액세스 부여 하는 경우 이러한 두 가지 주요 측면을 고려 합니다.
    - **리소스 관리**: 리소스를 관리 하려면 Azure portal에 대 한 액세스 제공 (가상 머신 만들기; 환경을 만들; 시작, 중지, 다시 시작, 삭제 및 아티팩트가 적용 및 등). 리소스 관리는 Azure에서 역할 기반 액세스 제어 (RBAC)를 사용 하 여 수행 됩니다. 사용자에 게 역할을 할당 하 고 리소스 및 액세스 수준 사용 권한을 설정 합니다.
    - **가상 컴퓨터 (네트워크 수준)** : 기본 구성으로 가상 컴퓨터 로컬 관리자 계정을 사용 합니다. 사용 가능한 도메인 경우 ([Azure AD Domain Services](../active-directory-domain-services/overview.md)는 온-프레미스 도메인 또는 클라우드 기반 도메인), 컴퓨터를 도메인에 조인할 수 있습니다. 사용자의 도메인 기반 id 사용 하 여 Vm에 연결할 수 있습니다.
- **온-프레미스 연결**: 이 아키텍처 다이어그램 [ExpressRoute](../expressroute/expressroute-introduction.md) 사용 됩니다. 하지만 사용할 수 있습니다는 [사이트 간 VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)합니다. ExpressRoute는 DevTest Labs에 대 한 필요 하지 않습니다, 하지만 일반적으로 기업에서 사용 됩니다. ExpressRoute는 회사 리소스에 액세스 해야 하는 경우에 필요 합니다. 일반적인 시나리오는:
    - 온-프레미스 데이터를 클라우드로 이동할 수 없는 경우
    - 랩의 virtual machines를 온-프레미스 도메인에 가입 하려고 했습니다.
    - 보안/규정 준수에 대 한 온-프레미스 방화벽을 통해 클라우드 환경에서 들어오고 나가는 모든 네트워크 트래픽을 강제 하려고 합니다.
- **네트워크 보안 그룹**: 클라우드 환경 (또는 클라우드 환경 내에서) 트래픽을 제한 하는 일반적인 방법은 소스를 기반으로 하며 대상 IP 주소를 사용 하는 [네트워크 보안 그룹](../virtual-network/security-overview.md)합니다. 예를 들어, 랩의 네트워크에 회사 네트워크에서 시작 되는 트래픽만 허용 하려면.
- **원격 데스크톱 게이트웨이**: 일반적으로 기업에서는 회사 방화벽에서 나가는 원격 데스크톱 연결을 차단합니다. 여러 가지 방법으로 응용 프로그램을 포함 하 여 DevTest labs에서 클라우드 기반 환경에 대 한 연결을 사용 하도록 설정 하려면:
  - 사용 된 [원격 데스크톱 게이트웨이](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture), 및 허용 목록에 추가 하는 게이트웨이의 고정 IP 주소 부하 분산 장치.
  - [모든 들어오는 트래픽을 RDP](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) ExpressRoute/사이트 및 사이트 간 VPN 연결을 통해. 이 기능은 일반적인 고려 사항 기업 DevTest Labs 배포를 계획 하는 경우입니다.
- **네트워크 서비스 (가상 네트워크, 서브넷)** : 합니다 [Azure 네트워킹](../networking/networking-overview.md) 토폴로지는 DevTest Labs 아키텍처의 또 다른 핵심 요소입니다. 랩의 리소스 통신를 온-프레미스 및 인터넷에 액세스할 수 있는지 여부를 제어 합니다. 아키텍처 다이어그램 고객은 DevTest Labs를 사용 하는 가장 일반적인 방법은 다음과 같습니다. 통해 연결 된 모든 랩 [가상 네트워크 피어 링](../virtual-network/virtual-network-peering-overview.md) 사용 하 여를 [허브-스포크 모델](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) 온-프레미스 ExpressRoute/사이트 및 사이트 간 VPN 연결에 있습니다. 하지만 DevTest Lab에서는 Azure Virtual Network를 직접 사용 하도록 네트워킹 인프라를 설정 하는 방법에 대 한 제한은 없습니다.
- **DevTest Labs**:  DevTest Labs는 전체 아키텍처의 핵심 부분입니다. 서비스에 대 한 자세한 내용은 참조 하세요 [DevTest Labs에 대 한](devtest-lab-overview.md)합니다.
- **가상 머신과 다른 리소스 (SaaS, PaaS, IaaS)** :  가상 머신은 다른 Azure 리소스와 함께 지 원하는 DevTest Labs는 주요 워크 로드입니다. DevTest Labs를 사용 하면 쉽고 빠르게 Azure 리소스 (Vm 및 기타 Azure 리소스 포함)에 대 한 액세스를 제공 하는 엔터프라이즈에 대 한 합니다. Azure에 대 한 액세스에 자세히 알아보려면 [개발자](devtest-lab-developer-lab.md) 하 고 [테스터](devtest-lab-test-env.md)합니다.

## <a name="scalability-considerations"></a>확장성 고려 사항
랩의 일반적인 작업에 사용 되는 다른 Azure 리소스를 갖지 DevTest Labs가 없는 기본 제공 할당량 또는 제한이 있지만 [구독 수준 할당량](../azure-subscription-service-limits.md)합니다. 따라서 일반적인 엔터프라이즈 배포에서 DevTest Labs의 대규모 배포에 맞게 여러 Azure 구독이 필요 합니다. 기업에 가장 일반적으로 도달 하는 할당량은:

- **리소스 그룹**: 기본 구성에서 DevTest Labs 모든 새 가상 컴퓨터에 대 한 리소스 그룹을 만들거나 사용자 서비스를 사용 하 여 환경을 만듭니다. 구독에 포함 될 수 있습니다 [리소스 그룹에 최대 980](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager)합니다. 따라서 가상 컴퓨터 및 구독에 환경을 제한입니다. 가지 고려해 야 하는 다른 두 구성이 있습니다.
    - **[모든 virtual machines는 동일한 리소스 그룹으로 이동](resource-group-control.md)** : 이 설정은 리소스 그룹 제한을 충족 하도록 도와줍니다, 있지만 리소스 그룹 당 리소스 유형 제한을 영향을 줍니다.
    - **공용 Ip를 공유를 사용 하 여**: 동일한 크기 및 지역의 모든 Vm은 동일한 리소스 그룹으로 이동합니다. 이 구성은 가상 머신에 공용 IP 주소를 보유 하도록 허용 된 경우 리소스 그룹 할당량 및 리소스 그룹 당 형식 리소스 할당량 간에 "중간 지점을"입니다.
- **리소스 당 리소스 그룹 리소스 유형별**: 에 대 한 기본 제한이 [리소스 리소스 유형별 리소스 그룹당 800는](../azure-subscription-service-limits.md#resource-group-limits)합니다.  사용 하는 경우는 *모든 Vm이 동일한 리소스 그룹으로 이동* Vm 추가 디스크가 많은 경우에 특히이 구독에 훨씬 빠르게 제한 구성, 사용자 키를 누릅니다.
- **Storage 계정**: DevTest Labs에서 랩 저장소 계정으로 제공 됩니다. 에 대 한 Azure 할당량이 [지역당 구독 당 저장소 계정 수는 250](../azure-subscription-service-limits.md#storage-limits)합니다. 동일한 지역에 DevTest Labs의 최대 250 이기도합니다.
- **역할 할당**: 역할 할당 (소유자, 리소스, 권한 수준) 리소스에 사용자 또는 보안 주체 액세스를 제공 하는 방법입니다. Azure에서 방법이 [구독 당 2,000 역할 할당도](../azure-subscription-service-limits.md#role-based-access-control-limits)합니다. DevTest Labs 서비스는 기본적으로 각 VM에 대 한 리소스 그룹을 만듭니다. 소유자는 권한을 부여 *소유자* DevTest Labs VM에 대 한 사용 권한 및 *판독기* 리소스 그룹에 대 한 사용 권한. 이러한 방식으로 사용자가 만든 각 새 VM 사용자에 게 랩에 권한을 부여 하는 경우 사용 되는 할당 하는 것 외에도 두 개의 역할 할당을 사용 합니다.
- **API 읽기/쓰기**: Azure 및 REST Api, PowerShell, Azure CLI 및 Azure SDK를 포함 하 여 DevTest Labs를 자동화 하는 방법은 여러 가지가 있습니다. 자동화를 통해 다른 API 요청 제한에 도달할 수 있습니다. 각 구독은 최대 허용 [12,000 읽기 요청 및 1,200 쓰기 시간당 요청](../azure-resource-manager/resource-manager-request-limits.md)합니다. 이 제한의 때 주의 DevTest Labs를 자동화 합니다.

## <a name="manageability-considerations"></a>관리 효율성 고려 사항
DevTest Labs에 단일 랩 작업에 대 한 뛰어난 관리 사용자 인터페이스가 있습니다. 하지만 기업에서는 있을 가능성이 높습니다 여러 Azure 구독 및 많은 실습 합니다. 변경 하기 일관 되 게 모든 랩 스크립팅 자동화 해야 합니다. 다음은 몇 가지 예제 및 DevTest Labs 배포에 대 한 유용한 관리입니다.

- **랩 설정의 변경 내용을**: 일반적인 시나리오는 배포의 모든 랩에서 특정 랩 설정 업데이트 하는 것입니다. 예를 들어, 새 VM 인스턴스 크기를 사용할 수 있고 모든 랩을 허용 하도록 업데이트 해야 합니다. PowerShell 스크립트, CLI 또는 REST Api를 사용 하 여 이러한 변경 내용을 자동화 하는 것이 좋습니다.  
- **아티팩트 리포지토리에 개인용 액세스 토큰**:  일반적으로 90 일, 1 년 또는 2 년 동안에에서는 Git 리포지토리에 개인용 액세스 토큰 만료 합니다. 연속성을 위해 반드시 개인용 액세스 토큰을 확장 하거나 새로 만듭니다. 다음 automation을 사용 하 여 모든 랩에 새 개인 액세스 토큰을 적용 하려면.
- **랩 설정 변경 제한**: 종종 특정 설정 (예: marketplace 이미지를 사용할 수 있어) 제한 해야 합니다. 리소스 종류를 변경 되지 않게 하려면 Azure Policy를 사용할 수 있습니다. 또는 사용자 지정 역할을 만들고 사용자 대신 해당 역할을 부여할 수는 *소유자* 랩에 대 한 역할입니다. (내부 지원, 랩 공지, 허용 된 VM 크기 및 등) 환경에서 대부분의 설정에 대 한이 수행할 수 있습니다.
- **Vm 명명 규칙을 따르는**: 관리자는 일반적으로 클라우드 기반 개발 및 테스트 환경에 포함 된 Vm을 쉽게 식별 하려고 합니다. 사용 하 여 이렇게 [Azure 정책](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns)합니다.

DevTest Labs는 동일한 방식으로 관리 되는 기본 Azure 리소스를 사용 해야 합니다: 네트워킹, 디스크, 계산 및 등입니다. 예를 들어 Azure Policy는 랩에서 만들어진 가상 컴퓨터에 적용 됩니다. Azure Security Center는 VM 규정 준수에 보고할 수 있습니다. 및 Azure Backup 서비스는 랩의 Vm에 대 한 정기 백업을 제공할 수 있습니다.

## <a name="security-considerations"></a>보안 고려 사항
Azure DevTest Labs (계산, 네트워킹 및 등)는 Azure에서 기존 리소스를 사용합니다. 따라서 자동으로 있다는 두 가지 이점이 플랫폼에 기본 제공 되는 보안 기능입니다. 예를 들어, 회사 네트워크에서 발생 하는 들어오는 원격 데스크톱 연결을 요구 하려면 간단히 추가할 네트워크 보안 그룹을 원격 데스크톱 게이트웨이에서 가상 네트워크. 추가 보안 고려 사항에 일상적으로 실습을 사용 하는 팀 멤버에 권한을 부여 하는 사용 권한 수준입니다. 가장 일반적인 권한은 [ *소유자* 하 고 *사용자*](devtest-lab-add-devtest-user.md)합니다. 이러한 역할에 대 한 자세한 내용은 참조 하세요. [Azure DevTest Labs에 소유자 및 사용자 추가](devtest-lab-add-devtest-user.md)합니다.

## <a name="next-steps"></a>다음 단계
이 시리즈의 다음 문서를 참조하세요. [Azure DevTest Labs 인프라를 확장할](devtest-lab-guidance-scale.md)합니다.
