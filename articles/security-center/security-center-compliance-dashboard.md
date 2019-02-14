---
title: Azure Security Center를 사용하여 규정 준수 개선 | Microsoft Docs
description: '자습서: Azure Security Center를 사용하여 규정 준수를 개선하는 방법을 알아봅니다.'
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 2e73cbd1de4336a08e26a609dfe704da0a068860
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106007"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>자습서: 규정 준수 개선
---

Azure Security Center를 통해 규정 준수 대시보드를 사용하여 규정 준수 요구 사항을 충족하기 위한 프로세스를 간소화할 수 있습니다. 대시보드에서 Security Center는 Azure 환경의 지속적인 평가에 기반하여 준수 상태에 대한 인사이트를 제공합니다. Security Center에서 수행한 평가는 보안 모범 사례에 따라 하이브리드 클라우드 환경의 위험 요소를 분석합니다. 이러한 평가는 지원되는 표준 세트에서 준수 제어에 매핑됩니다. 규정 준수 대시보드에서 특정 표준 또는 규정의 컨텍스트에서 사용자 환경 내의 이러한 모든 평가의 상태에 대한 명확한 시각을 갖습니다. 권장 사항에 따라 작업하고 환경에서 위험 요소를 줄임에 따라 준수 상태 개선을 확인할 수 있습니다.

이 자습서에서는 다음 방법을 알아봅니다.

-   규정 준수 대시보드를 사용하여 규정 준수를 평가합니다.

-   권장 사항에 따라 작업을 수행하여 준수 상태를 개선합니다

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/) 을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 자습서에서 설명하는 기능을 단계별로 실행하려면 Security Center의 표준 가격 책정 계층이 있어야 합니다. 비용 없이 Security Center 표준을 사용해 볼 수 있습니다.
자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요. [Security Center 표준에 Azure 구독 온보딩](https://docs.microsoft.com/azure/security-center/security-center-get-started) 빠른 시작을 통해 표준 계층으로 업그레이드하는 방법을 안내합니다.

##  <a name="assess-your-regulatory-compliance"></a>규정 준수 평가

Security Center는 지속적으로 리소스 구성을 평가하여 보안 문제 및 취약성을 식별합니다. 이러한 평가는 보안 예방 조치를 향상시키는 데 중점을 두는 권장 사항으로 표시됩니다. 규정 준수 대시보드에서 지원되는 요구 사항이 적용 가능한 보안 평가에 매핑되는 모든 해당 요구 사항이 있는 준수 표준 세트를 볼 수 있습니다. 이를 통해 이러한 평가의 상태를 기반으로 하는 표준에 대한 준수 상태를 볼 수 있습니다.

규정 준수 대시보드 보기는 사용자에게 중요한 표준 또는 규정에 따라 차이에 주목하는 데 도움이 될 수 있습니다. 이 초점이 맞춰진 보기를 사용하면 동적 클라우드 및 하이브리드 환경 내에서 시간이 지남에 따라 준수 점수를 지속적으로 모니터링할 수 있습니다.

>[!NOTE]
> 현재 지원되는 규정 표준은 다음과 같습니다. Azure CIS, PCI DSS 3.2, ISO 27001 및 SOC TSP 개발하는 대로 추가 표준이 대시보드에 반영됩니다.

1.  Security Center 기본 메뉴의 **정책 및 준수** 아래에서 **규정 준수 대시보드**를 선택합니다. <br>
화면 맨 위에 지원되는 규정 준수 세트로 준수 상태의 개요가 있는 대시보드가 표시됩니다. 전반적인 준수 점수 및 각 표준과 관련된 통과와 실패 평가의 수를 볼 수 있습니다.

    ![컴퓨터 설명 높은 신뢰도](./media/security-center-compliance-dashboard/compliance-dashboard.png)


2.  사용자와 관련된 준수 표준에 대한 탭을 선택합니다. 해당 표준에 대한 모든 컨트롤의 목록이 표시됩니다. 해당 컨트롤의 경우 해당 컨트롤과 관련된 통과 및 실패 평가의 세부 정보를 볼 수 있습니다. 일부 컨트롤은 회색으로 표시됩니다. 이러한 컨트롤에는 연결된 Security Center 평가가 없습니다. 이에 대한 요구 사항을 분석하고 자신의 환경에서 평가해야 합니다. 일부는 프로세스와 관련되며 기술적이지 않습니다.

    ![준수 탭](./media/security-center-compliance-dashboard/compliance-pci.png)

3.  **모두** 탭을 선택하여 관련된 모든 Security Center 권장 사항의 보기 및 관련된 해당 표준을 봅니다. 이 보기는 특정 권장 사항에 의해 영향을 받는 모든 다른 표준을 식별하는 데 유용할 수 있습니다. <br> 잠재적으로 이 보기를 사용하여 해결해야 하는 권장 사항의 우선 순위를 지정할 수 있습니다. 예를 들어 권장 사항 **구독에 대한 소유자 권한이 있는 계정에 대해 MFA 사용**이 여러 리소스에서 실패하고 여러 표준과 연결된 경우 이 권장 사항을 해결하는 것은 전반적인 준수 점수에 높은 영향을 미치게 됩니다.

    ![준수 점수 영향](./media/security-center-compliance-dashboard/compliance-all-tabs.png)

## <a name="improve-your-compliance-posture"></a>준수 상태 개선


규정 준수 대시보드에서 정보를 지정하여 대시보드 내에서 직접 권장 사항을 해결하여 준수 상태를 개선할 수 있습니다.

1.  대시보드에 표시되는 실패 평가를 클릭하여 해당 권장 사항에 대한 세부 정보를 봅니다. 각 권장 사항에는 문제를 해결하기 위해 수행해야 하는 수정 단계 세트가 포함되어 있습니다.

2.  특정 리소스를 선택하여 자세한 세부 정보를 보고 해당 리소스에 대한 권장 사항을 해결할 수 있습니다. <br>예를 들어 **Azure CIS 표준** 탭에서 권장 사항 **스토리지 계정에 대한 보안 전송 필요**를 클릭할 수 있습니다.

    ![준수 권장 사항](./media/security-center-compliance-dashboard/compliance-recommendation.png)

3. 권장 사항 정보를 클릭하고 비정상 리소스를 선택하면 Azure Portal 내에서 **보안 스토리지 전송** 활성화의 경험으로 직접 이동합니다.<br>권장 사항을 적용하는 방법에 대한 자세한 내용은 [Azure Security Center에서 보안 권장 사항 구현](security-center-recommendations.md)을 참조하세요.

    ![준수 권장 사항](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

4.  권장 사항을 해결하기 위한 작업을 수행한 후 준수 점수가 개선되므로 준수 대시보드 보고서에 영향이 나타납니다.

    > [!NOTE]
    > 평가는 약 12시간마다 실행되므로 평가가 실행된 후에만 준수 데이터에 영향이 표시됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Security Center의 규정 준수 대시보드를 사용하여 다음을 수행하는 것에 대해 알아보았습니다.

-   사용자에게 중요한 표준 및 규정에 대한 준수 상태 보기 및 모니터링

-   관련 권장 사항을 해결하고 준수 점수 개선을 확인하여 준수 상태 개선

규정 준수 대시보드는 준수 프로세스를 크게 간소화하고, Azure 및 하이브리드 환경에 대한 준수 증거를 수집하는 데 필요한 시간을 크게 절감할 수 있습니다.

Security Center에 대해 자세히 알아보려면 다음 항목을 참조하세요.

-   [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md)--Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.

-   [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md) - Azure 리소스 보호에 도움이 되도록 Azure Security Center에서 권장 사항을 사용하는 방법을 알아봅니다.

-   [Azure Security Center에서 보안 점수 개선](security-center-secure-score.md) - 보안 상태를 개선하기 위해 취약점 및 보안 권장 사항의 우선 순위를 지정하는 방법을 알아봅니다.

-   [Azure Security Center FAQ](security-center-faq.md)--서비스 사용에 관한 질문과 대답을 찾습니다.
