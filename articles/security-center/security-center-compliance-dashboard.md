---
title: '자습서: 규정 준수 검사 - Azure Security Center'
description: '자습서: Azure Security Center를 사용하여 규정 준수를 개선하는 방법을 알아봅니다.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: memildin
ms.openlocfilehash: 284a7f532ed918397fe1cfcf3458bbc6fb0bdd32
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739011"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>자습서: 규정 준수 개선

Azure Security Center를 통해 **규정 준수 대시보드** 를 사용하여 규정 준수 요구 사항을 충족하기 위한 프로세스를 간소화할 수 있습니다. 

Security Center는 하이브리드 클라우드 환경을 지속적으로 평가하여 구독에 적용되는 표준의 제어 및 모범 사례에 따라 위험 요인을 분석합니다. 대시보드는 이러한 표준 준수 상태를 반영합니다. 

Azure 구독에서 Security Center를 사용하도록 설정하면 [Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction)가 해당 구독에 자동으로 할당됩니다. 널리 인정받는 이 벤치마크는 클라우드 중심 보안에 초점을 맞춘 [CIS(Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) 및 [NIST(National Institute of Standards and Technology)](https://www.nist.gov/)의 컨트롤을 기반으로 합니다.

규정 준수 대시보드에는 선택한 표준 및 규정에 대한 환경 내의 모든 평가 상태가 표시됩니다. 권장 사항에 따라 작업하고 환경에서 위험 요소를 줄임에 따라 규정 준수 상태가 개선됩니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 규정 준수 대시보드를 사용하여 규정 준수 평가
> * 권장 사항에 따라 작업을 수행하여 준수 상태를 개선합니다
> * 규정 준수 상태 변경에 대한 경고를 설정합니다.
> * 규정 준수 데이터를 연속 스트림 및 주별 스냅샷으로 내보냅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서 설명하는 기능을 단계별로 실행하려면 다음을 수행합니다.

- [Azure Defender](azure-defender.md)를 사용하도록 설정해야 합니다. 30일 동안 무료로 Azure Defender를 사용해 볼 수 있습니다.
- 정책 규정 준수 데이터에 대한 읽기 권한자 액세스 권한이 있는 계정으로 로그인해야 합니다(**보안 읽기 권한자** 로는 충분하지 않음). 구독에 대한 **글로벌 읽기 권한자** 의 역할이 작동합니다. 최소한 **리소스 정책 기여자** 및 **보안 관리자** 역할이 할당되어야 합니다.

##  <a name="assess-your-regulatory-compliance"></a>규정 준수 평가

규정 준수 대시보드에는 선택한 규정 준수 표준이 모든 해당 요구 사항과 함께 표시되며, 여기서 지원되는 요구 사항은 적용 가능한 보안 평가에 매핑됩니다. 이러한 평가의 상태는 표준 준수를 반영합니다.

규정 준수 대시보드를 사용하면 선택한 표준 및 규정의 준수에 따른 차이에 주의를 집중할 수 있습니다. 또한 이러한 집중적인 보기를 통해 동적 클라우드 및 하이브리드 환경 내에서 시간이 지남에 따라 규정 준수를 지속적으로 모니터링할 수 있습니다.

1. Security Center 메뉴에서 **규정 준수** 를 선택합니다.

    화면 위쪽에는 지원되는 규정 준수 집합과 함께 규정 준수 상태에 대한 개요가 있는 대시보드가 있습니다. 전반적인 규정 준수 점수 및 각 표준과 관련된 통과와 실패 평가의 수가 표시됩니다.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="규정 준수 대시보드" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. (1)과 관련된 규정 준수 표준에 대한 탭을 선택합니다. 표준이 (2)에 적용되는 구독 및 해당 표준 (3)에 대한 모든 컨트롤의 목록이 표시됩니다. 해당 컨트롤의 경우 해당 컨트롤 (4)와 관련된 통과 및 실패 평가의 세부 정보와 영향을 받는 리소스 수 (5)를 볼 수 있습니다. 일부 컨트롤은 회색으로 표시됩니다. 이러한 컨트롤에는 연결된 Security Center 평가가 없습니다. 고객의 요구 사항을 확인하고 해당 환경에서 평가합니다. 일부는 프로세스와 관련되며 기술적이지 않습니다.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="특정 표준 규정 준수에 대한 세부 정보 살펴보기":::

1. 특정 표준에 대한 현재 규정 준수 상태가 요약된 PDF 보고서를 생성하려면 **보고서 다운로드** 를 선택합니다.

    이 보고서는 Security Center 평가 데이터를 기반으로 선택한 표준의 규정 준수 상태에 대한 요약 정보를 제공합니다. 해당 특정 표준의 컨트롤에 따라 보고서가 구성됩니다. 이 보고서는 관련자와 공유할 수 있으며, 내부 및 외부 감사자에게 증거 자료를 제공할 수 있습니다.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="규정 준수 보고서 다운로드":::

## <a name="improve-your-compliance-posture"></a>준수 상태 개선

규정 준수 대시보드의 정보를 사용하여 대시보드 내에서 직접 권장 사항을 해결함으로써 규정 준수 상태를 향상시킵니다.

1.  대시보드에 표시되는 실패 평가를 선택하여 해당 권장 사항에 대한 세부 정보를 봅니다. 각 권장 사항에는 문제를 해결하기 위한 수정 단계 세트가 포함됩니다.

1.  특정 리소스를 선택하여 자세한 세부 정보를 보고 해당 리소스에 대한 권장 사항을 해결합니다. <br>예를 들어 **Azure CIS 1.1.0** 표준에서 **가상 머신에서 디스크 암호화를 적용해야 합니다.** 권장 사항을 선택합니다.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="표준에서 권장 사항을 선택하면 권장 사항 세부 정보 페이지로 바로 연결됩니다.":::

1. 다음 예에서 권장 사항 세부 정보 페이지에서 **작업 수행** 을 선택하면 Azure Portal의 Azure Virtual Machine 페이지가 표시됩니다. 여기서는 **보안** 탭에서 암호화를 사용하도록 설정할 수 있습니다.

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="권장 사항 세부 정보 페이지의 작업 가져오기 단추를 클릭하면 수정 옵션으로 연결됩니다.":::

    권장 사항을 적용하는 방법에 대한 자세한 내용은 [Azure Security Center에서 보안 권장 사항 구현](security-center-recommendations.md)을 참조하세요.

1.  권장 사항을 해결하기 위한 작업을 수행하면 규정 준수 점수가 향상되므로 규정 준수 대시보드 보고서에 결과가 표시됩니다.

    > [!NOTE]
    > 평가는 약 12시간마다 실행되므로 다음 관련 평가가 실행된 후에만 규정 준수 데이터에 미치는 영향이 표시됩니다.


## <a name="export-your-compliance-status-data"></a>규정 준수 상태 데이터 내보내기

사용자 환경에서 다른 모니터링 도구를 사용하여 규정 준수 상태를 추적하려는 경우 이를 간단하게 수행할 수 있는 내보내기 메커니즘이 Security Center에 포함됩니다. 선택한 데이터를 Azure Event Hub 또는 Log Analytics 작업 영역으로 보내도록 **연속 내보내기** 를 구성합니다.

연속 내보내기 데이터를 Azure Event Hub 또는 Log Analytics 작업 영역에 사용합니다.

- 모든 규정 준수 데이터를 **연속 스트림** 으로 내보냅니다.

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-stream.png" alt-text="규정 준수 데이터의 스트림을 지속적으로 내보내기" lightbox="media/security-center-compliance-dashboard/export-compliance-data-stream.png":::

- 규정 준수 데이터의 **주별 스냅샷** 을 내보냅니다.

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="규정 준수 데이터의 주별 스냅샷을 지속적으로 내보내기" lightbox="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png":::

또한 규정 준수 대시보드에서 규정 준수 데이터의 **PDF/CSV 보고서** 를 직접 내보낼 수 있습니다.

:::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-report.png" alt-text="규정 준수 데이터를 PDF 또는 CSV 보고서로 내보내기" lightbox="media/security-center-compliance-dashboard/export-compliance-data-report.png":::

[Security Center 데이터 연속 내보내기](continuous-export.md)에서 자세히 알아보세요.


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>규정 준수 상태 변경 시 워크플로 자동화 실행

Security Center의 워크플로 자동화 기능은 규정 준수 평가 중 하나에서 상태가 변경될 때마다 Logic Apps를 트리거할 수 있습니다.

예를 들어 규정 준수 평가가 실패하는 경우 Security Center에서 특정 사용자에게 이메일을 보내도록 할 수 있습니다. 먼저 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 사용하여 논리 앱을 만든 다음, [Security Center 트리거에 대한 응답 자동화](workflow-automation.md)에서 설명한 대로 새 워크플로 자동화에서 트리거를 설정해야 합니다.

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="규정 준수 평가 변경 사항을 사용하여 워크플로 자동화 트리거" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::




## <a name="faq---regulatory-compliance-dashboard"></a>FAQ - 규정 준수 대시보드

- [규정 준수 대시보드에서 지원되는 표준은 무엇인가요?](#what-standards-are-supported-in-the-compliance-dashboard)
- [일부 컨트롤이 회색으로 표시되는 이유는 무엇인가요?](#why-do-some-controls-appear-grayed-out)
- [대시보드에서 PCI DSS, ISO 27001 또는 SOC2 TSP와 같은 기본 제공 표준을 제거하려면 어떻게 해야 하나요?](#how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard)
- [권장 사항에 따라 제안 내용이 변경되었지만 대시보드에 반영되지 않습니다.](#i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard)
- [규정 준수 대시보드에 액세스하는 데 필요한 권한은 무엇인가요?](#what-permissions-do-i-need-to-access-the-compliance-dashboard)
- [규정 준수 대시보드가 나에게 로드되지 않음](#the-regulatory-compliance-dashboard-isnt-loading-for-me)
- [내 대시보드의 표준에 따라 통과 및 실패한 컨트롤의 보고서를 어떻게 볼 수 있나요?](#how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard)
- [규정 준수 데이터가 포함된 보고서를 PDF 이외의 형식으로 다운로드하려면 어떻게 해야 하나요?](#how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf)
- [규정 준수 대시보드의 일부 정책에 대한 예외를 어떻게 만들 수 있나요?](#how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard)
- [규정 준수 대시보드를 사용하려면 어떤 Azure Defender 플랜 또는 라이선스가 필요한가요?](#what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard)

### <a name="what-standards-are-supported-in-the-compliance-dashboard"></a>규정 준수 대시보드에서 지원되는 표준은 무엇인가요?
기본적으로 규정 준수 대시보드는 Azure Security Benchmark를 표시합니다. Azure Security Benchmark는 일반적인 규정 준수 프레임워크를 기반으로 하는 보안 및 규정 준수 모범 사례에 대해 Microsoft에서 작성한 Azure 관련 지침입니다. [Azure Security Benchmark 소개](../security/benchmarks/introduction.md)에서 자세히 알아보세요.

다른 표준을 준수하는지 추적하려면 대시보드에 명시적으로 추가해야 합니다.
 
Azure CIS 1.1.0(신규), NIST SP 800-53 R4, NIST SP 800-171 R2, SWIFT CSP CSCF-v2020, 영국 공식 및 영국 NHS, HIPAA HITRUST, 캐나다 연방 PBMM, ISO 27001, SOC2-TSP 및 PCI DSS 3.2.1과 같은 표준을 추가할 수 있습니다.  
 
더 많은 표준이 대시보드에 추가되고 [규정 준수 대시보드에서 표준 집합 사용자 지정](update-regulatory-compliance-packages.md) 정보에 포함됩니다.

### <a name="why-do-some-controls-appear-grayed-out"></a>일부 컨트롤이 회색으로 표시되는 이유는 무엇인가요?
대시보드의 각 규정 준수 표준에 대해 표준 컨트롤 목록이 있습니다. 해당 컨트롤의 경우 통과 및 실패 평가의 세부 정보를 볼 수 있습니다.

일부 컨트롤은 회색으로 표시됩니다. 이러한 컨트롤에는 연결된 Security Center 평가가 없습니다. 일부는 프로시저 또는 프로세스와 관련될 수 있으므로 Security Center에서 확인할 수 없습니다. 일부 경우에는 자동화된 정책 또는 평가가 아직 구현되지 않지만 향후 포함될 예정입니다. 그리고 일부 컨트롤은 [클라우드의 공유 책임](../security/fundamentals/shared-responsibility.md)에 설명된 대로 플랫폼 책임일 수 있습니다. 

### <a name="how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard"></a>대시보드에서 PCI DSS, ISO 27001 또는 SOC2 TSP와 같은 기본 제공 표준을 제거하려면 어떻게 해야 하나요? 
규정 준수 대시보드를 사용자 지정하고 자신에게 적용되는 표준에만 집중하려면 조직과 관련이 없는 표시된 모든 규정 표준을 제거할 수 있습니다. 표준을 제거하려면 [대시보드에서 표준 제거](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard)의 지침을 따르세요.

### <a name="i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard"></a>권장 사항에 따라 제안 내용이 변경되었지만 대시보드에 반영되지 않습니다.
권장 사항을 해결하기 위한 조치를 취한 후, 규정 준수 데이터의 변화를 확인하기 위해 12시간 정도 기다립니다. 평가는 약 12시간마다 실행되므로 평가가 실행된 후에만 규정 준수 데이터에 미치는 영향을 확인할 수 있습니다.
 
### <a name="what-permissions-do-i-need-to-access-the-compliance-dashboard"></a>규정 준수 대시보드에 액세스하는 데 필요한 권한은 무엇인가요?
규정 준수 데이터를 보려면 정책 규정 준수 데이터에 대한 **읽기 권한자** 이상의 액세스 권한도 있어야 합니다. 보안 읽기 권한자만으로는 충분하지 않습니다. 구독의 글로벌 읽기 권한자라면 그것으로도 충분합니다.

대시보드에 액세스하고 표준을 관리하기 위한 최소 역할 집합은 **리소스 정책 기여자** 및 **보안 관리자** 입니다.


### <a name="the-regulatory-compliance-dashboard-isnt-loading-for-me"></a>규정 준수 대시보드가 나에게 로드되지 않음
규정 준수 대시보드를 사용하려면 Azure Security Center 구독 수준에서 Azure Defender를 사용하도록 설정해야 합니다. 대시보드가 올바르게 로드되지 않으면 다음 단계를 시도합니다.

1. 브라우저 캐시를 지우세요.
1. 다른 브라우저를 사용해보세요.
1. 다른 네트워크 위치에서 대시보드를 열어보세요.


### <a name="how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard"></a>내 대시보드의 표준에 따라 통과 및 실패한 컨트롤의 보고서를 어떻게 볼 수 있나요?
기본 대시보드에서 대시보드의 (1) '상위 4' 최저 규정 준수 표준에 대한 컨트롤 통과 및 실패 보고서를 볼 수 있습니다. 모든 통과/실패 컨트롤 상태를 보려면 (2) ***x* 모두 표시** 를 선택합니다(여기서 x는 추적 중인 표준 수). 컨텍스트 평면에는 추적된 표준의 모든 항목에 대해 규정 준수 상태가 표시됩니다.

:::image type="content" source="media/security-center-compliance-dashboard/summaries-of-compliance-standards.png" alt-text="규정 준수 대시보드의 요약 섹션":::


### <a name="how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf"></a>규정 준수 데이터가 포함된 보고서를 PDF 이외의 형식으로 다운로드하려면 어떻게 해야 하나요?
**보고서 다운로드** 를 선택하는 경우 표준 및 형식(PDF 또는 CSV)을 선택합니다. 결과 보고서에는 포털의 필터에서 선택한 현재 구독 집합을 반영합니다.

- PDF 보고서에는 선택한 표준의 요약 상태가 표시됩니다.
- CSV 보고서는 각 컨트롤과 연결된 정책과 관련이 있으므로 리소스별로 자세한 결과를 제공합니다.

현재는 사용자 지정 정책에 대한 보고서 다운로드가 지원되지 않습니다. 제공된 규제 표준에만 지원됩니다.


### <a name="how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard"></a>규정 준수 대시보드의 일부 정책에 대한 예외를 어떻게 만들 수 있나요?
Security Center에 기본 제공되고 보안 점수에 포함된 정책의 경우 [보안 점수에서 리소스 및 권장 사항 제외](exempt-resource.md)에 설명된 대로 포털에서 직접 하나 이상의 리소스에 대한 예외를 만들 수 있습니다.

다른 정책의 경우 [Azure 정책 예외 구조](../governance/policy/concepts/exemption-structure.md)의 지침에 따라 정책 자체에서 직접 예외를 만들 수 있습니다.


### <a name="what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard"></a>규정 준수 대시보드를 사용하려면 어떤 Azure Defender 플랜 또는 라이선스가 필요한가요?
Azure 리소스 유형에서 사용하도록 설정된 Azure Defender 패키지가 있는 경우 Security Center에서 모든 데이터와 함께 규정 준수 대시보드에 액세스할 수 있습니다.





## <a name="next-steps"></a>다음 단계

이 자습서에서는 Security Center의 규정 준수 대시보드를 사용하여 다음을 수행하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * 사용자에게 중요한 표준 및 규정에 대한 준수 상태를 확인하고 모니터링합니다.
> * 관련 권장 사항을 해결하고 준수 점수 개선을 확인하여 준수 상태 개선

규정 준수 대시보드는 규정 준수 프로세스를 크게 간소화하고, Azure 및 다중 하이브리드 환경에 대한 규정 준수 증거를 수집하는 데 필요한 시간을 크게 줄일 수 있습니다.

자세히 알아보려면 다음 관련 페이지를 참조하세요.

- [규정 준수 대시보드의 표준 집합 사용자 지정](update-regulatory-compliance-packages.md) - 규정 준수 대시보드에 표시되는 표준을 선택하는 방법을 알아봅니다. 
- [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md) - Azure 리소스 보호에 도움이 되도록 Security Center에서 권장 사항을 사용하는 방법을 알아봅니다.