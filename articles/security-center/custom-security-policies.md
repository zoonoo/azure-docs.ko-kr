---
title: Azure 보안 센터에서 사용자 지정 보안 정책 만들기 | 마이크로 소프트 문서
description: Azure 보안 센터에서 모니터링하는 Azure 사용자 지정 정책 정의입니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: memildin
ms.openlocfilehash: c709890ae6c57a001c6a0e9df4e973bd3bd24602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258263"
---
# <a name="using-custom-security-policies"></a>사용자 지정 보안 정책 사용

시스템과 환경을 보호하기 위해 Azure Security Center는 보안 권장 사항을 생성합니다. 이러한 권장 사항은 모든 고객에게 제공되는 일반 기본 보안 정책에 통합된 업계 모범 사례를 기반으로 합니다. 또한 보안 센터의 산업 및 규제 표준에 대한 지식에서 비롯될 수 있습니다.

이 기능을 사용하면 사용자 *지정* 이니셔티브를 추가할 수 있습니다. 그러면 환경이 만든 정책을 따르지 않는 경우 권장 사항을 받게 됩니다. 만드는 모든 사용자 지정 이니셔티브는 자습서에 설명된 규정 준수 대시보드의 기본 제공 이니셔티브와 함께 표시되며 [규정 준수 개선.](security-center-compliance-dashboard.md)

Azure 정책 [here](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location) 설명서에서 설명한 대로 사용자 지정 이니셔티브에 대 한 위치를 지정할 때 관리 그룹 또는 구독 이어야 합니다. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>구독에 사용자 지정 이니셔티브를 추가하려면 

1. 보안 센터의 사이드바에서 보안 **정책** 페이지를 엽니다.

1. 사용자 지정 이니셔티브를 추가할 구독 또는 관리 그룹을 선택합니다.

    [![사용자 지정 정책을 만들 구독 선택](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > 보안 센터에서 사용자 지정 표준을 평가하고 표시하려면 구독 수준(또는 그 이상)에 사용자 지정 표준을 추가해야 합니다. 
    >
    > 사용자 지정 표준을 추가하면 해당 범위에 *이니셔티브가* 할당됩니다. 따라서 해당 할당에 필요한 가장 넓은 범위를 선택하는 것이 좋습니다.

1. 보안 정책 페이지에서 사용자 지정 이니셔티브에서 **사용자 지정 이니셔티브 추가를**클릭합니다.

    [![**사용자 지정 이니셔티브 추가**](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    다음 페이지가 나타납니다.

    ![정책 만들기 또는 추가](media/custom-security-policies/create-or-add-custom-policy.png)

1. 사용자 지정 이니셔티브 추가 페이지에서 조직에서 이미 만든 사용자 지정 정책 목록을 검토합니다. 구독에 할당할 멤버가 표시되면 에 **추가를**클릭합니다. 목록에 요구 사항을 충족하는 이니셔티브가 없는 경우 이 단계를 건너뜁니다.

1. 새 사용자 지정 이니셔티브를 만들려면 다음을 수행합니다.

    1. **새 .**
    1. 정의의 위치와 이름을 입력합니다.
    1. 포함할 정책을 선택하고 **추가**를 클릭합니다.
    1. 원하는 매개 변수를 입력합니다.
    1. **저장**을 클릭합니다.
    1. 맞춤 이니셔티브 추가 페이지에서 새로 고침을 클릭하면 새 이니셔티브가 사용 가능한 것으로 표시됩니다.
    1. **추가를** 클릭하고 구독에 할당합니다.

    > [!NOTE]
    > 새 이니셔티브를 만들려면 구독 소유자 자격 증명이 필요합니다. Azure 역할에 대한 자세한 내용은 [Azure 보안 센터의 사용 권한을](security-center-permissions.md)참조하십시오.

    새 이니셔티브가 적용되며 다음과 같은 두 가지 방법으로 영향을 확인할 수 있습니다.

    * 보안 센터 사이드바에서 정책 & 규정 준수에서 **규정 준수를 선택합니다.** 규정 준수 대시보드가 열리면 기본 제공 이니셔티브와 함께 새로운 사용자 지정 이니셔티브를 보여 줍니다.
    
    * 환경이 정의한 정책을 따르지 않는 경우 권장 사항을 받기 시작합니다.

1. 정책에 대한 결과 권장 사항을 보려면 사이드바에서 **권장 사항을** 클릭하여 권장 사항 페이지를 엽니다. 권장 사항은 "사용자 지정" 레이블과 함께 표시되며 약 1시간 이내에 사용할 수 있습니다.

    [![사용자 지정 권장 사항](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>다음 단계

이 문서에서는 사용자 지정 보안 정책을 만드는 방법을 배웠습니다. 

기타 관련 자료는 다음 문서를 참조하십시오. 

- [보안 정책 개요](tutorial-security-policy.md)
- [기본 제공 보안 정책 목록](security-center-policy-definitions.md)