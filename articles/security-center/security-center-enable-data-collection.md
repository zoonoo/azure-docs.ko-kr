---
title: Azure Security Center에서 데이터 수집 | Microsoft Docs
description: " Azure Security Center에서 데이터 수집을 활성화하는 방법을 알아봅니다. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 8fed3ce98b23c5ac1cc97b88a278c5946f06af8e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968773"
---
# <a name="data-collection-in-azure-security-center"></a>Azure Security Center에서 데이터 수집
Security Center는 Azure virtual machines (Vm), 가상 머신 확장 집합, IaaS 컨테이너 및 보안 취약성과 위협을 모니터링 하는 온-프레미스) (포함 비 Azure 컴퓨터에서 데이터를 수집 합니다. Microsoft Monitoring Agent를 사용하여 데이터를 수집합니다. Microsoft Monitoring Agent는 머신에서 다양한 보안 관련 구성 및 이벤트 로그를 읽고 분석용으로 작업 영역에 데이터를 복사합니다. 이러한 데이터의 예로는 운영 체제 유형 및 버전, 운영 체제 로그(Windows 이벤트 로그), 실행 중인 프로세스, 머신 이름, IP 주소, 로그인된 사용자를 들 수 있습니다. 또한 Microsoft Monitoring Agent 에이전트 작업 영역에 크래시 덤프 파일을 복사합니다.

누락된 업데이트, 잘못 구성된 OS 보안 설정, 엔드포인트 보호 사용, 상태 및 위협 검색에 대한 가시성을 제공하려면 데이터 컬렉션이 필요합니다. 

이 문서에서는 Microsoft Monitoring Agent를 설치하고 수집된 데이터를 저장할 Log Analytics 작업 영역을 설정하는 방법을 설명합니다. 데이터 컬렉션을 사용하도록 설정하려면 두 작업을 모두 수행해야 합니다. 

> [!NOTE]
> - 계산 리소스 (Vm, 가상 머신 확장 집합, IaaS 컨테이너 및 비 Azure 컴퓨터)에 대 한 데이터 수집만 필요 합니다. 에이전트를 프로비전하지 않더라도 Azure Security Center를 활용할 수 있지만, 보안 수준이 제한되고 위에 나열된 기능이 지원되지 않습니다.  
> - 지원되는 플랫폼 목록은 [Azure Security Center에서 지원되는 플랫폼](security-center-os-coverage.md)을 참조하세요.
> - 가상 머신 확장 집합에 대한 데이터 컬렉션은 현재 지원되지 않습니다.
> - 신규 또는 기존 작업 영역을 사용할지 여부를 Log Analytics에 데이터를 저장할 데이터 저장소에 대 한 추가 요금이 발생에 대 한 자세한 가격 책정 페이지를 참조 하세요. 수도 있습니다.

## Microsoft Monitoring Agent 자동 프로비저닝 사용 <a name="auto-provision-mma"></a>

머신에서 데이터를 수집하려면 Microsoft Monitoring Agent가 설치되어 있어야 합니다.  에이전트를 자동으로 설치할 수도 있고(권장) 에이전트를 수동으로 설치하도록 선택할 수도 있습니다.  

>[!NOTE]
> 자동 프로비전은 기본적으로 해제되어 있습니다. 기본적으로 자동 프로비전을 설치하도록 Security Center를 설정하려면 **켜기**로 설정합니다.
>

자동 프로 비전를 설정 하면 Security Center에 Microsoft Monitoring agent에서 지원 되는 모든 Azure Vm 및 새로 만든 프로 비전 합니다. 자동 프로비저닝을 사용하는 것이 좋지만 수동 에이전트 설치도 사용할 수 있습니다. [Microsoft Monitoring Agent 확장을 설치하는 방법을 알아봅니다](#manualagent).



Microsoft Monitoring Agent의 자동 프로비저닝을 사용하도록 설정하려면 다음을 수행합니다.
1. Security Center 주 메뉴에서 **보안 정책**을 선택합니다.
2. 목록의 원하는 구독에 대한 설정 열에서 **설정 편집**을 클릭합니다.

   ![구독 선택][7]

3. **보안 정책** 아래에서 **데이터 수집**을 선택합니다.
4. **자동 프로비저닝** 아래에서 **설정**을 선택하여 자동 프로비저닝을 사용하도록 설정합니다.
5. **저장**을 선택합니다.

   ![자동 프로비저닝 사용][1]

>[!NOTE]
> - 기존 설치를 프로비전하는 방법은 [기존 에이전트 설치 시 자동 프로비전](#preexisting)을 참조하세요.
> - 수동으로 프로비전하는 방법은 [수동으로 Microsoft Monitoring Agent 확장 설치](#manualagent)를 참조하세요.
> - 자동 프로비전을 해제하는 방법은 [자동 프로비전 해제](#offprovisioning)를 참조하세요.
> - PowerShell을 사용하여 Security Center를 온보드하는 방법에 대한 지침은 [PowerShell을 사용하여 Azure Security Center의 온보딩 자동화](security-center-powershell-onboarding.md)를 참조하세요.
>

## <a name="workspace-configuration"></a>작업 영역 구성
Security Center에서 수집된 데이터는 Log Analytics 작업 영역에 저장됩니다.  Security Center에서 작성된 작업 영역에 저장되어 있는 Azure VM이나 직접 만든 기존 작업 영역에서 데이터를 수집할 수 있습니다. 

작업 영역 구성은 구독별로 설정되며, 여러 구독에서 동일한 작업 영역을 사용할 수 있습니다.

### <a name="using-a-workspace-created-by-security-center"></a>Security Center가 만든 작업 영역 사용

Security Center는 데이터를 저장할 기본 작업 영역을 자동으로 만들 수 있습니다. 

Security Center가 만든 작업 영역을 선택하려면:

1. **기본 작업 영역 구성** 아래에서 [Security Center가 만든 작업 영역 사용]을 선택합니다.
   ![가격 책정 계층 선택][10] 

1. **저장**을 클릭합니다.<br>
    Security Center는 해당 지리적 위치에 새 리소스 그룹 및 기본 작업 영역을 만들고 에이전트를 해당 작업 영역에 연결합니다. 작업 영역 및 리소스 그룹에 대한 명명 규칙은 다음과 같습니다.<br>
   **작업 영역: DefaultWorkspace-[subscription-ID]-[geo]<br> 리소스 그룹: DefaultResourceGroup-[geo]**

   구독에 여러 지리적 위치의 VM이 포함된 경우에는 Security Center에서 여러 작업 영역을 만듭니다. 여러 작업 영역은 데이터 프라이버시 규칙을 유지하기 위해 만들어집니다.
1. Security Center는 구독에 설정된 가격 책정 계층에 따라 작업 영역에서 자동으로 Security Center 솔루션을 사용합니다. 

> [!NOTE]
> Security Center에서 만든 작업 영역의 Log Analytics 가격 책정 계층은 Security Center 청구에 영향을 주지 않습니다. Security Center 청구는 항상 작업 영역에 설치된 Security Center 보안 정책 및 솔루션에 기반합니다. 체험 계층의 경우 Security Center는 기본 작업 영역에서 *SecurityCenterFree* 솔루션을 사용하도록 설정합니다. 표준 계층의 경우 Security Center는 기본 작업 영역에서 *Security* 솔루션을 사용하도록 설정합니다.
> Log Analytics에 데이터를 저장하면 데이터 저장소 요금이 추가로 발생합니다. 자세한 내용은 가격 책정 페이지를 참조하세요.

자세한 내용은 [Security Center 가격 책정](https://azure.microsoft.com/pricing/details/security-center/)을 참조하세요.

기존 log analytics 계정에 대 한 자세한 내용은 참조 하세요. [기존 log analytics 고객](security-center-faq.md#existingloganalyticscust)합니다.

### <a name="using-an-existing-workspace"></a>기존 작업 영역 사용

기존 Log Analytics 작업 영역을 갖고 있는 고객은 동일한 작업 영역을 사용하고 싶을 것입니다.

기존 Log Analytics 작업 영역을 사용하려면 작업 영역에 대한 읽기 및 쓰기 권한이 필요합니다.

> [!NOTE]
> 기존 작업 영역에서 사용하도록 설정된 솔루션은 연결된 Azure VM에 적용됩니다. 유료 솔루션의 경우 이로 인해 추가 요금이 발생할 수 있습니다. 데이터 개인 정보를 고려하여, 선택한 작업 영역이 적절한 지리적 영역에 있어야 합니다.
> Log analytics에서 데이터를 저장할 데이터 저장소에 대 한 추가 요금이 발생에 대 한 자세한 가격 책정 페이지를 참조 하세요. 수도 있습니다.

기존 Log Analytics 작업 영역을 선택하려면 다음을 수행합니다.

1. **기본 작업 영역 구성** 아래에서 **다른 작업 영역 사용**을 선택합니다.

   ![기존 작업 영역 선택][2]

2. 풀다운 메뉴에서 수집된 데이터를 저장할 작업 영역을 선택합니다.

   > [!NOTE]
   > 풀 다운 메뉴의 모든 구독에서 모든 작업 영역을 사용할 수 있습니다. 자세한 내용은 [구독 간 작업 영역 선택](security-center-enable-data-collection.md#cross-subscription-workspace-selection)을 참조하세요. 작업 영역에 액세스할 수 있는 권한이 있어야 합니다.
   >
   >

3. **저장**을 선택합니다.
4. **저장**을 선택하면 이전에 기본 작업 영역에 연결된 모니터링되는 VM을 다시 구성할지를 묻는 메시지가 표시됩니다.

   - 새 작업 영역 설정을 새 VM에만 적용하려면 **아니요**를 선택합니다. 그러면 새 작업 영역 설정이 새 Agent 설치(Microsoft Monitoring Agent가 설치되어 있지 않은 새로 검색된 VM)에만 적용됩니다.
   - 새 작업 영역 설정을 모든 VM에 적용하려면 **예**를 선택합니다. 이 경우에는 Security Center에서 만든 작업 영역에 연결된 모든 VM이 새 대상 작업 영역에 다시 연결됩니다.

   > [!NOTE]
   > 예를 선택하는 경우 모든 VM이 새 대상 작업 영역에 다시 연결될 때까지는 Security Center에서 작성된 작업 영역을 삭제하면 안 됩니다. 작업 영역을 너무 빨리 삭제하면 이 작업이 실패합니다.
   >
   >

   - 작업을 취소하려면 **취소**를 선택합니다.

     ![기존 작업 영역 선택][3]

5. Microsoft Monitoring Agent를 설정할 작업 영역의 가격 책정 계층을 선택합니다. <br>기존 작업 영역을 사용하려면 작업 영역의 가격 책정 계층을 설정합니다. 그러면 아직 Security Center 솔루션이 없는 경우 작업 영역에 설치됩니다.

    a.  Security Center 주 메뉴에서 **보안 정책**을 선택합니다.
     
    b.  목록의 원하는 구독에 대한 설정 열에서 **설정 편집**을 클릭하여 에이전트를 연결하려는 원하는 작업 영역을 선택합니다.
        ![작업 영역 선택][8] c. 가격 책정 계층을 설정합니다.
        ![가격 책정 계층 선택][9] 
   
   >[!NOTE]
   >작업 영역에서 **보안** 또는 **SecurityCenterFree** 솔루션이 이미 설정된 경우 가격 책정이 자동으로 설정됩니다. 

## <a name="cross-subscription-workspace-selection"></a>구독 간 작업 영역 선택
데이터를 저장할 작업 영역을 선택할 때 모든 구독에서 모든 작업 영역을 사용할 수 있습니다. 구독 간 작업 영역 선택을 사용하면 서로 다른 구독에서 실행 중인 가상 머신에서 데이터를 수집하고 사용자가 선택한 작업 영역에 저장할 수 있습니다. 이 옵션은 조직에서 중앙 집중식 작업 영역을 사용하고 있으며 보안 데이터 컬렉션에도 사용하려는 경우에 유용합니다. 작업 영역을 관리하는 방법에 대한 자세한 내용은 [작업 영역 액세스 관리](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access)를 참조하세요.


## <a name="data-collection-tier"></a>데이터 수집 계층
Azure Security Center에서 데이터 수집 계층을 선택하면 Log Analytics 작업 영역의 보안 이벤트 스토리지에만 영향을 미칩니다. Log Analytics 에이전트는 계속 수집 하 고 (있는 경우) Log Analytics 작업 영역에 저장 하도록 선택 하면 보안 이벤트의 계층에 관계 없이, Azure Security Center의 위협 검색에 필요한 보안 이벤트를 분석. 작업 영역에 보안 이벤트를 저장하도록 선택하면 작업 영역에서 해당 이벤트를 조사, 검색 및 감사할 수 있습니다. 
> [!NOTE]
> Log analytics에서 데이터를 저장할 데이터 저장소에 대 한 추가 요금이 발생에 대 한 자세한 가격 책정 페이지를 참조 하세요. 수도 있습니다.
> 
> 작업 영역에 저장할 4개 이벤트 집합 중에서 구독 및 작업 영역에 적합한 필터링 정책을 선택할 수 있습니다. 

- **없음** – 보안 이벤트 저장소를 사용하지 않도록 설정합니다. 기본 설정입니다.
- **최소** – 이벤트의 양을 최소화하려는 고객을 위한 더 작은 이벤트 집합입니다.
- **일반** – 대다수 고객의 요구를 충족하는 이벤트 집합으로, 전체 감사 내역을 확인할 수 있습니다.
- **모든 이벤트** – 모든 이벤트를 저장하려는 고객용입니다.


> [!NOTE]
> 이러한 보안 이벤트 집합은 Security Center의 표준 계층에서만 사용할 수 있습니다. Security Center의 가격 책정 계층에 대해 자세히 알아보려면 [가격 책정](security-center-pricing.md)을 참조하세요.
이러한 집합은 일반적인 시나리오를 해결하도록 설계되었습니다. 특정 집합을 구현하기 전에 요구에 적합한 집합을 평가하세요.
>
>

Microsoft는 **일반** 및 **최소** 이벤트 집합에 포함할 이벤트를 결정하기 위해 다수의 고객과 협력하고 업계 표준을 활용하여 각 이벤트의 필터링 미적용 시 빈도와 해당 사용 방식을 파악했습니다. 그에 따라 이 프로세스에서는 다음 지침이 사용되었습니다.

- **최소** - 이 집합에는 보안 침해 성공을 나타낼 수 있는 이벤트와 중요 이벤트(양이 매우 적음)만 포함하세요. 예를 들어 로그인 성공/실패(이벤트 ID 4624, 4625)는 이 집합에 포함되지만 로그아웃은 포함되지 않습니다. 로그아웃의 경우 감사에서는 중요하지만 검색에서는 중요하지 않으며 양이 비교적 많기 때문입니다. 이 집합에 포함되는 데이터 볼륨 중 대부분은 로그인 이벤트 및 프로세스 만들기 이벤트(이벤트 ID 4688)입니다.
- **일반** - 이 집합에서는 전체 사용자 감사 내역을 제공하세요. 예를 들어 사용자 로그인 및 사용자 로그오프(이벤트 ID 4634)를 모두 이 집합에 포함합니다. Microsoft는 보안 그룹 변경, 주요 도메인 컨트롤러 Kerberos 작업, 그리고 업계의 조직에서 권장하는 기타 이벤트와 같은 감사 작업을 이 집합에 포함했습니다.

양이 매우 적은 이벤트는 일반 집합에 포함되었습니다. 모든 이벤트를 선택하는 대신 이러한 이벤트를 선택하는 주된 이유는 특정 이벤트를 필터링하여 제외하기 위해서가 아니라 이벤트의 양을 줄이기 위해서이기 때문입니다.

아래에는 각 집합별 전체 보안 및 App Locker 이벤트 ID가 나와 있습니다.

| 데이터 계층 | 수집된 이벤트 표시기 |
| --- | --- |
| 최소 | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| 일반 | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - GPO(그룹 정책 개체)를 사용하는 경우 감사 정책인 프로세스 만들기 이벤트 4688과 *CommandLine* 필드 내부 이벤트 4688을 사용하는 것이 좋습니다. 프로세스 만들기 이벤트 4688에 대한 자세한 내용은 Security Center의 [FAQ](security-center-faq.md#what-happens-when-data-collection-is-enabled)를 참조하세요. 감사 정책에 대한 자세한 내용은 [감사 정책 권장 사항](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations)을 참조하세요.
> -  [적응형 애플리케이션 제어](security-center-adaptive-application.md)에 데이터 수집을 사용하기 위해 Security Center는 모든 애플리케이션을 허용하도록 감사 모드에서 로컬 AppLocker 정책을 구성합니다. 이렇게 하면 AppLocker가 이벤트를 생성하게 되고, 이 이벤트를 Security Center에서 수집하여 활용합니다. 이 정책은 이미 AppLocker 정책이 구성된 컴퓨터에서는 구성할 수 없습니다. 
> - Windows 필터링 플랫폼 [이벤트 ID 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)을 수집하려면 [감사 필터링 플랫폼 연결](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)을 사용하도록 설정해야 합니다(Auditpol /set /subcategory:"Filtering Platform Connection" /Success:Enable).
>

필터링 정책을 선택하려면 다음을 수행합니다.
1. **보안 정책 데이터 수집** 블레이드의 **보안 이벤트**에서 필터링 정책을 선택합니다.
2. **저장**을 선택합니다.

   ![필터링 정책 선택][5]

### 기존 에이전트 설치 시 자동 프로비전 <a name="preexisting"></a> 

다음 사용 사례는 이미 에이전트 또는 확장이 설치된 경우에 자동 프로비전의 작동 방식을 지정합니다. 

- 확장 (직접 에이전트) 것이 아니라 컴퓨터에서 Microsoft Monitoring Agent가 설치<br>
Microsoft Monitoring Agent (을 Azure 확장이 아니라) VM에 직접 설치 되어 있으면 Security Center가 Microsoft Monitoring Agent 확장을 설치 하 고 Microsoft Monitoring agent를 최신 버전으로 업그레이드할 수 있습니다.
설치 된 에이전트는 계속 해당 이미 구성 된 작업 영역에 보고 하 고 또한 Security Center에서 구성 된 작업 영역에 보고 됩니다 (멀티 호 밍 지원 됨).
구성된 된 작업 영역 사용자 작업 영역 (없습니다 Security Center의 기본 작업 영역)이 경우 설치 해야 합니다 "보안"securityFree"솔루션에 Vm 및 컴퓨터에서 이벤트 처리를 시작 하기 위해 Security Center에 대 한 해당 작업 영역에 보고 하는 /입니다.<br>
<br>
2019-03-17, 기존 에이전트는 감지 하면 전에 Security Center에 구독 등록의 기존 컴퓨터에 Microsoft Monitoring Agent 확장을 설치 하지 않습니다 및 컴퓨터에는 영향을 받지 않습니다. 이러한 컴퓨터를이 컴퓨터에 에이전트 설치 문제를 해결 하기 위한 "컴퓨터에서 에이전트 상태 문제 모니터링 해결" 권장 사항을 참조 하십시오.

  
- System Center Operations Manager 에이전트는 컴퓨터에 설치<br>
보안 센터를 기존 Operations Manager는 Microsoft Monitoring Agent 확장 하 여 나란히 설치 됩니다. 기존 Operations Manager 에이전트는 일반적으로 보고서를 Operations Manager 서버 계속 됩니다. Microsoft Monitoring Agent 및 Operations Manager 에이전트를이 프로세스 중 최신 버전으로 업데이트 될 공용 런타임 라이브러리를 공유 하는 참고 합니다.
참고-Operations Manager 2012 에이전트 버전이 설치 되어 있으면 **하지** 의 자동 배포를 설정 합니다.<br>

- 기존 VM 확장이 있음<br>
    - Monitoring Agent 확장으로 설치 될 때만 단일 작업 영역에 보고 확장 구성을 허용 합니다. Security Center는 사용자 작업 영역에 대한 기존 연결을 재정의하지 않습니다. Security Center "보안" 또는 "securityFree" 솔루션 설치가 완료 된 후에 이미 연결 된 작업 영역에서 VM에서 보안 데이터를 저장 합니다. Security Center는이 프로세스에서 최신 버전으로 확장 버전을 업그레이드할 수 있습니다.  
    - 기존 작업 영역이 어떤 작업 영역으로 데이터를 전송하는지 확인하려면 [Azure Security Center를 사용하여 연결 유효성을 검사](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/)하는 테스트를 실행하세요. 또는 Log Analytics 작업 영역을 열고 하 고 작업 영역을 선택 하 고 VM을 선택한 후 수 있으며 Log Analytics 에이전트 연결 확인 수 있습니다. 
    - Environment가 있는 경우 Log Analytics 에이전트는 클라이언트 워크스테이션에 설치 되어 않았고 목록을 검토 기존 Log Analytics 작업 영역에 보고 [Azure Security Center에서 지원 되는 운영 체제](security-center-os-coverage.md) 되도록 운영 체제는 지원 및 참조 [기존 log analytics 고객](security-center-faq.md#existingloganalyticscust) 자세한 내용은 합니다.
 
### 자동 프로비전 끄기 <a name="offprovisioning"></a>
언제든지 보안 정책에서 이 설정을 해제하여 리소스 자동 프로비전을 끌 수 있습니다. 


1. Security Center 주 메뉴로 돌아가서 보안 정책을 선택합니다.
2. 클릭 **설정 편집** 자동 프로 비전을 사용 하지 않도록 설정 하려는 구독 행에서.
3. **보안 정책 - 데이터 수집** 블레이드의 **자동 프로비저닝** 아래에서 **해제**를 선택합니다.
4. **저장**을 선택합니다.

   ![자동 프로비저닝 사용 안 함][6]

자동 프로비전을 해제하면(끄면) 기본 작업 영역 구성 섹션이 표시되지 않습니다.

이전에 설정된 자동 프로비전을 해제하면:
-   에이전트가 새 VM에 프로비전되지 않습니다.
-   Security Center가 기본 작업 영역에서 데이터 수집을 중지합니다.
 
> [!NOTE]
>  자동 프로비전을 해제해도 Microsoft Monitoring Agent가 프로비전된 Azure VM에서 에이전트가 제거되지는 않습니다. OMS 확장을 제거하는 방법은 [Security Center가 설치한 OMS 확장을 제거하는 방법](security-center-faq.md#remove-oms)을 참조하세요.
>
    
## 수동 에이전트 프로비전 <a name="manualagent"></a>
 
Microsoft Monitoring Agent를 수동으로 설치하는 여러 가지 방법이 있습니다. 수동으로 설치하는 경우 자동 프로비전을 해제해야 합니다.

### <a name="operations-management-suite-vm-extension-deployment"></a>Operations Management Suite VM 확장 배포 

Security Center가 VM의 보안 데이터를 수집하고 권장 사항 및 경고를 제공할 수 있도록 Microsoft Monitoring Agent를 수동으로 설치할 수 있습니다.
1. [자동 프로비전 – 끄기]를 선택합니다.
2. 작업 영역을 만들고 Microsoft Monitoring Agent를 설정할 작업 영역의 가격 책정 계층을 설정합니다.

   a.  Security Center 주 메뉴에서 **보안 정책**을 선택합니다.
     
   b.  에이전트를 연결하려는 작업 영역을 선택합니다. 작업 영역이 Security Center에서 사용하는 구독과 동일한 구독에 있어야 하고 작업 영역에 대한 읽기/쓰기 권한이 있어야 합니다.
       ![작업 영역 선택][8]
3. 가격 책정 계층을 설정합니다.
   ![가격 책정 계층 선택][9] 
   >[!NOTE]
   >작업 영역에서 **보안** 또는 **SecurityCenterFree** 솔루션이 이미 설정된 경우 가격 책정이 자동으로 설정됩니다. 
   > 

4. Resource Manager 템플릿을 사용하여 새 VM에 에이전트를 배포하려면 OMS 가상 머신 확장을 설치합니다.

   a.  [Windows용 OMS 가상 머신 확장 설치](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Linux용 OMS 가상 머신 확장 설치](../virtual-machines/extensions/oms-linux.md)
5. 기존 VM에 확장을 배포하려면 [Azure Virtual Machines에 대한 데이터 수집](../azure-monitor/learn/quick-collect-azurevm.md)의 지침을 따릅니다.

   > [!NOTE]
   > **이벤트 및 성능 데이터 수집**은 선택 사항입니다.
   >
6. PowerShell을 사용하여 확장을 배포하려면 다음 PowerShell 예제를 사용합니다.
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. **Log Analytics**로 이동하여 **고급 설정**을 클릭합니다.
    
      ![로그 분석 설정][11]

   2. **WorkspaceID** 및 **기본 키** 값을 복사합니다.
  
      ![값 복사][12]

   3. 공용 구성 및 프라이빗 구성을 다음 값으로 채웁니다.
     
           $PublicConf = '{
               "workspaceId": "WorkspaceID value"
           }' 
 
           $PrivateConf = '{
               "workspaceKey": "<Primary key value>”
           }' 

      - Windows VM에 설치하는 경우:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - Linux VM에 설치하는 경우:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> PowerShell을 사용하여 Security Center를 온보드하는 방법에 대한 지침은 [PowerShell을 사용하여 Azure Security Center의 온보딩 자동화](security-center-powershell-onboarding.md)를 참조하세요.

## <a name="troubleshooting"></a>문제 해결

-   자동 프로비전 설치 문제를 파악하려면 [에이전트 상태 문제 모니터링](security-center-troubleshooting-guide.md#mon-agent)을 참조하세요.

-  모니터링 에이전트 네트워크 요구 사항을 파악하려면 [모니터링 에이전트 네트워크 요구 사항 문제 해결](security-center-troubleshooting-guide.md#mon-network-req)을 참조하세요.
-   수동 온보딩 문제를 파악하려면 [Operations Management Suite 온보딩 문제 해결 방법](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues)을 참조하세요.

- 모니터링되지 않는 VM 및 컴퓨터 문제를 파악하려면 [모니터링되지 않는 VM 및 컴퓨터](security-center-virtual-machine-protection.md#unmonitored-vms-and-computers)를 참조하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center에서 데이터 수집 및 자동 프로비저닝이 작동하는 방식에 대해 알아보았습니다. 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center FAQ](security-center-faq.md)--서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md)--Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.



<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
[8]: ./media/security-center-enable-data-collection/manual-provision.png
[9]: ./media/security-center-enable-data-collection/pricing-tier.png
[10]: ./media/security-center-enable-data-collection/workspace-selection.png
[11]: ./media/security-center-enable-data-collection/log-analytics.png
[12]: ./media/security-center-enable-data-collection/log-analytics2.png
