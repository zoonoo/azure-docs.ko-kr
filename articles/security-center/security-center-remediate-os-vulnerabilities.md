---
title: "Azure Security Center에서 OS 취약성 해결 | Microsoft Docs"
description: "이 문서에서는 Azure 보안 센터 권장 사항 **OS 취약성 해결**을 구현하는 방법을 보여 줍니다."
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
ms.date: 09/11/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 39879c22278a55f841e294cda5a89bec2bdf6988
ms.contentlocale: ko-kr
ms.lasthandoff: 09/13/2017

---
# <a name="remediate-os-vulnerabilities-in-azure-security-center"></a>Azure 보안 센터에서 OS 취약성 해결
Azure Security Center에서는 VM(Virtual Machines) 및 컴퓨터의 OS(운영 체제)를 매일 분석하여 이러한 VM과 컴퓨터를 공격에 보다 취약하게 만들 수 있는 구성을 확인합니다. Security Center는 OS 구성이 권장 구성 규칙과 일치하지 않는 경우 취약성을 해결하는 것이 좋으며, 이러한 취약성 해결을 위해 구성을 변경하는 것이 좋습니다.

> [!NOTE]
> 모니터링되는 특정 구성에 대한 자세한 내용은 [권장 구성 규칙 목록](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) 을 참조하세요.
>
>

## <a name="implement-the-recommendation"></a>권장 사항 구현
OS 취약성 수정은 Security Center에서 권장 사항으로 표시됩니다. 이 권장 사항은 **권장 사항** 및 **계산** 아래에 표시됩니다.

이 예제에서는 **계산** 아래의 **OS 취약점을 개선(Microsoft가 제공)** 권장 사항을 살펴보겠습니다.
1. Security Center 주 메뉴 아래에서 **계산**을 선택합니다.

   ![OS 취약성 해결][1]

2. **계산** 아래에서 **OS 취약점을 개선(Microsoft가 제공)**을 선택합니다. **OS 취약성(Microsoft 제공) 불일치** 대시보드가 열립니다.

   ![OS 취약성 해결][2]

  대시보드 위쪽에는 다음 정보가 제공됩니다.

  - 전체 VM과 컴퓨터에서 OS 구성이 실패한 심각도별 규칙의 총 수
  - 전체 VM과 컴퓨터에서 OS 구성이 실패한 유형별 규칙의 총 수
  - Windows OS 구성 및 Linux OS 구성별로 실패한 규칙의 총 수

  대시보드 아래쪽에는 VM과 컴퓨터 전체에서 실패한 모든 규칙과 누락된 업데이트의 심각도가 나열됩니다. 이 목록에는 다음과 같은 정보가 포함됩니다.

  - **CCEID**: 규칙에 대한 CCE 고유 식별자입니다. 보안 센터는 일반 구성 열거형(CCE)을 사용하여 구성 규칙에 대한 고유 ID를 할당합니다.
  - **이름**: 실패한 규칙의 이름입니다.
  - **규칙 유형**: 레지스트리 키, 보안 정책 또는 감사 정책입니다.
  - ** VM 및 컴퓨터 수**: 실패한 규칙이 적용되는 VM 및 컴퓨터의 총 수입니다.
  - **규칙 심각도**: CCE 심각도 값(위험, 중요, 경고)입니다.
  - **상태**: 권장 사항의 현재 상태입니다.

    - **열기**: 권장 사항이 아직 해결되지 않았습니다.
    - **진행 중**: 현재 권장 사항이 해당 리소스에 적용되고 있으며 아무런 작업도 수행할 필요가 없습니다.
    - **해결됨**: 권장 사항이 이미 완료되었습니다. 문제가 해결되었으면 해당 항목이 회색으로 표시됩니다.

3. 목록에서 실패한 규칙을 선택하면 세부 정보를 확인할 수 있습니다.

   ![실패한 구성 규칙][3]

  이 블레이드에 다음과 같은 정보가 제공됩니다.

  - 이름 -- 규칙의 이름
  - CCIED -- 규칙에 대한 CCE 고유 ID
  - OS 버전 -- VM 또는 컴퓨터의 OS 버전
  - 규칙 심각도 -- CCE 심각도 값(위험, 중요, 경고)
  - 전체 설명 -- 규칙의 설명
  - 취약성 -- 규칙이 적용되지 않은 경우 취약성 또는 위험에 대한 설명
  - 잠재적 영향 -- 규칙이 적용될 때의 비즈니스 영향
  - 대책 – 수정 단계
  - 예상 값 -- 보안 센터가 규칙을 기준으로 VM OS 구성을 분석할 때 예상되는 값
  - 실제 값 -- 규칙을 기준으로 VM OS 구성을 분석한 후 반환되는 값
  - 규칙 작업 -- 규칙을 기준으로 VM OS 구성을 분석하는 동안 보안 센터가 사용하는 규칙 작업

4. 위쪽 리본에서 **검색** 아이콘을 선택합니다. 검색이 열리고 선택한 OS 취약성이 있는 VM과 컴퓨터가 포함된 작업 영역이 나열됩니다. 선택한 규칙이 서로 다른 작업 영역에 연결된 여러 VM에 적용되는 경우에만 이 작업 영역 선택 블레이드가 표시됩니다.

  ![나열된 작업 영역][4]

5. 작업 영역을 선택합니다. OS 취약성이 있는 작업 영역이 표시되도록 필터링된 Log Analytics 검색 쿼리가 열립니다.

  ![OS 취약성이 있는 작업 영역][5]

6. 목록에서 컴퓨터를 선택하면 자세한 내용을 확인할 수 있습니다. 해당 컴퓨터와 관련된 정보만 표시되도록 필터링된 다른 검색 결과가 열립니다.

  ![해당 컴퓨터에 대한 정보가 표시되도록 필터링된 검색 결과][6]

## <a name="next-steps"></a>다음 단계
이 문서에서는 보안 센터 권장 사항 "OS 취약성 해결"을 구현하는 방법을 보여 주었습니다. [여기](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)서 구성 규칙 집합을 검토할 수 있습니다. 보안 센터는 일반 구성 열거형(CCE)을 사용하여 구성 규칙에 대한 고유 ID를 할당합니다. 자세한 내용은 [CCE](https://nvd.nist.gov/cce/index.cfm) 사이트를 방문하세요.

Security Center에 대해 알아보려면 다음 리소스를 참조하세요.

* [Azure Security Center에서 지원되는 플랫폼](security-center-os-coverage.md) - 지원되는 Windows 및 Linux VM의 목록을 제공합니다.
* [Azure Security Center에서 보안 정책 설정](security-center-policies.md) - Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md) - 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md) - Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) - 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) - Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png

