---
title: Azure 보안 센터 규정 준수 대시보드에서 동적 규정 준수 모니터링으로 업데이트하는 방법 | 마이크로 소프트 문서
description: 규정 준수 패키지 업데이트(미리 보기)
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 4080825bbb1f6c274f5b5aafd28e8c672148b98f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159288"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard-preview"></a>규정 준수 대시보드에서 동적 규정 준수 패키지로 업데이트(미리 보기)

Azure Security Center는 리소스 구성을 업계 표준, 규정 및 벤치마크의 요구 사항과 지속적으로 비교합니다. **규정 준수 대시보드는** 특정 규정 준수 제어 및 요구 사항을 충족하는 방법에 따라 규정 준수 자세에 대한 통찰력을 제공합니다.

규정 준수 상태를 추적할 수 있는 한 가지 표준은 [Azure CIS 1.1.0입니다(보다](https://www.cisecurity.org/benchmark/azure/) 공식적으로는 "CIS Microsoft Azure Foundations 벤치마크 버전 1.1.0"). 

규정 준수 대시보드에 처음 나타나는 Azure CIS의 표현은 보안 센터에 포함된 정적 규칙 집합에 의존합니다.

동적 **컴플라이언스 패키지(미리 보기)** 기능을 통해 보안 센터는 시간이 지남에 따라 업계 표준의 적용 범위를 자동으로 개선합니다. 규정 준수 패키지는 기본적으로 Azure 정책에 정의된 이니셔티브입니다. 선택한 범위(구독, 관리 그룹 등)에 할당할 수 있습니다. 대시보드에서 평가로 매핑된 규정 준수 데이터를 보려면 보안 정책 내에서 관리 그룹 또는 구독에 규정 준수 패키지를 추가합니다. 규정 준수 패키지를 추가하면 선택한 범위에 규정 준수 이니셔티브가 효과적으로 할당됩니다. 이러한 방식으로 대시보드에서 새로 게시된 규제 이니셔티브를 규정 준수 표준으로 추적할 수 있습니다. Microsoft가 이니셔티브에 대한 새 콘텐츠(표준의 더 많은 컨트롤에 매핑하는 새 정책)를 릴리스하면 추가 콘텐츠가 대시보드에 자동으로 나타납니다.

Azure CIS 벤치마크인 **Azure CIS 1.1.0(새)에**대한 동적 규정 준수 패키지는 원래 *정적* 버전에서 다음을 수행하여 개선됩니다.

* 더 많은 정책 포함
* 추가된 새 커버리지로 자동으로 업데이트 

아래에 설명된 대로 새 동적 패키지로 업데이트합니다.

## <a name="adding-a-dynamic-compliance-package"></a>동적 규정 준수 패키지 추가

다음 단계에서는 Azure CIS 벤치마크 v1.1.0의 준수 여부를 모니터링하기 위한 동적 패키지를 추가하는 방법을 설명합니다.   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Azure CIS 1.1.0(새) 동적 규정 준수 패키지로 업데이트 

1. 보안 **정책** 페이지를 엽니다. 이 페이지에는 관리 그룹, 구독, 작업 영역 및 관리 그룹 구조의 수가 표시됩니다.

1. 규정 준수 상태를 관리하려는 구독 또는 관리 그룹을 선택합니다. 모든 중첩된 리소스에 대해 규정 준수 데이터를 집계하고 추적할 수 있도록 표준이 적용되는 가장 높은 범위를 선택하는 것이 좋습니다. 

1. 업계 & 규제 표준(미리 보기) 섹션에서 Azure CIS 1.1.0을 새 콘텐츠에 대해 업데이트할 수 있습니다. **지금 업데이트를**클릭합니다. 

1. 선택적으로 **더 많은 표준 추가를** 클릭하여 규정 준수 표준 추가 페이지를 **엽니다.** 거기에서 **NisT SP 800-53 R4,** **SWIFT CSCf-v2020,** **UKO 및 UKNHS**및 **캐나다 PBMM과**같은 기타 규정 준수 표준에 대한 **Azure CIS 1.1.0(신규)** 및 동적 패키지를 수동으로 검색할 수 있습니다.
    
    > [!TIP]
    > 소유자 또는 정책 참여자인 사용자만 규정 준수 표준을 추가하는 데 필요한 권한이 있습니다. 

    ![Azure Security Center의 규정 준수 대시보드에 규정 패키지 추가](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. 보안 센터의 사이드바에서 **규정 준수를** 선택하여 규정 준수 대시보드를 엽니다. 
    * Azure CIS 1.1.0(새)이 이제 업계 & 규제 표준 목록에 표시됩니다. 
    * Azure CIS 1.1.0 준수의 원래 *정적* 보기도 함께 유지됩니다. 나중에 자동으로 제거될 수 있습니다.

    > [!NOTE]
    > 새로 추가된 표준이 규정 준수 대시보드에 표시되려면 몇 시간이 걸릴 수 있습니다.


    [![이전 및 새 Azure CIS를 보여주는 규정 준수 대시보드](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>다음 단계

이 아티클에서는 다음에 대해 알아보았습니다.

* 규정 준수 대시보드에 표시된 표준을 새로운 *동적* 패키지로 **업그레이드하는** 방법
* 규정 **준수 패키지를 추가하여** 추가 표준 준수 여부를 모니터링하는 방법. 

기타 관련 자료는 다음 문서를 참조하십시오. 

- [보안 센터 규정 준수 대시보드](security-center-compliance-dashboard.md)
- [보안 정책 작업](tutorial-security-policy.md)
- [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md) - Azure 리소스 보호에 도움이 되도록 Azure Security Center에서 권장 사항을 사용하는 방법을 알아봅니다.