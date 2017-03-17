---
title: "Azure Security Center에서 OS 취약성 해결 | Microsoft Docs"
description: "이 문서에서는 Azure Security Center 권장 사항 **OS 취약성 해결**을 구현하는 방법을 보여 줍니다."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 7680aafcefbe6f1e6e3b55137da2e46b42df5a9b
ms.lasthandoff: 03/04/2017


---
# <a name="remediate-os-vulnerabilities-in-azure-security-center"></a>Azure 보안 센터에서 OS 취약성 해결
Azure 보안 센터는 매일 가상 컴퓨터(VM) 운영 체제(OS)를 분석하여 VM을 공격에 더 취약하게 만들 수 있는 구성을 찾고 이러한 취약성을 해결하기 위한 구성 변경을 권장합니다. Security Center는 VM의 OS 구성이 권장 구성 규칙과 일치하지 않는 경우 취약성 해결을 권장합니다.

> [!NOTE]
> 모니터링되는 특정 구성에 대한 자세한 내용은 [권장 구성 규칙 목록](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) 을 참조하세요. 현재는 Windows Server 2016이 완전히 지원되지 않습니다.
>
>

## <a name="implement-the-recommendation"></a>권장 사항 구현

> [!NOTE]
> 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다.  단계별 가이드는 아닙니다.
>
>

1. **권장 사항** 블레이드에서 **OS 취약성 해결**을 선택합니다.
   ![OS 취약성 해결][1]

    **OS 취약성 해결** 블레이드가 열리고 권장 구성 규칙과 일치하지 않는 OS 구성이 있는 VM이 나열됩니다.  각 VM에 대해 블레이드에서 다음을 식별합니다.

   * **실패한 규칙** -- VM의 OS 구성이 실패한 규칙 수입니다.
   * **마지막 검사 시간** : 보안 센터가 VM을 마지막으로 검사한 날짜 및 시간입니다.
   * **상태** : 권장 사항의 현재 상태입니다.

     * 열기: 취약성이 아직 해결되지 않음
     * 진행 중: 취약성이 현재 적용되고 있으며 아무런 작업도 수행할 필요가 없음
     * 해결됨: 취약성이 이미 해결됨(문제가 해결되면 항목이 회색으로 표시됨)
   * **심각도** - 모든 취약성이 낮음 심각도로 설정됩니다. 즉, 취약성을 해결해야 하지만 즉시 주의할 필요는 없습니다.

2. VM을 선택합니다. 해당 VM에 대한 블레이드가 열리고 실패한 규칙이 표시됩니다.
   ![실패한 구성 규칙][2]

3. 규칙을 선택합니다. 이 예제에서는 **암호는 복잡성 조건을 만족해야 함**을 선택합니다. 실패한 규칙 및 영향을 설명하는 블레이드가 열립니다. 세부 정보를 검토하고 운영 체제 구성을 적용할 방법을 생각합니다.
  ![실패한 규칙에 대한 설명][3]

  보안 센터는 일반 구성 열거형(CCE)을 사용하여 구성 규칙에 대한 고유 ID를 할당합니다. 이 블레이드에 다음과 같은 정보가 제공됩니다.

  - 이름 -- 규칙의 이름
  - 심각도 -- CCE 심각도 값, 즉 요주의, 중요 또는 경고
  - CCIED -- 규칙에 대한 CCE 고유 ID
  - 설명 -- 규칙에 대한 설명
  - 취약성 -- 규칙이 적용되지 않은 경우 취약성 또는 위험에 대한 설명
  - 영향 -- 규칙이 적용될 때 비즈니스 영향
  - 예상 값 -- 보안 센터가 규칙을 기준으로 VM OS 구성을 분석할 때 예상되는 값
  - 규칙 작업 -- 규칙을 기준으로 VM OS 구성을 분석하는 동안 보안 센터가 사용하는 규칙 작업
  - 실제 값 -- 규칙을 기준으로 VM OS 구성을 분석한 후 반환되는 값
  - 평가 결과 –- 분석 결과: 통과, 실패

## <a name="see-also"></a>참고 항목
이 문서에서는 보안 센터 권장 사항 "OS 취약성 해결"을 구현하는 방법을 보여 주었습니다. [여기](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)서 구성 규칙 집합을 검토할 수 있습니다. 보안 센터는 일반 구성 열거형(CCE)을 사용하여 구성 규칙에 대한 고유 ID를 할당합니다. 자세한 내용은 [CCE](http://cce.mitre.org) 사이트를 방문하세요.

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md) -- Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure 보안 센터에서 보안 권장 사항 관리](security-center-recommendations.md) -- 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
* [Azure 보안 센터에서 보안 상태 모니터링](security-center-monitoring.md) –- Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) -- 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure 보안 센터를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) -- 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure 보안 센터 FAQ](security-center-faq.md) -- 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) -- Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/recommendation.png
[2]:./media/security-center-remediate-os-vulnerabilities/vm-remediate-os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png

