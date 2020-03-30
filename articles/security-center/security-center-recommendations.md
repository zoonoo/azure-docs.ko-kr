---
title: Azure Security Center의 보안 권장 사항
description: 이 문서에서는 Azure Security Center의 권장 사항이 Azure 리소스를 보호하고 보안 정책을 준수하는 데 어떤 도움이 되는지 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2019
ms.author: memildin
ms.openlocfilehash: 408b0f020be72b8e6b10dd6c97298afda1b91360
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245305"
---
# <a name="security-recommendations-in-azure-security-center"></a>Azure Security Center의 보안 권장 사항 
이 항목에서는 Azure 리소스를 보호하는 데 도움이 되는 Azure 보안 센터의 권장 사항을 보고 이해하는 방법을 설명합니다.

> [!NOTE]
> 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다.  이 문서는 단계별 가이드가 아닙니다.
>

## <a name="what-are-security-recommendations"></a>보안 권장 사항이란?

권장 사항은 리소스를 보호하기 위해 수행할 작업입니다.

보안 센터는 Azure 리소스의 보안 상태를 주기적으로 분석하여 잠재적인 보안 취약점을 식별합니다. 그런 다음 제거하는 방법에 대한 권장 사항을 제공합니다.

각 권장 사항은 다음과 같은 사항을 제공합니다.

- 권장되는 내용에 대한 간략한 설명입니다.
- 권장 사항을 구현하기 위해 수행할 수정 단계입니다. <!-- In some cases, Quick Fix remediation is available. -->
- 권장 작업을 수행해야 하는 리소스입니다.
- 이 권장 사항을 구현하면 보안 점수가 올라갈 수 있는 금액인 **보안 점수 영향입니다.**

## <a name="monitor-recommendations"></a>권장 사항 모니터링<a name="monitor-recommendations"></a>

보안 센터는 리소스의 보안 상태를 분석하여 잠재적인 취약점을 식별합니다. **개요** 아래의 **권장 사항** 타일에는 보안 센터에서 식별한 총 권장 사항 수가 표시됩니다.

![보안 센터 개요](./media/security-center-recommendations/asc-overview.png)

1. **개요**에서 **권장 사항 타일을** 선택합니다. **권장 사항** 목록이 열립니다.

      ![권장 사항 보기](./media/security-center-recommendations/view-recommendations.png)

    권장 사항을 필터링할 수 있습니다. 권장 사항을 필터링하려면 **권장 사항** 블레이드에서 **필터**를 선택합니다. **필터** 블레이드가 열리면 확인하려는 심각도 및 상태 값을 선택합니다.

   * **권장 사항**: 권장 사항입니다.
   * **보안 점수 영향**: 보안 센터에서 보안 권장 사항을 사용하여 생성된 점수와 고급 알고리즘을 적용하여 각 권장 사항이 얼마나 중요한지 결정합니다. 자세한 내용은 [보안 점수 계산을](security-center-secure-score.md#secure-score-calculation)참조하십시오.
   * **리소스**:이 권장 사항이 적용되는 리소스를 나열합니다.
   * **상태 막대**: 특정 권장 사항의 심각도를 설명합니다.
       * **높음(빨간색)**: 의미 있는 리소스(예: 응용 프로그램, VM 또는 네트워크 보안 그룹)에 취약점이 있으므로 주의가 필요합니다.
       * **중간(주황색)**: 취약점이 존재하며 이를 제거하거나 프로세스를 완료하려면 중요하지 않거나 추가 단계가 필요합니다.
       * **낮음(파란색)**: 해결해야 하지만 즉각적인 주의가 필요하지 않은 취약점이 있습니다. (기본적으로 낮은 권장 사항은 표시되지 않지만 표시하려는 경우 낮은 권장 사항으로 필터링할 수 있습니다.) 
       * **건강 (녹색)**:
       * **사용할 수 없음(회색)**:

1. 각 권장 사항의 세부 정보를 보려면 권장 사항을 클릭합니다.

    ![권장 사항 세부 정보](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Azure 리소스에 대한 [클래식 및 리소스 관리자 배포 모델을](../azure-classic-rm.md) 참조하십시오.
 
## <a name="next-steps"></a>다음 단계

이 문서에서는 보안 센터의 보안 권장 사항을 소개했습니다. 권장 사항을 수정하는 방법을 알아보려면 다음을 수행하십시오.

* [권장 사항 해결](security-center-remediate-recommendations.md) — Azure 구독 및 리소스 그룹에 대한 보안 정책을 구성하는 방법을 알아봅니다.
