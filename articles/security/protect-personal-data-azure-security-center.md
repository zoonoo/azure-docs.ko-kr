---
title: "Azure Security Center를 사용하여 개인 데이터 보호 | Microsoft Docs"
description: "Azure Security Center를 사용하여 개인 데이터를 보호합니다."
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 3a941389713a4d3dbffbbfe8a717409927d85c6d
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2017
---
# <a name="protect-personal-data-from-breaches-and-attacks-azure-security-center"></a>위반 및 공격으로부터 개인 데이터 보호: Azure Security Center

이 문서는 Azure Security Center를 사용하여 위반 및 공격으로부터 개인 데이터를 보호하는 방법을 이해하는 데 도움이 됩니다.

## <a name="scenario"></a>시나리오 

미국에 본사를 둔 대형 크루즈 회사는 영국 제도뿐만 아니라 지중해 및 발트해 연안의 여정도 제공하도록 사업을 확장하고 있습니다. 이러한 노력을 지원하기 위해 이탈리아, 독일, 덴마크 및 영국에 기반을 둔 몇 개의 소형 크루즈 라인을 인수했습니다.

회사에서 Microsoft Azure를 사용하여 클라우드에 회사 데이터를 저장합니다. 여기에는 이름, 주소, 전화 번호 및 신용 카드 정보와 같이 식별 가능한 개인 정보가 포함됩니다. 또한 다음과 같은 인적 자원 정보도 포함됩니다.

- 주소
- 전화 번호
- 납세자 번호
- 의료 보험 정보

또한 크루즈 라인에서 보상 및 충성도 프로그램 구성원에 대한 대규모 데이터베이스를 유지하고 있습니다. 회사 직원은 회사의 원격 사무실에서 네트워크에 액세스하고 전 세계에 위치한 여행사는 일부 회사 리소스에 액세스할 수 있습니다.
개인 데이터는 이러한 위치와 Microsoft 데이터 센터 간에 네트워크를 통해 이동합니다.

## <a name="problem-statement"></a>문제 설명

회사에서 Azure 리소스에 대한 공격의 위협에 대해 우려하고 있습니다. 권한 없는 사람에게 고객과 직원의 개인 데이터가 노출되지 않도록 방지하려고 합니다. 클라우드 리소스에 대한 지속적인 보안을 모니터링하는 효과적인 방법뿐만 아니라 방지 및 대응/수정에 대한 지침도 원합니다.
오늘날의 정교하고 조직화된 공격자들에 대한 강력한 방어선이 필요합니다.

## <a name="company-goal"></a>회사 목표

회사의 목표 중 하나는 고객 및 직원의 개인 데이터를 위협으로부터 보호하여 개인 정보를 보호하는 것입니다. 이러한 목표 중 하나는 영향을 완화하기 위해 위반의 증상에 즉시 대응하는 것입니다. 보안의 현재 상태를 평가하고, 취약한 구성을 식별하고, 이를 수정하는 방법이 필요합니다.

## <a name="solutions"></a>솔루션

Microsoft ASC(Azure Security Center)에서는 통합된 보안 모니터링 및 정책 관리 솔루션을 제공합니다. 사용하기 쉽고 효과적인 위협 방지, 검색 및 대응 기능을 제공합니다.

### <a name="prevention"></a>방지

ASC를 사용하면 보안 정책을 설정하고, JIT(Just-In-Time) 액세스를 제공하며, 보안 권장 사항을 구현할 수 있게 하여 위반을 방지할 수 있습니다.

보안 정책은 지정된 구독 내에 있는 리소스에 권장되는 제어 집합을 정의합니다. JIT 액세스는 Azure VM에 대한 인바운드 트래픽을 잠궈 공격에 대한 노출을 줄이는 데 사용할 수 있습니다. 보안 권장 사항은 Azure 리소스의 보안 상태를 분석한 후 ASC에서 만들어집니다.

#### <a name="how-do-i-set-security-policies-in-asc"></a>ASC에서 보안 정책을 설정하려면 어떻게 할까요?

각 구독에 대한 보안 정책을 구성할 수 있습니다. 보안 정책을 수정하려면 해당 구독의 소유자 또는 참여자여야 합니다. Azure Portal에서 다음을 수행합니다.

1. ASC 대시보드에서 **정책**을 선택합니다.

2. 정책을 사용하도록 설정하려는 구독을 선택합니다.

3. **방지 정책**을 선택하여 구독당 정책을 구성합니다. **가상 컴퓨터에서 데이터 수집**은 **설정**으로 설정되어야 합니다.

4. **방지 정책**  옵션에서 **설정**을 선택하여 구독과 관련된 보안 권장 사항을 사용하도록 설정합니다.

![](media/protect-personal-data-azure-security-center/prevention-policy.png)

사용하도록 설정할 수 있는 각 정책 권장 사항에 대한 자세한 지침과 설명은 [Azure Security Center에서 보안 정책 설정](https://docs.microsoft.com/azure/security-center/security-center-policies#set-security-policies)을 참조하세요.

#### <a name="how-do-i-configure-just-in-time-access-jit"></a>JIT(Just-In-Time) 액세스를 구성하려면 어떻게 할까요?

JIT를 사용하도록 설정되면 Security Center에서 NSG 규칙을 만들어 Azure VM에 대한 인바운드 트래픽을 잠급니다. VM에서 인바운드 트래픽을 잠글 포트를 선택합니다. JIT 액세스를 사용하려면 다음을 수행합니다.

1. ASC 블레이드에서 **Just-In-Time VM 액세스 타일**을 선택합니다.

2. **권장** 탭을 선택합니다.

3. **VM** 아래에서 사용하도록 설정할 VM을 선택합니다. 그러면 VM 옆에 있는 확인 표시가 선택됩니다. 
4. VM에서 **JIT 사용**을 선택합니다.
5. **저장**을 선택합니다.

그런 다음 ASC에서 JIT에 사용하도록 권장하는 기본 포트를 확인할 수 있습니다. Just-In-Time 솔루션을 사용하도록 설정하려는 새 포트를 추가 및 구성할 수도 있습니다. Security Center의 **Just-In-Time VM 액세스** 타일에서는 JIT 액세스를 수행하도록 구성된 VM 수가 표시됩니다. 지난 주에 승인된 액세스 요청 수도 표시됩니다.

![](media/protect-personal-data-azure-security-center/jit-vm.png)

이를 수행하는 방법에 대한 지침 및 Just-In-Time 액세스에 대한 자세한 내용은 [Just-In-Time을 사용하여 가상 컴퓨터 액세스 관리](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)를 참조하세요.

#### <a name="how-do-i-implement-asc-security-recommendations"></a>ASC 보안 권장 사항을 구현하려면 어떻게 할까요?

보안 센터가 잠재적인 보안 취약점을 식별하는 경우 권장 사항을 만듭니다. 권장 사항은 필요한 컨트롤을 구성하는 과정을 안내합니다. 
1. ASC 대시보드에서 **권장 사항** 타일을 선택합니다.

2. 각 줄이 하나의 권장 사항을 나타내는 테이블 형식으로 표시된 권장 사항을 봅니다.

3. 권장 사항을 필터링하려면 **필터**를 선택하고 표시할 심각도 및 상태 값을 선택합니다.

4. 적용할 수 없는 권장 사항을 해제하려면 마우스 오른쪽 단추를 클릭하고 **해제**를 선택합니다.

5. 먼저 어떤 권고 사항을 적용해야 하는지 평가합니다.

6. 우선 순위에 따라 권장 사항을 적용합니다.

가능한 권장 사항 목록 및 각각을 적용하는 방법에 대한 연습은 [Azure Security Center에서 보안 권장 사항 관리](https://docs.microsoft.com/azure/security-center/security-center-recommendations)를 참조하세요.

### <a name="detection-and-response"></a>검색 및 대응

위협이 검색된 후 최대한 빨리 대응하므로 검색과 대응이 거의 동시에 수행됩니다.
ASC 위협 검색은 Azure 리소스, 네트워크 및 연결된 파트너 솔루션에서 보안 정보를 자동으로 수집하여 작동합니다. 공격자가 새롭고 점차 정교해지는 악용을 풀어 놓음에 따라 ASC에서는 검색 알고리즘을 빠르게 업데이트할 수 있습니다. ASC 위협 검색 기능이 작동하는 방법에 대한 자세한 내용은 [Azure Security Center 검색 기능](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)을 참조하세요.

#### <a name="how-do-i-manage-and-respond-to-security-alerts"></a>보안 경고를 관리하고 이에 대응하려면 어떻게 할까요?

우선 순위가 지정된 보안 경고 목록은 문제를 빠르게 조사하는 데 필요한 정보와 함께 Security Center에 표시됩니다. Security Center에는 공격을 해결하는 방법에 대한 권장 사항도 포함되어 있습니다. 보안 경고를 관리하려면 다음을 수행합니다.

1. ASC 대시보드에서 **보안 경고** 타일을 선택합니다. 그러면 각 경고에 대한 세부 정보가 표시됩니다.

2. 날짜, 상태 및 심각도에 따라 경고를 필터링하려면 **필터**를 선택한 다음 확인하려는 값을 선택합니다.

3. 경고에 대응하려면 정보를 선택하고 검토한 다음 공격받은 리소스를 선택합니다.

4. **설명** 필드에서 권장되는 수정 방법을 포함한 세부 정보가 표시됩니다.

![](media/protect-personal-data-azure-security-center/security-alerts.png)

경고를 관리하는 방법에 대한 자세한 내용은 [Azure Security Center에서 보안 경고 관리 및 대응](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)을 참조하세요.

보안 경고 조사에 대한 추가 지원을 받기 위해 회사에서 [Azure 로그 통합](https://aka.ms/AzLog)을 사용하여 ASC 경고를 자체의 SIEM 솔루션과 통합할 수 있습니다.

#### <a name="how-do-i-manage-security-incidents"></a>보안 인시던트를 관리하려면 어떻게 할까요?

ASC에서 보안 인시던트는 킬 체인(kill chain) 패턴과 일치하는 리소스에 대한 모든 경고의 집계입니다. 인시던트는 관련된 경고 목록을 표시하므로 각 항목에 대한 자세한 정보를 얻을 수 있습니다. [보안 경고] 타일 및 블레이드에서 인시던트가 표시됩니다.

보안 인시던트를 검토하고 관리하려면 다음을 수행합니다.

1. **보안 경고** 타일을 선택합니다. 보안 인시던트가 검색되면 보안 경고 그래프 아래에 표시됩니다. 다른 경고와 다른 아이콘이 표시됩니다.

2. 이 보안 인시던트에 대한 자세한 내용을 보려면 해당 인시던트를 선택합니다. 추가 세부 정보에는 전체 설명, 심각도, 현재 상태, 공격받은 리소스, 인시던트에 대한 수정 단계 및 이 인시던트에 포함된 경고가 포함되어 있습니다.

필터링하여 **인시던트만**, **경고만**, **둘 다**를 볼 수 있습니다.

#### <a name="how-do-i-access-the-threat-intelligence-report"></a>위협 인텔리전스 보고서에 액세스하려면 어떻게 할까요?

ASC는 여러 원본의 정보를 분석하여 위협을 식별합니다. 인시던트 대응 팀에서 위협을 조사하고 수정하도록 지원하기 위해 Security Center에는 검색된 위협에 관한 정보를 포함한 위협 인텔리전스 보고서가 있습니다.

Security Center에는 공격에 따라 달라질 수 있는 세 가지 유형의 위협 보고서가 있습니다.
제공되는 보고서는 다음과 같습니다.

- 활동 그룹 보고서: 공격자 및 이들의 목표와 전술에 대해 자세히 설명합니다.

- 캠페인 보고서: 특정 공격 캠페인의 세부 정보에 중점을 둡니다.

- 위협 요약 보고서: 이전 두 보고서의 모든 항목을 다룹니다.

이러한 유형의 정보는 공격의 출처, 공격자의 동기 및 이 문제를 완화하기 위한 조치를 파악하기 위한 지속적인 조사가 있는 인시던트 대응 프로세스에서 매우 유용합니다.

1. 위협 인텔리전스 보고서에 액세스하려면 다음을 수행합니다.

2. ASC 대시보드에서 **보안 경고** 타일을 선택합니다.

3. 자세한 정보를 얻으려는 보안 경고를 선택합니다.

4. **보고서** 필드에서 위협 인텔리전스 보고서에 대한 링크를 클릭합니다.

5. 그러면 다운로드할 수 있는 PDF 파일이 열립니다.

![](media/protect-personal-data-azure-security-center/security-alerts-suspicious-process.png)

ASC 위협 인텔리전스 보고서에 대한 자세한 내용은 [Azure Security Center 위협 인텔리전스 보고서](https://docs.microsoft.com/azure/security-center/security-center-threat-report)를 참조하세요.

### <a name="assessment"></a>평가

ASC는 보안 상태에 대한 테스트, 판단 및 평가를 지원하기 위해 가상 컴퓨터 권장 구성 요소의 일부로 Qualys 클라우드 에이전트와 통합된 취약성 평가를 제공합니다.

Qualys 에이전트는 Qualys 관리 플랫폼에 취약성 데이터를 보고한 다음, 취약성 및 상태 모니터링 데이터를 ASC로 다시 보냅니다. 취약성 평가 솔루션을 추가하도록 제안되는 권장 사항은 ASC 대시보드의 **권장 사항** 블레이드에 표시됩니다.

취약성 평가 솔루션이 대상 VM에 설치된 후에 Security Center는 VM을 스캔하여 시스템 및 응용 프로그램 취약성을 감지하고 식별합니다. 감지된 문제는 **Virtual Machines 권장 사항** 옵션에 표시됩니다.

#### <a name="how-do-i-implement-a-vulnerability-assessment-solution"></a>취약성 평가 솔루션을 구현하려면 어떻게 할까요? 

통합된 취약성 평가 솔루션이 Virtual Machine에 아직 배포되지 않았으면 Security Center에 설치하는 것이 좋습니다.

1. ASC 대시보드의 **권장 사항** 블레이드에서 **취약성 평가 솔루션 추가**를 선택합니다.

2. 취약성 평가 솔루션을 설치하려는 VM을 선택합니다.

3. **[수]개 VM에 설치**를 클릭합니다.

4. Azure Marketplace에서 파트너 솔루션을 선택하거나 **기존 솔루션 사용** 아래에서 **Qualys**를 선택합니다.

5. **파트너 솔루션** 블레이드에서 자동 업데이트 설정을 설정하거나 해제할 수 있습니다.

취약성 평가 솔루션을 구현하는 방법에 대한 자세한 지침은 [Azure Security Center의 취약성 평가](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Security Center 빠른 시작 가이드](https://docs.microsoft.com/azure/security-center/security-center-get-started)

- [Azure Security Center 소개](https://docs.microsoft.com/azure/security-center/security-center-intro)

- [Azure 로그 통합에 Azure Security Center 경고 통합](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration)

- [통합된 취약성 평가를 통한 Azure Security Center 강화(영문)](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/16/boost-azure-security-center-with-integrated-vulnerability-assessment/)
