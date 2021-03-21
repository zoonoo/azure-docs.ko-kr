---
title: Azure Security Center에서 규제 준수 대시보드 사용
description: Security Center의 규정 준수 대시보드에서 규제 표준을 추가 하 고 제거 하는 방법에 대해 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 768f686889663d9b1af4b88d84b361ac9460a5a0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381735"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>규정 준수 대시보드의 표준 집합 사용자 지정

Azure Security Center는 리소스 구성을 업계 표준, 규정 및 벤치마크의 요구 사항과 지속적으로 비교합니다. **규정 준수 대시보드** 는 특정 규정 준수 요구 사항을 충족 하는 방법에 따라 규정 준수 상태에 대 한 통찰력을 제공 합니다.


## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>규정 준수 표준은 Security Center 어떻게 표시 되나요?

산업 표준, 규정 표준 및 벤치 마크는 Security Center의 규정 준수 대시보드에 표시 됩니다. 각 표준은 Azure Policy에 정의 된 이니셔티브입니다.

대시보드에서 평가로 매핑된 준수 데이터를 확인 하려면 **보안 정책** 페이지 내에서 관리 그룹 또는 구독에 규정 준수 표준을 추가 합니다. Azure Policy 및 이니셔티브에 대해 자세히 알아보려면 [보안 정책 사용](tutorial-security-policy.md)을 참조 하세요.

선택한 범위에 표준 또는 벤치 마크를 할당 한 경우 규정 준수 대시보드에 평가로 매핑된 모든 관련 준수 데이터가 포함 된 표준이 표시 됩니다. 또한 할당 된 모든 표준에 대 한 요약 보고서를 다운로드할 수 있습니다.

Microsoft는 규정 표준 자체를 추적 하 고 시간에 따라 일부 패키지에서 자동으로 해당 범위를 개선 합니다. Microsoft에서 이니셔티브에 대해 새 콘텐츠를 릴리스할 때 표준의 컨트롤에 매핑되는 새 정책이 대시보드에 자동으로 표시 됩니다.


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>Security Center에서 사용할 수 있는 규정 준수 표준은 무엇 인가요?

기본적으로 모든 구독에는 할당 된 **Azure 보안 벤치 마크가** 있습니다. 이는 일반적인 규정 준수 프레임 워크를 기반으로 하는 보안 및 규정 준수 모범 사례에 대 한 Microsoft에서 작성 한 Azure 관련 지침입니다. [Azure 보안 벤치마크에 대해 자세히 알아보세요](../security/benchmarks/introduction.md).

다음과 같은 표준을 추가할 수도 있습니다.

- NIST SP 800-53 R4
- SWIFT CSP CSCF-v2020
- 영국 공식 및 영국 NHS
- 캐나다 연방 PBMM
- Azure CIS 1.1.0

표준은 사용할 수 있게 되 면 대시보드에 추가 됩니다.


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>대시보드에 규정 표준 추가

다음 단계에서는 패키지를 추가하여 지원되는 규정 표준 중 하나를 준수하는지 모니터링하는 방법을 설명합니다.

> [!NOTE]
> 대시보드에 표준을 추가 하려면 구독에서 Azure Defender를 사용 하도록 설정 해야 합니다. 또한 소유자 또는 정책 참여자 인 사용자 에게만 규정 준수 표준을 추가 하는 데 필요한 권한이 있습니다. 

1. Security Center의 사이드바에서 **규정 준수** 를 선택하여 규정 준수 대시보드를 엽니다. 여기에서 현재 선택된 구독에 현재 할당된 규정 준수 표준을 확인할 수 있습니다.   

1. 페이지 맨 위에서 **규정 준수 정책 관리** 를 선택합니다. 정책 관리 페이지가 표시됩니다.

1. 규정 준수 상태를 관리할 구독 또는 관리 그룹을 선택합니다. 

    > [!TIP]
    > 모든 중첩된 리소스에 대해 규정 준수 데이터를 집계하고 추적할 수 있도록 표준이 적용되는 가장 높은 범위를 선택하는 것이 좋습니다. 

1. 조직과 관련된 표준을 추가하려면 **더 많은 표준 추가** 를 클릭합니다. 

1. **규정 준수 표준 추가** 페이지에서 다음을 비롯 하 여 사용 가능한 표준을 검색할 수 있습니다.

    - **NIST SP 800-53 R4**
    - **NIST SP 800 171 R2**
    - **SWIFT CSP CSCF v2020**
    - **UKO 및 영국 NHS**
    - **캐나다 연방 PBMM**
    - **HIPAA HITRUST**
    - **Azure CIS 1.1.0**
    
    ![Azure Security Center의 규정 준수 대시보드에 규제 표준 추가](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. **추가** 를 선택 하 고 범위, 매개 변수, 수정 등의 특정 이니셔티브에 필요한 모든 세부 정보를 입력 합니다.

1. Security Center의 사이드바에서 **규정 준수** 를 다시 선택하여 규정 준수 대시보드로 돌아갑니다.

    새 표준이 업계 및 규정 표준 목록에 표시됩니다. 

    > [!NOTE]
    > 새로 추가된 표준이 규정 준수 대시보드에 표시되는 데 몇 시간 정도 걸릴 수 있습니다.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="규정 준수 대시보드" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

## <a name="remove-a-standard-from-your-dashboard"></a>대시보드에서 표준 제거

제공 된 규정 표준이 조직과 관련이 없는 경우 UI에서이를 제거 하는 간단한 프로세스입니다. 이를 통해 규정 준수 대시보드를 추가로 사용자 지정 하 고 해당 하는 표준에만 집중할 수 있습니다.

표준을 제거 하려면:

1. Security Center의 메뉴에서 **보안 정책** 을 선택 합니다.

1. 표준을 제거 하려는 관련 구독을 선택 합니다.

    > [!NOTE]
    > 관리 그룹이 아닌 구독에서 standard를 제거할 수 있습니다. 

    보안 정책 페이지가 열립니다. 선택한 구독에 대해 기본 정책, 산업 및 규제 표준 및 사용자가 만든 사용자 지정 이니셔티브를 표시 합니다.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Azure Security Center의 규정 준수 대시보드에서 규제 표준 제거":::

1. 제거 하려는 표준에 대해 **사용 안 함** 을 선택 합니다. 확인 창이 표시 됩니다.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="선택한 규제 표준을 정말로 제거할지 확인 합니다.":::

1. **예** 를 선택합니다. 표준이 제거 됩니다. 


## <a name="next-steps"></a>다음 단계

이 문서에서는 규정 및 업계 표준 준수를 모니터링 하는 **규정 준수 표준을 추가** 하는 방법을 알아보았습니다.

관련 자료는 다음 페이지를 참조 하세요.

- [Azure Security Benchmark](../security/benchmarks/introduction.md)
- [보안 센터 규정 준수 대시보드](security-center-compliance-dashboard.md) -Security Center 및 외부 도구를 사용 하 여 규정 준수 데이터를 추적 하 고 내보내는 방법을 알아봅니다.
- [보안 정책 작업](tutorial-security-policy.md)