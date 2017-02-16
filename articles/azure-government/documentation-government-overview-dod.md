---
title: "Azure Governmnet DoD 개요 | Microsoft 문서"
description: "Azure Government용 응용 프로그램 개발에 대한 지침 및 기능 비교를 제공합니다."
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: cba97199-851d-43ae-a75a-c601f3f81601
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/12/2017
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 71e8742d71ce6ebab08988751a886df4df000f8a
ms.openlocfilehash: 4e815c5fce563155f7a747262a3695a5180706e3


---
# <a name="department-of-defense-dod-in-azure-government"></a>Azure Government의 국방부(DoD)
## <a name="overview"></a>개요
Azure Government는 국방부(DoD) 엔터티가 Impact Level 4(L4) 및 Impact Level 5(L5)의 <a href="http://iasecontent.disa.mil/cloud/SRG/index.html">DoD 클라우드 컴퓨팅 보안 요구 사항 가이드, 버전 1, 릴리스 2</a>에 포함된 워크로드를 포함하여 다양한 워크로드 및 솔루션을 배포하는 데 사용됩니다.

Azure Government는 Defense Information Systems Agency의 Information Impact Level 5 DoD Provisional Authorization을 획득한 최초이자 유일한 대규모 상업적 클라우드 서비스입니다. 또한 Azure Government 지역은 US Department of Defense(미국방부) 고객 워크로드 전용으로 현재 일반 공급 상태입니다.

DoD가 클라우드로 전환하는 주요 동인 중 하나는 조직이 임무에 집중하고 자체 IT 솔루션의 구축 및 관리로 인한 혼란을 최소화할 수 있다는 점입니다.

Azure Government 기반 클라우드 아키텍처는 DoD 직원이 임무 목표에 집중하고, SharePoint 같은 IT 상용 서비스와 기타 응용 프로그램 워크로드를 관리할 수 있습니다.  따라서 중요한 IT 리소스를 응용 프로그램 개발, 분석 및 사이버 보안에 집중할 수 있습니다.

Azure가 제공하는 탄력성과 유연성은 DoD 고객에게 엄청난 이점으로 작용합니다. 온-프레미스 또는 DoD 데이터 센터에서 작업할 때 기존 하드웨어 및 서비스 조달 프로세스를 거치는 것보다 더 간단하게, 더 빠르게, 더 경제적으로 클라우드의 워크로드를 강화할 수 있습니다. 예를 들어, 심지어 테스트 환경에 새로운 다중 서버 하드웨어를 조달하는 경우에도 작업에 여러 달이 걸리고 상당한 자본 지출에 대한 승인을 받아야 합니다. 반면, Azure를 사용하면 기존 워크로드의 테스트 마이그레이션을 몇 주 또는 심지어 며칠 이내에 경제적인 방법으로 구성할 수 있습니다(테스트가 끝나면 환경을 폐기하여 지속적인 비용 지출을 막을 수 있음).

이러한 유연성은 중요합니다. DoD 고객이 Azure로 전환하면 비용을 절감할 수 있을 뿐 아니라 클라우드에서 새로운 기회를 찾을 수 있습니다. 예를 들어 간단하게 테스트 환경을 가동하여 신기술에 대한 통찰력을 확보하고, 응용 프로그램을 마이그레이션하고 Azure에서 테스트한 후 클라우드의 프로덕션 배포에 커밋할 수 있습니다. 업무 소유자는 아무 위험 없이 간편하게 보다 경제적인 옵션을 탐색할 수 있습니다.

또 다른 주요 영역은 보안입니다. 어떤 클라우드를 배포하더라도 서비스를 안전하고 안정적으로 제공하려면 적절한 계획이 필요합니다. 하지만 실제로는 Azure Government에서 가장 적절하게 구성된 클라우드 기반 워크로드(최대 L4 워크로드 포함)가 기존의 DoD 위치 및 데이터 센터에 구축된 기존 배포보다 안전합니다. 국방부가 모든 자산을 물리적으로 보호하는 데 필요한 전문 지식과 경험을 갖고 있지만 IT 노출 영역에는 많은 과제가 기다리고 있습니다. 사이버 보안은 빠르게 변화하고 있으며, 필요에 따라 대응 조치를 신속하게 개발하고 배포할 수 있는 전문 기술과 역량이 필요합니다. 현재 Azure 플랫폼(상용 및 Government)은 수십만 고객을 지원하고 있으며, 이처럼 고객 수가 많기 때문에 Microsoft는 진화하는 공격 벡터를 신속하게 감지한 후 개발 인력을 투입하여 신속하게 적절한 방어 수단을 개발하고 구축할 수 있습니다.

## <a name="dod-region-qa"></a>DoD 지역 Q&A

### <a name="what-are-the-azure-government-dod-regions"></a>Azure Government DoD 지역이란 무엇인가요? 
미국 국방부 동부 및 미국 국방부 중부 지역은 클라우드 컴퓨팅(특히, DoD 클라우드 컴퓨팅 보안 요구 사항 가이드(SRG)에 따라 DoD Impact Level 5로 지정된 데이터)에 대한 US Department of Defense(미국방부) 보안 요구 사항을 충족하기 위해 설계된 물리적으로 분리된 Microsoft Azure 지역입니다.   

### <a name="what-is-the-difference-between-azure-government-and-the-azure-government-dod-regions"></a>Azure Government와 Azure Government DoD 지역은 어떻게 다른가요? 
Azure Government는 연방, 주 및 지방 정부 고객, 소규모 조직, ITAR 대상 단체 및 이를 대신하여 작업을 수행하는 솔루션 공급자를 위한 서비스를 제공하는 미국 정부 커뮤니티 클라우드입니다. 모든 Azure Government 지역은 DoD Impact Level 5 데이터 및 FedRAMP의 높은 표준에 대한 보안 요구 사항을 충족하도록 설계 및 작동됩니다.

Azure Government DoD 지역은 DoD 고객만 사용하는 전용 Compute 및 Storage 인프라를 제공하여 Impact Level 5 데이터에 대한 물리적 분리 요구 사항을 지원하도록 설계됩니다.  

#### <a name="what-is-the-difference-between-impact-level-4-and-impact-level-5-data"></a>Impact Level 4와 Impact Level 5 데이터는 어떻게 다른가요?  
Impact Level 4 데이터는 CUI(Controlled Unclassified Information)이며, 여기에는 수출 제한 대상 데이터, 개인정보보호로 보호받는 의료 정보 및 명시적인 CUI 지정(즉, For Official Use Only, Law Enforcement Sensitive, Sensitive Security Information)이 요구되는 기타 데이터가 포함될 수 있습니다.

Impact Level 5 데이터는 정보 소유자, 공법 또는 정부 규제에 따라 높은 수준의 보호가 필요한 CUI(Controlled Unclassified Information)를 포함합니다.  Impact Level 5 데이터에는 미분류 국가 보안 시스템이 포함됩니다.  SRG Impact Level에 대한 추가 정보, 고유한 요구 사항 및 특성은 DoD 클라우드 컴퓨팅 보안 요구 사항 가이드의 섹션 3에 나와 있습니다.  

### <a name="what-data-is-categorized-as-impact-level-5"></a>어떤 데이터가 Impact Level 5로 분류되나요? 
Level 5는 정보 소유자, 공법 또는 기타 정부 규제에 따라 Level 4에서 제공하는 것보다 높은 수준의 보호가 필요한 CUI(Controlled Unclassified Information)를 포함합니다. Level 5는 미분류 국가 보안 시스템(NSS)도 지원합니다.  이 레벨에는 보통 수준의 기밀성 및 무결성(M-M-x)에 이르는 CNSSI-1253 기반의 NSS 및 CUI 정보 분류가 적용됩니다.

### <a name="what-is-microsoft-doing-differently-to-support-impact-level-5-data"></a>Impact Level 5 데이터를 지원하기 위한 Microsoft의 차별성은 무엇인가요? 
정의에 따라 Impact Level 5 데이터를 전용 인프라에서 처리할 수 있어 비연방 정부 테넌트로부터 DoD 고객의 물리적 분리를 보장해 줍니다.  미국 국방부 동부 및 미국 국방부 중부 지역 서비스에 있어서, Microsoft는 DoD에서 명시한 요구 사항보다 훨씬 높은 수준으로, 다른 대규모 상업적 클라우드 솔루션이 제공하는 보호 수준 및 용량을 능가하는 독보적인 서비스를 DoD 고객에게 제공합니다.

### <a name="do-these-regions-support-classified-data-requirements"></a>이러한 지역에서 분류된 데이터 요구 사항을 지원하나요? 
이러한 Azure Government DoD 지역은 최대 Impact Level 5를 포함한 미분류 데이터만 지원합니다.  Impact Level 6 데이터는 Secret까지 분류된 정보로 정의됩니다.

### <a name="what-organizations-in-the-dod-can-use-the-azure-government-dod-regions"></a>DoD의 어떤 조직에서 Azure Government DoD 지역을 사용할 수 있나요? 
미국 국방부 동부 및 미국 국방부 중부 지역은 US Department of Defense(미국방부) 고객층을 지원하도록 작성되었습니다.  다음 내용이 포함됩니다.
* The Office of the Secretary of Defense(미국 국방부 장관실)
* The Joint Chiefs of Staff(미국 합동 참모 본부)
* The Joint Staff(합동 참모)
* The Defense Agencies(방위청)
* Department of Defense Field Activities(국방부 분야 업무)
* The Department of the Army(육군성)
* The Department of the Navy(해군성)(미국 해병대 포함)
* The Department of the Air Force(공군성)
* The United States Coast Guard(미국 연안경비대)
* The unified combatant commands(통합전투사령부)
* 위에 나와 있는 공식 기관의 통제 또는 감독 하에 있는 기타 근무처, 기관, 업무 및 사령부

### <a name="are-the-dod-regions-more-secure"></a>DoD 지역은 더 안전한가요? 
Microsoft는 보안 및 규정 준수에 관한 현지 및 국제 표준을 준수하여 모든 Azure 데이터 센터 및 지원 인프라를 운영하고 있으며 상업적 클라우드 플랫폼의 규정 준수 투자 및 성과에 앞장서고 있습니다.  이러한 새로운 DoD 지역은 클라우드 컴퓨팅에 대해 DoD SRG에 정의된 요구 사항에 맞는 특정한 보증 및 약속을 제공합니다.

### <a name="why-are-there-multiple-dod-regions"></a>DoD 지역이 여러 개인 이유는 무엇인가요? 
여러 DoD 지역을 보유함으로써 Microsoft는 고객이 비즈니스 연속성을 보장하고 시스템 승인 요구 사항을 충족하는 지역 간 재해 복구 시나리오용 솔루션을 설계할 수 있도록 합니다.  또한 고객은 자신의 물리적 위치에 가장 가까운 접근 지역 내에 솔루션을 배포하여 성능을 최적화할 수 있습니다.

### <a name="are-these-dod-regions-connected-to-the-niprnet"></a>이러한 DoD 지역은 NIPRNet에 연결되어 있나요? 
DoD는 CUI에 사용되는 상업적 클라우드 서비스가 클라우드 액세스 지점(CAP)을 통해 고객에게 연결되어야 한다고 규정합니다.  따라서 Azure DoD 지역은 지리적으로 분산된 여러 CAP로의 중복 연결을 통해 NIPRNet에 연결됩니다.  DoD CAP는 DoD 정보 시스템 네트워크 및 서비스에 보호 기능을 제공하는 네트워크 경계 보호 및 모니터링 장치 시스템입니다.

### <a name="what-does-general-availability-mean"></a>일반 공급이란 무엇인가요? 
일반 공급은 Azure Government의 DoD 지역을 사용하여 프로덕션 워크로드를 지원할 수 있고 해당 지역에 배포된 모든 서비스에 대한 SLA를 금융 지원하며 일반 공급도 지원됨을 의미합니다.

### <a name="how-does-a-dod-customer-acquire-azure-government-dod-services"></a>DoD 고객은 Azure Government DoD 서비스를 어떻게 획득하나요? 
Azure Government DoD 서비스는 Azure Government와 같은 재판매 채널을 통해 공인 주체로 구매할 수 있습니다.  클라우드 서비스 획득 계획 및 비용 예측을 간소화하겠다는 Microsoft의 약속에 따라 Azure Government DoD 지역에 대한 가격 책정이 일반 공급 시점에 Azure 가격 계산기에 포함될 예정입니다.  Azure Government DoD 서비스는 수요에 맞춰 신속하게 확장하거나 축소할 수 있으므로 사용한 만큼만 지불하면 됩니다.
Azure Government를 이미 사용 중인 기업계약 고객은 계약 수정이 필요하지 않습니다.  

### <a name="how-are-the-dod-regions-priced"></a>DoD 지역의 가격은 어떻게 되나요? 
DoD 지역의 경우 지역 기간 가격 책정을 활용합니다.  즉, 확인된 DoD 고객에 대한 서비스 비용은 워크로드를 실행하는 Azure Government 지역을 기반으로 합니다.  구체적인 가격 정보는 Microsoft 고객 담당자에게 문의하세요.  DoD 지역에 대한 가격 책정은 Azure.com 계산기를 통해 향후 제공될 예정입니다.

### <a name="how-does-a-dod-organization-get-validated-for-the-azure-government-dod-regions"></a>Azure Government DoD 지역에 대해 DoD 조직을 검증하려면 어떻게 하나요? 
Azure DoD 지역에 대한 액세스 권한을 얻으려면, 고객은 조직 및 Azure DoD 환경의 용도를 검증하기 위한 사전 자격 과정을 완료해야 합니다.  사전 자격 과정을 성공적으로 완료하면 Microsoft에서 구독 만들기, 환경 액세스 및 조직의 다른 멤버에게 역할 기반 액세스 제어를 제공하는 방법에 대한 추가 지침을 조직별 지원자에게 제공합니다.

### <a name="can-independent-software-vendors-and-solution-providers-building-on-azure-deploy-solutions-in-the-azure-government-dod-regions"></a>Azure에서 구축 중인 독립 소프트웨어 공급 업체 및 솔루션 공급자가 Azure Government DoD 지역에 솔루션을 배포할 수 있나요? 
Azure 기반 클라우드 서비스 제품을 사용하는 솔루션 공급자는 Azure Government DoD 지역에서 DoD 전용 단일 테넌트 및 다중 테넌트 솔루션을 작동할 수 있습니다.  이러한 공급자는 승인된 DoD 주체와의 계약이 증빙된 문서 또는 승인된 DoD 주체의 스폰서 서류를 보유하고 있음을 나타내 자격을 증명해야 합니다.  Azure Government DoD 지역에서 공급자가 제공하는 서비스에는 해당 제품에서 Impact Level 5 정보를 처리하는 솔루션을 운영하기 위해 컴퓨터 네트워크 방어, 인시던트 보고 및 선별된 직원이 포함되어야 합니다.  DoD 클라우드 컴퓨팅 보안 요구 사항 가이드에서 솔루션 공급자에 대한 추가 지침을 찾을 수 있습니다.

### <a name="will-office-365-or-microsoft-dynamics-365-be-a-part-of-this-offering"></a>Office 365 또는 Microsoft Dynamics 365가 이 제품에 포함되나요? 
Microsoft는 이 제품과 함께 Impact Level 5에서 DoD용 Office 365 서비스를 제공하고 있습니다.  향후 Dynamics 365를 통해 Azure DoD 지역에서 Impact Level 5 서비스를 제공할 계획입니다.

### <a name="how-do-i-connect-to-the-dod-regions-once-i-have-a-subscription"></a>구독이 있을 경우 DoD 지역에 어떻게 연결하나요? 
Azure Government에 대한 DoD 지역은 Azure Government 관리 포털을 통해 사용할 수 있습니다.  사용 승인된 DoD 고객에게는 사용 가능한 서비스를 배포할 때 가능한 옵션으로 지역 목록이 표시됩니다.  Azure Government 구독 관리에 대한 일반적인 지침은 일반적인 설명서를 참조하세요.

### <a name="what-services-are-part-of-your-impact-level-5-accreditation-scope"></a>Impact Level 5 승인 범위에는 어떤 서비스가 포함되나요? 
Azure는 매주 새로운 서비스 및 기능이 추가되는 에버그린 서비스이며 범위에 있는 여러 서비스가 정기적으로 확장됩니다.  최신 정보는 <a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA"> Microsoft 보안 센터를 참조하세요.


## <a name="next-steps"></a>다음 단계:
<a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA"> Microsoft 보안 센터 - DoD 웹 페이지 </a>

<a href="http://iasecontent.disa.mil/cloud/SRG/index.html"> DoD 클라우드 컴퓨팅 보안 요구 사항 가이드, 버전 1, 릴리스 2 </a>

<a href="https://azure.microsoft.com/en-us/offers/azure-government/"> Azure Government 재판매인 채널

<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 블로그</a>.




<!--HONumber=Jan17_HO3-->


