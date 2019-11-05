---
title: Azure Security Center 규정 준수 대시보드에서 동적 규제 준수 모니터링으로 업데이트 하는 방법 | Microsoft Docs
description: 규정 준수 패키지 업데이트 (미리 보기)
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
ms.openlocfilehash: f76b5443d6c1e3fd2cebf87cba39ba0a6bbdeaee
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521788"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard-preview"></a>규정 준수 대시보드의 동적 준수 패키지 업데이트 (미리 보기)

Azure Security Center는 리소스의 구성을 업계 표준, 규정 및 벤치 마크의 요구 사항과 지속적으로 비교 합니다. **규정 준수 대시보드** 는 특정 준수 제어 및 요구 사항을 충족 하는 방법에 따라 규정 준수 상태에 대 한 통찰력을 제공 합니다.

규정 준수 상태를 추적할 수 있는 표준 하나는 [AZURE cis 1.1.0](https://www.cisecurity.org/benchmark/azure/) (보다 공식적이 고 "Ci Microsoft Azure 기초 벤치 마크 버전 1.1.0")입니다. 

규정 준수 대시보드에 처음 표시 되는 Azure CI 표현은 Security Center에 포함 된 정적 규칙 집합을 사용 합니다.

**동적 준수 패키지 (미리 보기)** 기능을 사용 Security Center 하면 시간에 따라 업계 표준의 적용 범위가 자동으로 향상 됩니다. 호환성 패키지는 기본적으로 Azure Policy에 정의 된 이니셔티브입니다. 선택한 범위 (구독, 관리 그룹 등)에 할당할 수 있습니다. 대시보드에서 평가로 매핑된 준수 데이터를 확인 하려면 보안 정책 내에서 관리 그룹 또는 구독에 규정 준수 패키지를 추가 합니다. 규정 준수 패키지를 추가 하면 선택한 범위에 규정 준수 이니셔티브를 효과적으로 할당 합니다. 이러한 방식으로 새로 게시 된 규정 이니셔티브를 대시보드의 규정 준수 표준으로 추적할 수 있습니다. Microsoft에서 이니셔티브 (표준의 더 많은 컨트롤에 매핑되는 새 정책)에 대해 새 콘텐츠를 릴리스할 때 추가 콘텐츠가 대시보드에 자동으로 표시 됩니다.

Azure cis 벤치 마크에 대 한 동적 준수 패키지 인 **AZURE cis 1.1.0 (신규)** 는 다음을 수행 하 여 원래 *정적* 버전을 개선 합니다.

* 추가 정책 포함
* 새 검사가 추가 됨에 따라 자동으로 업데이트 

아래 설명 된 대로 새 동적 패키지로 업데이트 합니다.

## <a name="adding-a-dynamic-compliance-package"></a>동적 준수 패키지 추가

다음 단계에서는 Azure CIS 벤치 마크 v 1.1.0를 사용 하 여 호환성을 모니터링 하기 위해 동적 패키지를 추가 하는 방법을 설명 합니다.   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Azure CIS 1.1.0 (새로운) 동적 준수 패키지 업데이트 

1. **보안 정책** 페이지를 엽니다. 이 페이지에는 관리 그룹, 구독, 작업 영역 및 관리 그룹 구조의 수가 표시 됩니다.

1. 규정 준수 상태를 관리 하려는 구독 또는 관리 그룹을 선택 합니다. 규정 준수 데이터가 모든 중첩 된 리소스에 대해 집계 되 고 추적 되도록 표준에 적용할 수 있는 가장 높은 범위를 선택 하는 것이 좋습니다. 

1. 업계 & 규제 표준 (미리 보기) 섹션에서 새 콘텐츠에 대 한 Azure CIS 1.1.0를 업데이트할 수 있는 것을 볼 수 있습니다. **지금 업데이트**를 클릭 합니다. 

1. 필요에 따라 **표준 추가** 를 클릭 하 여 **규제 준수 표준 추가** 페이지를 엽니다. 여기에서 **NIST SP 800-53**4, **SWIFT CSP CSCF-v2020**, **UKO, 영국 NHS**및 **캐나다 pmm**와 같은 다른 규정 준수 표준에 대해 **Azure CIS 1.1.0 (신규)** 및 동적 패키지를 수동으로 검색할 수 있습니다.
    
    ![Azure Security Center의 규정 준수 대시보드에 규정 패키지 추가](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. Security Center의 사이드바에서 **규제 준수** 를 선택 하 여 규제 준수 대시보드를 엽니다. 
    * 이제 Azure CIS 1.1.0 (신규)가 업계 & 규제 표준 목록에 표시 됩니다. 
    * Azure CIS 1.1.0 준수의 원래 *정적* 보기도 함께 유지 됩니다. 나중에 자동으로 제거 될 수 있습니다.

    > [!NOTE]
    > 새로 추가 된 표준이 준수 대시보드에 표시 되는 데 몇 시간 정도 걸릴 수 있습니다.


    [이전 및 새 Azure CI를 표시 하는 ![규제 준수 대시보드](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>다음 단계

이 아티클에서는 다음에 대해 알아보았습니다.

* 규정 준수 대시보드에 표시 **된 표준을** 새 *동적* 패키지로 업그레이드 하는 방법
* 규정 준수 **패키지를 추가** 하 여 추가 표준 준수를 모니터링 하는 방법 

기타 관련 자료는 다음 문서를 참조 하세요. 

- [보안 센터 규정 준수 대시보드](security-center-compliance-dashboard.md)
- [보안 정책 사용](tutorial-security-policy.md)
- [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md) -Azure Security Center에서 권장 사항을 사용 하 여 Azure 리소스를 보호 하는 방법을 알아봅니다.
- [Azure Security Center faq](security-center-faq.md) -Security Center 사용에 대 한 질문과 대답을 확인 하세요.