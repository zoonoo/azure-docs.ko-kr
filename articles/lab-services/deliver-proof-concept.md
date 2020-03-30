---
title: 개념 증명 제공 - Azure DevTest 랩 | 마이크로 소프트 문서
description: Azure DevTest 랩을 엔터프라이즈 환경에 성공적으로 통합할 수 있도록 개념 증명을 제공하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: takamath
ms.openlocfilehash: ca843213760cee60799568a6f33059c2bd91c835
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75643290"
---
# <a name="deliver-a-proof-of-concept"></a>개념 증명 제공 

Azure DevTest Labs의 주요 시나리오 중 하나는 클라우드에서 개발 및 테스트 환경을 사용하도록 설정하는 것입니다. 다음은 이러한 템플릿의 예입니다.

* 클라우드에서 개발자 데스크톱 만들기
* 테스트를 위한 환경 구성.
* 가상 시스템 및 기타 Azure 리소스에 대한 액세스를 사용하도록 설정합니다.
* 개발자가 배우고 실험할 수 있는 샌드박스 영역을 설정합니다.

또한 DevTest Labs는 기업이 기업 보안, 규정 및 규정 준수 정책을 준수하는 개발자에게 "자체 서비스 Azure"를 제공할 수 있도록 정책 가드레일 및 비용 제어를 제공합니다. 

모든 엔터프라이즈에는 Azure DevTest 랩을 환경에 성공적으로 통합하는 방법에 대한 다양한 요구 사항이 있습니다. 이 문서에서는 성공적인 개념 증명을 보장하기 위해 기업이 완료해야 하는 가장 일반적인 단계에 대해 설명합니다. 개념 증명은 성공적인 종단 간 배포를 위한 첫 번째 단계입니다. 

## <a name="getting-started"></a>시작 

개념 증명을 제공하기 시작하려면. Azure 및 DevTest 랩에 대해 알아보는 데 시간을 할애하는 것이 중요합니다.  다음은 몇 가지 시작 리소스입니다. 

* [Azure 포털 이해](https://azure.microsoft.com/features/azure-portal/)
* [데브테스트 연구소의 기초 사항](devtest-lab-overview.md)
* [DevTest 랩지원 시나리오](devtest-lab-guidance-get-started.md)
* [DevTest 랩 엔터프라이즈 설명서](devtest-lab-guidance-prescriptive-adoption.md)
* [소개에서 Azure 네트워킹으로](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>사전 요구 사항 

DevTest Labs를 사용하여 파일럿 또는 개념 증명을 성공적으로 완료하려면 몇 가지 전제 조건이 있습니다. 

* **Azure 구독**: 기업에는 Azure에 대한 액세스를 가능하게 하는 기존 [엔터프라이즈 계약이](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) 있는 경우가 많으며 DevTest Labs에 대한 기존 또는 새 구독을 사용할 수 있습니다. 또는 기업은 파일럿 중에 [Visual Studio 구독을](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) 사용할 수 있습니다(무료 Azure 크레딧 활용). 이러한 옵션을 사용할 수 없는 경우 엔터프라이즈는 [무료 Azure 계정을](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ)만들고 사용할 수 있습니다. 엔터프라이즈 계약이 있는 경우 [엔터프라이즈 개발자/테스트 구독을](https://azure.microsoft.com/offers/ms-azr-0148p/) 사용하면 Windows 10/Windows 8.1 클라이언트 운영 체제에 액세스하고 개발 및 테스트 워크로드에 대한 할인 요금을 얻을 수 있습니다. 
* **Azure Active Directory 테넌트**: 사용자 관리(예: 사용자 추가 또는 랩 소유자 추가)를 사용하려면 해당 사용자가 파일럿용 Azure 구독에 사용되는 [Azure Active Directory 테넌트의](https://azure.microsoft.com/services/active-directory/) 일부여야 합니다. 종종 기업은 사용자가 클라우드에서 온-프레미스 ID를 사용할 수 있도록 [하이브리드 ID를](../active-directory/hybrid/whatis-hybrid-identity.md) 설정하지만 DevTest Labs 파일럿에는 필요하지 않습니다. 

## <a name="scoping-of-the-pilot"></a>파일럿 의 범위 지정 

구현을 시작하기 전에 파일럿을 계획하는 것이 중요합니다. 리소스가 무기한 으로 유지되지 않는다는 것을 미리 알면 파일럿 사용자에게 적절한 기대치가 설정됩니다. 

> [!IMPORTANT]
> 파일럿을 선명하게 조정하고 기대치를 미리 설정하는 것의 중요성을 충분히 강조할 수는 없습니다.

시험 운용을 시작하기 전에 다음 주요 질문에 답하십시오. 

* 무엇을 배우고 싶은지, 그리고 파일럿의 성공은 어떤 모습일까요? 
* 파일럿에서 다룰 워크로드 또는 시나리오는 무엇입니까? 파일럿의 범위를 빠르게 조정하고 완료할 수 있도록 작은 집합만 정의하는 것이 중요합니다. 
* 랩에서 사용할 수 있어야 하는 리소스는 무엇입니까? 예: 사용자 지정 이미지, 마켓플레이스 이미지, 정책, 네트워크 토폴로지. 
* 환경을 확인하기 위해 시험 운용에 참여할 사용자와 팀은 누구입니까?  
* 파일럿 의 기간은 어떻게 됩니까? 2주 또는 1개월과 같이 계획된 범위에 잘 맞는 기간을 선택합니다. 
* 파일럿이 완료되면 파일럿 중에 사용된 할당된 리소스는 어떻게 됩니까? 파일럿 리소스를 폐기할 계획입니까? 다음과 같은 생각을 할 수 있습니다.
   
   "파일럿 이 끝날 때 가상 머신과 랩을 버릴 계획을 세울 수 있다면 파일럿에 대한 단일 구독을 설정하고 확장 롤아웃 질문을 병렬로 해결하는 동안 모든 작업을 수행할 수 있습니다." 

파일럿을 "완벽"하게 만드는 일반적인 경향이 있으므로 회사에서 서비스가 출시된 후 최종 상태가 어떻게 될지 동일하게 나타냅니다. 이것은 잘못된 가정입니다. "완벽한"에 가까워질수록 파일럿을 시작하기 *전에* 더 많이 완료해야합니다. 파일럿의 목적은 최종 서비스를 확장하고 롤아웃하는 데 올바른 결정을 내리는 것입니다. 

파일럿의 초점은 Azure DevTest Labs가 기업에 적합한 서비스인지 여부에 대한 질문에 답하기 위해 필요한 최소한의 워크로드 와 종속성을 선택하는 데 중점을 두어야 합니다. 빠르고 깔끔한 성공을 보장하기 위해 최소한의 종속성으로 가장 간단한 워크로드를 선택하는 것이 좋습니다. 그렇지 않은 경우 파일럿 단계의 성공을 확장 단계에서 복제할 수 있도록 잠재적복잡성을 노출하는 가장 대표적인 워크로드를 선택합니다. 

다음 예제에서는 범위가 잘 조정된 개념 증명을 보여 줍니다. 

## <a name="example-proof-of-concept-plan"></a>예: 개념 증명 계획 

이 섹션에서는 DevTest Labs에 대한 파일럿 개념 증명을 지정하는 데 사용할 샘플을 보여 주며 있습니다. 

> [!TIP]
> 실패에 대 한 프로젝트를 설정 하는 가능성을 최소화 하려면 이 섹션에 설명 된 예제를 건너뛰지 않는 것이 좋습니다. 

### <a name="overview"></a>개요 

공급업체가 회사 네트워크에서 격리된 환경으로 사용할 수 있도록 DevTest Labs를 기반으로 Azure에서 새 환경을 개발할 계획입니다. 솔루션이 요구 사항을 충족하는지 여부를 결정하기 위해 종단 간 솔루션의 유효성을 검사하는 개념 증명을 개발합니다. 경험을 검증하기 위해 여러 공급업체를 포함시켰습니다. 

### <a name="outcomes"></a>결과 

개념 증명을 구축할 때, 우리는 먼저 결과 (우리가 달성하고자하는 것)에 초점을 맞춥니다. 개념 증명이 끝날 때까지 다음을 기대합니다. 

* 공급업체가 Azure Active Directory(Azure AD)의 게스트 계정을 사용하여 Azure에서 격리된 환경에 액세스하는 데 사용할 수 있는 종단 간 솔루션입니다. 환경에는 생산성을 높이는 데 필요한 리소스가 있습니다. 
* 광범위한 사용 및 채택에 영향을 주는 잠재적차단 문제는 모두 모두 수거되고 이해됩니다.
* 개념 증명 개발에 관련된 개인은 모든 코드를 잘 이해합니다. 그들은 또한 관련 된 담보를 이해 하 고 광범위 한 채택에 확신.

### <a name="open-questions-and-prerequisites"></a>질문 및 전제 조건 열기 

* 이 프로젝트에 사용할 수 있는 구독이 만들어졌습니까? 
* Azure AD 테넌트와 Azure AD 글로벌 관리자가 Azure AD 관련 질문에 대한 도움말 및 지침을 제공할 수 있는 사람이 확인되어 있습니까? 
* 프로젝트에 참여하는 개인을 위해 협업할 수 있는 장소가 있습니까? 

   * 소스 코드 및 스크립트(예: Azure 리포지토리) 
   * 문서(예: 마이크로소프트 팀 또는 쉐어포인트)  
   * 대화(예: 마이크로소프트 팀) 
   * 작업 항목(예: Azure 보드) 
* 공급업체에 필요한 리소스는 무엇입니까? 여기에는 가상 컴퓨터와 다른 필수 서버에서 로컬로 네트워크에서 사용할 수 있는 응용 프로그램이 포함됩니다. 
* 가상 시스템이 Azure의 도메인에 조인되나요? 그렇다면 Azure Active Directory 도메인 서비스(Azure AD DS) 또는 다른 서비스일까요? 
* 개념 증명의 대상이 될 팀이나 공급업체를 확인했습니까? 환경에 대한 고객은 누구입니까?
* 개념 증명에 사용할 Azure 지역은 무엇입니까? 
* 공급업체가 IaaS(VM) 외에 DevTest Labs를 통해 사용할 수 있는 서비스 목록이 있습니까? 
* 랩 사용에 대한 공급업체/사용자를 교육하려면 어떻게 해야 할까요? 

### <a name="components-of-the-proof-of-concept-solution"></a>개념 증명 솔루션의 구성 요소 

솔루션에 다음 구성 요소가 있을 것으로 예상됩니다. 

* 다양한 공급업체 팀은 Azure에서 랩 집합을 사용합니다.
* 랩은 요구 사항을 지원하는 네트워크 인프라에 연결됩니다.
* 공급업체는 Azure AD 및 역할 할당을 통해 랩에 액세스할 수 있습니다.
* 공급업체는 리소스에 성공적으로 연결할 수 있는 방법을 가지고 있습니다. 특히 사이트 간 VPN을 사용하면 공용 IP 주소 없이 가상 시스템에 직접 액세스할 수 있습니다.
* 아티팩트 집합은 공급업체가 가상 시스템에 필요한 필수 소프트웨어를 다룹니다.

## <a name="additional-planning-and-design-decisions"></a>추가 계획 및 설계 결정 

전체 DevTest Labs 솔루션을 릴리스하기 전에 몇 가지 중요한 계획 및 설계 결정을 내려야 합니다. 개념 증명을 작업하는 경험은 이러한 결정을 내리는 데 도움이 될 수 있습니다. 추가 고려 사항은 다음과 같습니다. 

* **구독 토폴로지:** Azure의 리소스에 대한 엔터프라이즈 수준 요구 사항은 [단일 구독 내에서 사용 가능한 할당량을 초과하여](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)확장할 수 있습니다. 이렇게 하려면 초기 구독 한도를 늘리기 위해 여러 Azure 구독 및/또는 서비스 요청이 필요합니다. 구독 간에 리소스를 배포하는 방법을 미리 결정하는 것이 중요합니다. 한 가지 중요한 리소스는 나중에 [리소스를](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) 다른 구독으로 이동하기가 어렵기 때문에 구독 결정 가이드입니다. 예를 들어 랩을 만든 후에는 다른 구독으로 이동할 수 없습니다.  
* **네트워크 토폴로지:** DevTest Labs가 자동으로 만드는 [기본 네트워크 인프라는](../app-service/networking-features.md) 엔터프라이즈 사용자의 요구 사항 및 제약 조건을 충족하기에 충분하지 않을 수 있습니다. [Azure ExpressRoute 연결 가상 네트워크,](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)구독 간 연결에 대한 [허브 및 스포크,](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) 온-프레미스 연결만 보장하기 위한 강제 [라우팅을](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) 참조하는 것이 일반적입니다. DevTest Labs를 사용하면 기존 가상 네트워크를 랩에 연결하여 랩에서 새 가상 컴퓨터를 만들 때 사용할 수 있습니다. 
* **가상 시스템의 원격 액세스**: DevTest 연구소에 있는 가상 컴퓨터에 원격으로 액세스할 수 있는 많은 옵션이 있습니다. 가장 쉬운 방법은 공용 IP 또는 공유 공용 IP를 사용하는 것입니다. 다음은 [랩에서 사용할 수 있는 설정입니다.](devtest-lab-shared-ip.md) 이러한 옵션만으로는 충분하지 않은 경우 원격 액세스 게이트웨이를 사용하는 것도 옵션입니다. 이 옵션은 [DevTest Labs 엔터프라이즈 참조 아키텍처에](devtest-lab-reference-architecture.md) 표시되며 [DevTest Labs 원격 데스크톱 게이트웨이 설명서에](configure-lab-remote-desktop-gateway.md)자세히 설명되어 있습니다. 또한 기업은 ExpressRoute 또는 사이트 간 VPN을 사용하여 랩을 온-프레미스 네트워크에 연결할 수도 있습니다. 이 옵션을 사용하면 인터넷에 노출되지 않고 개인 IP 주소를 기반으로 가상 시스템에 직접 원격 데스크톱 또는 SSH 연결을 사용할 수 있습니다. 
* **사용 권한 처리**: DevTest Labs에서 일반적으로 사용되는 두 가지 주요 권한은 [소유자 및 랩 사용자입니다.](devtest-lab-add-devtest-user.md) DevTest Labs를 광범위하게 롤아웃하기 전에 랩의 각 액세스 수준을 위임받을 사람을 결정하는 것이 중요합니다. 일반적인 모델은 랩 소유자와 랩 사용자로 팀 구성원으로 예산 소유자(예: 팀 리더)입니다. 이 모델을 사용하면 예산을 담당하는 사람(팀 장)이 정책 설정을 조정하고 팀을 예산 범위 내에서 유지할 수 있습니다.  

## <a name="completing-the-proof-of-concept"></a>개념 증명 완료 

예상 된 학습을 다룬 후 파일럿을 완료할 시간입니다. 이제 사용자로부터 피드백을 수집하고 파일럿이 성공했는지 확인하고 조직에서 엔터프라이즈의 DevTest Labs의 규모 롤아웃을 진행할지 여부를 결정해야 합니다. 또한 규모 롤아웃 전반에 걸쳐 일관성을 보장하기 위해 DevTest Labs 및 관련 리소스의 배포를 자동화하는 것도 고려해 볼 수 있는 좋은 시기입니다. 

## <a name="next-steps"></a>다음 단계 

* [DevTest 랩 엔터프라이즈 설명서](devtest-lab-guidance-prescriptive-adoption.md)
* [엔터프라이즈용 참조 아키텍처](devtest-lab-reference-architecture.md)
* [DevTest 랩 배포 확장](devtest-lab-guidance-orchestrate-implementation.md)
* [Azure DevTest Labs의 구현 오케스트레이션](devtest-lab-guidance-orchestrate-implementation.md)
